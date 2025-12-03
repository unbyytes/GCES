# MITRE ATT&CK — Relatório de XSS Persistente (Stored XSS)

## MITRE ATT&CK — Táticas & Técnicas

### 1. Tática: Initial Access (TA0001)

#### Técnica: Exploit Public-Facing Application (T1190)

O atacante utiliza o endpoint de criação/edição de conversas para inserir payload malicioso no campo Rich Text sem sanitização no backend.

### 2. Tática: Execution (TA0002)

#### Técnica: User Execution – Malicious Script (T1204.002)

O navegador da vítima executa automaticamente o script injetado quando a página *Welcome* é carregada devido ao uso de `| safe` no Jinja2.

### 3. Tática: Persistence (TA0003)

#### Técnica: Server-Side Injection (XSS Stored — variante T1055)

O payload HTML/JS é armazenado permanentemente no banco de dados e executado para qualquer usuário que acesse a página.

### 4. Tática: Defense Evasion (TA0005)

#### Técnica: Trusted Relationship (T1199)

A confiança do backend no filtro de sanitização do frontend permite que o atacante evite controles de segurança enviando requisições diretas (cURL/Postman).

#### Técnica: Obfuscated/Unescaped Input (T1027)

Ausência de sanitização no servidor permite que o payload seja inserido sem codificação ou neutralização.

### 5. Tática: Credential Access (TA0006)

#### Técnica: Credential Access via Web Session Cookie (T1539)

O XSS permitiria o roubo de cookies de sessão da vítima.

### 6. Tática: Collection (TA0009)

#### Técnica: Input Capture — Keylogging (T1056.001)

Um payload mais avançado poderia registrar entradas do usuário (ex.: credenciais, respostas da pesquisa).

### 7. Tática: Impact (TA0040)

#### Técnica: Defacement / Content Alteration (T1491.001)

O atacante pode alterar visualmente a página da conversa via código injetado.

#### Técnica: Redirect (T1185)

Possibilidade de redirecionar usuários para sites externos maliciosos.

---

## MITRE ATT&CK — Mitigações

### 1. Mitigation: Input Validation (M1014)

Implementar sanitização no backend usando biblioteca como `nh3` com allow-list de tags e atributos seguros.

### 2. Mitigation: Restrict Web-Based Content (M1021)

Remover `| safe` dos templates ou garantir que apenas conteúdo sanitizado chegue ao template.

### 3. Mitigation: User Training (M1017)

Checklist interno para revisões de segurança envolvendo campos Rich Text e motores de template.

---

## MITRE ATT&CK — Detecção

### 1. Detection: Application Log Monitoring (D1015)

Monitorar requisições POST incomuns contendo HTML ou JavaScript em campos Rich Text.

### 2. Detection: Script Execution Monitoring (D1039)

Detectar scripts inesperados executados no navegador durante o fluxo normal da aplicação.
