# SAST e Camadas de Defesa no Pipeline de Segurança

## O que é SAST (Static Application Security Testing)

SAST é uma família de técnicas que analisam código estático (sem executar o programa) para encontrar padrões associados a vulnerabilidades — por isso é frequentemente chamada de “análise de código-fonte” ou white-box testing. Ela costuma ser aplicada cedo no SDLC (shift-left), muitas vezes a cada commit/merge no CI, porque detectar e corrigir nessa fase é mais barato e rápido.

[🔗 Fonte oficial – OWASP](https://owasp.org/www-community/controls/Static_Code_Analysis)

---


## Como SAST funciona (em alto nível)

Ferramentas SAST empregam várias técnicas, muitas vezes combinadas:

- **Análise léxica/sintática e AST:** parseiam o código e procuram *patterns* suspeitos (p. ex., uso de `eval()` com entrada não confiável).  
- **Análise de fluxo de dados (taint analysis):** rastreiam como dados “contaminados” fluem até *sinks* perigosos (SQL, comando, template).  
- **Regras específicas por linguagem e framework:** definem práticas inseguras; podem ser customizadas.  
- **Integração com CI/CD:** execução automatizada no pipeline para dar feedback precoce ao desenvolvedor.

[Fonte – Semgrep Docs](https://semgrep.dev/docs/writing-rules/overview)

---



## O que o SAST detecta (e o que não detecta)

### Geralmente bem coberto por SAST

- Injeções (SQL, comando, template) e uso inseguro de APIs sensíveis.  
- Falhas de autenticação/autorização reconhecíveis.  
- Más práticas criptográficas (MD5, RNG fraco), *hardcoded secrets*, validação insuficiente.  
- Ausência de controles de escape/sanitização.

### Limitações típicas

- Falsos positivos (o código parece vulnerável fora de contexto) e falsos negativos (padrões não cobertos pelas regras);
- Pouca visibilidade de comportamentos em tempo de execução (ex.: configurações dinâmicas, middleware que mitiga um risco);
- Não cobre vulnerabilidades fora do código de primeira parte (ex.: dependências vulneráveis, problemas só detectáveis em execução). Nesses casos, complementa-se com DAST/IAST e SCA (análise de dependências). 

**Resumo:** SAST vê *o que o código diz que fará*, não necessariamente *o que o sistema faz em produção*.

---

## Onde SAST se encaixa no pipeline (camadas de defesa)

Em um pipeline DevSecOps maduro, o SAST é uma das camadas de proteção:

| Camada                           | Ferramenta típica         | O que analisa                      | Tipo de risco que previne                      |
| -------------------------------- | ------------------------- | ---------------------------------- | ---------------------------------------------- |
| **Controle de código**           | **Gitleaks**, truffleHog  | Repositórios e commits             | Vazamento de segredos (tokens, senhas, chaves) |
| **Código-fonte (estático)**      | **Bandit**, **Semgrep**   | Código em repouso (sem execução)   | Vulnerabilidades lógicas e de implementação    |
| **Dependências e bibliotecas**   | **pip-audit**, **Safety** | Pacotes externos (PyPI, npm, etc.) | Uso de versões vulneráveis (CVE conhecidas)    |
| **Execução / aplicação rodando** | **DAST**, **IAST**        | Aplicação ativa (runtime)          | Falhas de autenticação, injeções, XSS, etc.    |
| **Infraestrutura e containers**  | **Trivy**, **Checkov**    | Dockerfiles, IaC, imagens          | Configurações inseguras, permissões excessivas |

[Fonte – GitLab SAST Docs](https://docs.gitlab.com/user/application_security/sast/)

---


# Bandit vs Semgrep (para Python/Django)

- [Bandit Docs](https://bandit.readthedocs.io/en/latest/)  
- [Semgrep Repository](https://github.com/semgrep/semgrep)


### **Bandit**

**Vantagens**
- Simplicidade e foco em Python; fácil integração ao pipeline.  
- Perfis e filtros por severidade/confiança.  
- Extensível via *plugins*.

**Desvantagens**
- Menor expressividade para regras complexas.  
- Falta de ruleset curado específico para Django.

---

### **Semgrep**

**Vantagens**
- Regras customizáveis em YAML.  
- Suporte a *taint analysis* (fonte → sink).  
- Ruleset oficial para Django.  
- Integração nativa com CI (`semgrep ci`, modo diff-aware).

**Desvantagens**
- Curva de aprendizado maior.  
- Requer manutenção contínua do conjunto de regras.

---

### Para um projeto Django

- Se você quer resultado rápido e cobertura básica de problemas Python: comece com Bandit como “trava de segurança” mínima na etapa de lint/security. 
- Se você precisa detectar padrões específicos de Django (ex: sanitização em views, uso de APIs perigosas no framework) e analisar fluxo de dados (entrada do usuário→ORM, render), Semgrep com o ruleset Django e taint mode oferece melhor poder expressivo. 
- Combinação pragmática (recomendada): Bandit + Semgrep — Bandit cobre o básico de Python; Semgrep cobre Django + regras do time e casos de dataflow. (Modelo de camadas; +CI dedicado do Semgrep). 

---


# Camadas de Defesa no Pipeline de Segurança

O termo Defense in Depth (Defesa em Profundidade) vem da cibersegurança clássica, especialmente dos frameworks do NIST (National Institute of Standards and Technology) e da OWASP.
No contexto de um pipeline DevSecOps, cada ferramenta cobre um vetor diferente de ataque.
Assim, se uma falha passar por uma camada, outra pode bloquear, alertar ou mitigar o risco.




| Camada                           | Ferramenta típica         | O que analisa                      | Tipo de risco que previne                      |
| -------------------------------- | ------------------------- | ---------------------------------- | ---------------------------------------------- |
| **Controle de código**           | **Gitleaks**, truffleHog  | Repositórios e commits             | Vazamento de segredos (tokens, senhas, chaves) |
| **Código-fonte (estático)**      | **Bandit**, **Semgrep**   | Código em repouso (sem execução)   | Vulnerabilidades lógicas e de implementação    |
| **Dependências e bibliotecas**   | **pip-audit**, **Safety** | Pacotes externos (PyPI, npm, etc.) | Uso de versões vulneráveis (CVE conhecidas)    |
| **Execução / aplicação rodando** | **DAST**, **IAST**        | Aplicação ativa (runtime)          | Falhas de autenticação, injeções, XSS, etc.    |
| **Infraestrutura e containers**  | **Trivy**, **Checkov**    | Dockerfiles, IaC, imagens          | Configurações inseguras, permissões excessivas |


--- 

# Gitleaks vs pip-audit

Essas duas ferramentas exemplificam camadas diferentes da defesa em profundidade.

## Camada 1 – Gitleaks (Segredos e Credenciais)

O que é:
Ferramenta de secret scanning — busca padrões de segredos em commits, branches e históricos Git.
Detecta credenciais expostas antes de o código ir para o repositório remoto.

## Risco prevenido:

- Vazamento de tokens de API, chaves AWS, senhas de banco, certificados privados.
- Esses segredos, se expostos, permitem acesso direto a sistemas críticos (sem explorar o código).

Exemplo teórico:
Um desenvolvedor acidentalmente faz commit de uma variável:

```
AWS_SECRET_KEY = "ABCD1234..."
```
Mesmo que o código seja seguro em termos de lógica, o segredo é uma porta de entrada imediata para invasões.


# Pip-audit (Dependências Vulneráveis)

O que é:
Ferramenta da Python Packaging Authority (PyPA) que analisa pacotes Python instalados e compara suas versões com o banco de dados público de vulnerabilidades (CVE).

Risco prevenido:
Uso de bibliotecas comprometidas ou com falhas conhecidas, mesmo que o seu código esteja correto.
Evita vulnerabilidades do tipo “Vulnerable and Outdated Components” (OWASP A06).

Exemplo teórico:
Seu projeto Django usa:

```
requests==2.19.1
```

Essa versão tem uma falha (CVE-2018-18074) que permite leak de autenticação via redirecionamento HTTP.

# OWASP Top 10 x Ferramentas

| OWASP Categoria                     | Bandit | Semgrep | Gitleaks | pip-audit |
| ----------------------------------- | -------| ------- | -------- | --------- |
| A01 – Broken Access Control         | 🟡     | 🟢     | ⚪       | ⚪       |
| A02 – Cryptographic Failures        | 🟢     | 🟡     | 🟢       | ⚪       |
| A03 – Injection                     | 🟢     | 🟢     | ⚪       | ⚪       |
| A04 – Insecure Design               | ⚪     | 🟡     | ⚪       | ⚪       |
| A05 – Security Misconfiguration     | 🟢     | 🟢     | ⚪       | ⚪       |
| A06 – Vulnerable Components         | ⚪     | ⚪     | ⚪       | 🟢       |
| A07 – Identification/Authentication | 🟢     | 🟢     | ⚪       | ⚪       |
| A08 – Software/Data Integrity       | ⚪     | ⚪     | 🟢       | 🟢       |
| A09 – Logging/Monitoring            | ⚪     | 🟡     | ⚪       | ⚪       |
| A10 – SSRF                          | 🟢     | 🟢     | ⚪       | ⚪       |
Legenda:
🟢 — Cobertura direta 🟡 — Cobertura parcial ⚪ — Fora do escopo

[Fonte – OWASP-TOP-10](https://github.com/OWASP/Top10)