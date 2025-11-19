# Ferramentas de Segurança do Pipeline

Este estudo reúne uma visão simples, direta e prática das ferramentas de segurança utilizadas no pipeline do projeto: Gitleaks, pip-audit, Semgrep e Trivy FS Scan.
Essas ferramentas têm o objetivo de identificar segredos expostos, falhas em dependências, problemas de código e vulnerabilidades no sistema de arquivos, ajudando o Blue Team a prevenir riscos antes do deploy.

A seguir, descrevo cada ferramenta, acompanhada de um exemplo real detectado no projeto e orientações sobre como interpretar corretamente cada alerta.



## 1. Gitleaks

Ferramenta que verifica o repositório em busca de segredos expostos, como chaves privadas, tokens, senhas e credenciais acidentalmente versionadas.

**Exemplo Real (do relatório):**

```
Secret:      -----BEGIN PRIVATE KEY-----\nMIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQDCRSkPuWTLV5Ec...
RuleID:      private-key
Entropy:     5.985962
File:        gitleaks-report-sebastian.json
Line:        89
Commit:      b1121bd22de59bb8b48a5b7eaac2766156e9d747
Author:      sebazac332
Email:       seb55381@hotmail.com
Date:        2025-11-18T03:54:37Z
Fingerprint: b1121bd22de59bb8b48a5b7eaac2766156e9d747:gitleaks-report-sebastian.json:private-key:89
Link:        https://gitlab.com/sebazac332/ej-application-sebastian/blob/b1121bd22de59bb8b48a5b7eaac2766156e9d747/gitleaks-report-sebastian.json#L89

```
## Interpretação:
O Gitleaks identificou um padrão de private key. Mesmo que seja parte de um relatório, o padrão é considerado sensível. Cabe ao analista confirmar se o segredo é real ou apenas um arquivo gerado para teste.


# pip-audit

Ferramenta que identifica vulnerabilidades em bibliotecas Python, indicando CVEs, versões corrigidas e impacto da falha.

**Exemplo Real (do relatório):**

```
{
  "name": "djangorestframework",
  "version": "3.14.0",
  "vulns": [
    {
      "id": "GHSA-gw84-84pc-xp82",
      "fix_versions": ["3.15.2"],
      "aliases": ["CVE-2024-21520"],
      "description": "Versions of the package djangorestframework before 3.15.2 are vulnerable to Cross-site..."
    }
  ]
}
```

## Interpretação:
A versão instalada do djangorestframework é vulnerável ao CVE-2024-21520, relacionado a XSS. A ferramenta sugere atualização para a versão 3.15.2 para mitigar o risco.

# Semgrep

Ferramenta de análise estática que encontra problemas de segurança no código fonte (XSS, SQL Injection, má validação, práticas inseguras etc.).

**Exemplo Real (do relatório):**

```
Uploading artifacts...
semgrep-report.json: found 1 matching artifact files and directories
Job succeeded
```
## Interpretação:
O Semgrep detectou ao menos 1 achado e gerou o arquivo semgrep-report.json. Cabe ao analista abrir o arquivo e verificar a regra ativada e o trecho de código afetado.

## Trivy FS Scan

Analisa o sistema de arquivos do projeto, detectando vulnerabilidades em pacotes, arquivos e bibliotecas utilizadas no ambiente.

**Exemplo Real (do relatório):**

```
gunicorn – CVE-2024-1135  
Installed: 19.10.0  
Fixed:     22.0.0  
Description: HTTP Request Smuggling due to improper validation of Transfer-Encoding headers
Link: https://avd.aquasec.com/nvd/cve-2024-1135
```
## Interpretação:
Uma vulnerabilidade crítica em gunicorn que permite Request Smuggling. Atualizar para 22.0.0 elimina o risco.


## Fontes Oficiais para Verificar Vulnerabilidades e Erros

Use estes recursos para validar se um achado é real. Cada link tem uma breve descrição para facilitar a triagem.

## Vulnerabilidades (CVE, GHSA, CVSS)
- [NVD – National Vulnerability Database](https://nvd.nist.gov)
Base oficial de CVEs e métricas CVSS.
- [CVE Details](https://www.cvedetails.com) 
Consulta por pacote/CVE com histórico.
- [GitHub Security Advisory](https://github.com/advisories)
Advisories e correções no ecossistema GitHub.

## Dependências Python
- [PyPA Advisory Database](https://github.com/pypa/advisory-db) 
 Base de avisos para pacotes Python.
- [PyPI package security pages](https://pypi.org)
Páginas de pacotes e versões.

## Regras e vulnerabilidades de código (Semgrep)
- [Base oficial de regras Semgrep](https://semgrep.dev/r)
Repositório de regras públicas e exemplos.

## Vulnerabilidades detectadas pelo Trivy
- [AVD – Aqua Vulnerability Database](https://avd.aquasec.com)
Banco de dados de vulnerabilidades integrável ao Trivy.

## Segredos / padrões do Gitleaks
- [Regras oficiais do Gitleaks](https://github.com/gitleaks/gitleaks) 
Padrões e exemplos de regras para detecção de segredos.