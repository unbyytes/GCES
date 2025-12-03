# Red Team – Biblioteca de Scripts de PoC

**Projeto:** EJ-Application  
**Equipe:** OWASP Red Team  
**Propósito:** Scripts reutilizáveis para testes de segurança e validação de PoCs  
**Última Atualização:** Dezembro 3, 2025

---

## 1. Script: Validação de HTTP Security Headers

**Arquivo:** `scripts/test_security_headers.sh`

```bash
#!/bin/bash
# Validar presença e corretude de HTTP security headers em produção e staging
# Uso: ./test_security_headers.sh <url>
# Exemplo: ./test_security_headers.sh https://www.ejplatform.org

TARGET_URL="${1:-https://www.ejplatform.org}"
HEADERS_REQUIRED=("Content-Security-Policy" "Strict-Transport-Security" "X-Content-Type-Options" "X-Frame-Options")

echo "=== HTTP Security Headers Audit ==="
echo "Target: $TARGET_URL"
echo "Date: $(date)"
echo ""

# Fazer requisição e extrair headers
RESPONSE=$(curl -s -I "$TARGET_URL")

# Validar cada header
for header in "${HEADERS_REQUIRED[@]}"; do
    if echo "$RESPONSE" | grep -q "^${header}:"; then
        VALUE=$(echo "$RESPONSE" | grep "^${header}:" | cut -d' ' -f2-)
        echo "✅ $header: $VALUE"
    else
        echo "❌ $header: NOT FOUND"
    fi
done

echo ""
echo "=== Verificação de Diretivas CSP ==="
CSP=$(echo "$RESPONSE" | grep "^Content-Security-Policy:" | cut -d' ' -f2-)

if echo "$CSP" | grep -q "default-src 'self'"; then
    echo "✅ default-src restringido"
else
    echo "⚠️  default-src pode estar permissivo"
fi

if echo "$CSP" | grep -q "script-src 'self'"; then
    echo "✅ script-src restringido"
else
    echo "⚠️  script-src pode permitir inline scripts"
fi

if echo "$CSP" | grep -q "unsafe-inline"; then
    echo "⚠️  Detectado 'unsafe-inline' (considere remover)"
fi
```

**Como Usar:**

```bash
chmod +x scripts/test_security_headers.sh

# Testar produção
./test_security_headers.sh https://www.ejplatform.org

# Testar staging
./test_security_headers.sh https://staging.ejplatform.org
```

---

## 2. Script: Teste de XSS Refletido

**Arquivo:** `scripts/test_xss_reflected.py`

```python
#!/usr/bin/env python3
"""
Teste automatizado para XSS refletido.
Realiza requisições com payloads e valida se há reflexão não escapada.
"""

import requests
import sys
from urllib.parse import urljoin

# Payloads XSS comuns
XSS_PAYLOADS = [
    '"><script>alert(1)</script>',
    '\'><script>alert(1)</script>',
    '"><img src=x onerror=alert(1)>',
    '\';alert(1);//',
    '<svg onload=alert(1)>',
]

def test_xss_endpoint(base_url, endpoint, param_name, session=None):
    """Testar XSS em um endpoint específico."""
    
    if session is None:
        session = requests.Session()
    
    target_url = urljoin(base_url, endpoint)
    vulnerabilities = []
    
    for payload in XSS_PAYLOADS:
        try:
            # Enviar payload como parâmetro
            params = {param_name: payload}
            response = session.get(target_url, params=params, timeout=5)
            
            # Verificar se payload foi refletido sem escape
            if payload in response.text and "<script>" in response.text:
                vulnerabilities.append({
                    "payload": payload,
                    "param": param_name,
                    "severity": "CRÍTICO"
                })
                print(f"⚠️  XSS Detectado: {payload[:50]}...")
            elif payload in response.text:
                print(f"✅ Payload refletido com escape (seguro)")
                
        except requests.RequestException as e:
            print(f"❌ Erro ao testar: {e}")
    
    return vulnerabilities

def main():
    if len(sys.argv) < 2:
        print("Uso: python3 test_xss_reflected.py <base_url> [endpoint] [param_name]")
        print("Exemplo: python3 test_xss_reflected.py https://www.ejplatform.org /conversations/tags/promoted search_text")
        sys.exit(1)
    
    base_url = sys.argv[1]
    endpoint = sys.argv[2] if len(sys.argv) > 2 else "/search/"
    param_name = sys.argv[3] if len(sys.argv) > 3 else "q"
    
    print(f"=== XSS Reflected Test ===")
    print(f"Target: {base_url}{endpoint}")
    print(f"Parameter: {param_name}")
    print("")
    
    # Criar sessão com cookies válidos (opcional)
    session = requests.Session()
    
    vulnerabilities = test_xss_endpoint(base_url, endpoint, param_name, session)
    
    if vulnerabilities:
        print(f"\n❌ {len(vulnerabilities)} vulnerabilidade(s) encontrada(s)")
        for vuln in vulnerabilities:
            print(f"   - {vuln}")
    else:
        print("\n✅ Nenhuma vulnerabilidade XSS refletida encontrada")

if __name__ == "__main__":
    main()
```

**Como Usar:**

```bash
chmod +x scripts/test_xss_reflected.py

# Testar endpoint de busca
python3 scripts/test_xss_reflected.py https://www.ejplatform.org /conversations/tags/promoted search_text

# Testar outro endpoint
python3 scripts/test_xss_reflected.py https://www.ejplatform.org /api/v1/search/ q
```

---

## 3. Script: Teste de SQL Injection

**Arquivo:** `scripts/test_sqli.py`

```python
#!/usr/bin/env python3
"""
Teste automatizado para SQL Injection.
Detecta padrões de erro SQL ou comportamentos anormais.
"""

import requests
import sys
from urllib.parse import urljoin

SQLI_PAYLOADS = [
    "' OR '1'='1",
    "' OR 1=1 --",
    "' UNION SELECT NULL --",
    "admin' --",
    "' OR 'a'='a",
]

def test_sqli_endpoint(base_url, endpoint, param_name):
    """Testar SQL Injection em um endpoint."""
    
    target_url = urljoin(base_url, endpoint)
    vulnerabilities = []
    
    # Fazer requisição baseline (sem payload)
    try:
        baseline = requests.get(target_url, timeout=5)
        baseline_length = len(baseline.text)
    except requests.RequestException:
        print("❌ Erro ao conectar no servidor")
        return vulnerabilities
    
    print(f"Baseline Response Length: {baseline_length} bytes")
    print("")
    
    for payload in SQLI_PAYLOADS:
        try:
            params = {param_name: payload}
            response = requests.get(target_url, params=params, timeout=5)
            
            # Indicadores de SQLi bem-sucedido
            sql_errors = ["SQL", "syntax", "database", "query", "mysql", "postgresql"]
            response_lower = response.text.lower()
            
            if any(error in response_lower for error in sql_errors):
                vulnerabilities.append({
                    "payload": payload,
                    "error": "SQL Error detected in response"
                })
                print(f"⚠️  SQL Error encontrado: {payload}")
                
            elif len(response.text) != baseline_length:
                print(f"⚠️  Resposta diferente (comportamento anômalo): {payload}")
            else:
                print(f"✅ Payload testado: {payload[:40]}... (sem alteração)")
                
        except requests.RequestException as e:
            print(f"❌ Erro: {e}")
    
    return vulnerabilities

def main():
    if len(sys.argv) < 2:
        print("Uso: python3 test_sqli.py <base_url> [endpoint] [param_name]")
        print("Exemplo: python3 test_sqli.py https://www.ejplatform.org /conversations/tags/promoted search_text")
        sys.exit(1)
    
    base_url = sys.argv[1]
    endpoint = sys.argv[2] if len(sys.argv) > 2 else "/search/"
    param_name = sys.argv[3] if len(sys.argv) > 3 else "q"
    
    print(f"=== SQL Injection Test ===")
    print(f"Target: {base_url}{endpoint}")
    print(f"Parameter: {param_name}")
    print("")
    
    vulnerabilities = test_sqli_endpoint(base_url, endpoint, param_name)
    
    if vulnerabilities:
        print(f"\n❌ {len(vulnerabilities)} indicador(es) de SQLi encontrado(s)")
    else:
        print("\n✅ Nenhuma vulnerabilidade SQL Injection óbvia encontrada")

if __name__ == "__main__":
    main()
```

**Como Usar:**

```bash
python3 scripts/test_sqli.py https://www.ejplatform.org /conversations/tags/promoted search_text
```

---

## 4. Script: Teste de Rate Limiting

**Arquivo:** `scripts/test_rate_limit.sh`

```bash
#!/bin/bash
# Testar proteção contra brute force/rate limiting
# Uso: ./test_rate_limit.sh <endpoint> <max_requests>

ENDPOINT="${1:-https://www.ejplatform.org/api/v1/users/login/}"
MAX_REQUESTS="${2:-10}"

echo "=== Rate Limiting Test ==="
echo "Endpoint: $ENDPOINT"
echo "Max Requests: $MAX_REQUESTS"
echo "Date: $(date)"
echo ""

STATUS_CODES=()

for i in $(seq 1 $MAX_REQUESTS); do
    RESPONSE=$(curl -s -o /dev/null -w "%{http_code}" "$ENDPOINT" -X POST -d "username=test&password=test")
    STATUS_CODES+=("$RESPONSE")
    
    echo "Request $i: HTTP $RESPONSE"
    
    # Se receber 429 (Too Many Requests), rate limit ativado
    if [ "$RESPONSE" -eq 429 ]; then
        echo "✅ Rate limiting ativado após $i requisições"
        break
    fi
    
    sleep 0.5
done

# Análise
if [[ " ${STATUS_CODES[@]} " =~ " 429 " ]]; then
    echo ""
    echo "✅ Rate limiting detectado (código 429)"
else
    echo ""
    echo "⚠️  Nenhum rate limiting 429 detectado após $MAX_REQUESTS requisições"
fi
```

**Como Usar:**

```bash
chmod +x scripts/test_rate_limit.sh
./test_rate_limit.sh https://www.ejplatform.org/api/v1/users/login/ 15
```

---

## 5. Script: Auditoria Completa (Master Script)

**Arquivo:** `scripts/run_security_audit.sh`

```bash
#!/bin/bash
# Script master: executa todos os testes de segurança em sequência
# Uso: ./run_security_audit.sh <target_url>

TARGET="${1:-https://www.ejplatform.org}"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
REPORT_FILE="security_audit_${TIMESTAMP}.txt"

echo "╔════════════════════════════════════════════════════════════╗"
echo "║     RED TEAM SECURITY AUDIT - EJ-Application               ║"
echo "║     Target: $TARGET"
echo "║     Report: $REPORT_FILE"
echo "╚════════════════════════════════════════════════════════════╝"
echo ""

{
    echo "=== AUDIT INICIADO ==="
    echo "Data: $(date)"
    echo "Alvo: $TARGET"
    echo ""
    
    echo "## 1. HTTP Security Headers"
    echo ""
    ./scripts/test_security_headers.sh "$TARGET"
    echo ""
    
    echo "## 2. XSS Reflected Test"
    echo ""
    python3 scripts/test_xss_reflected.py "$TARGET" "/conversations/tags/promoted/" "search_text"
    echo ""
    
    echo "## 3. SQL Injection Test"
    echo ""
    python3 scripts/test_sqli.py "$TARGET" "/conversations/tags/promoted/" "search_text"
    echo ""
    
    echo "## 4. Rate Limiting Test"
    echo ""
    bash scripts/test_rate_limit.sh "$TARGET/api/v1/users/login/" 10
    echo ""
    
    echo "=== AUDIT FINALIZADO ==="
    echo "Data: $(date)"
    
} | tee "$REPORT_FILE"

echo ""
echo "✅ Relatório salvo em: $REPORT_FILE"
```

**Como Usar:**

```bash
chmod +x scripts/run_security_audit.sh
./scripts/run_security_audit.sh https://www.ejplatform.org
```
