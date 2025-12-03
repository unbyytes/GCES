---
hide:
  - navigation
---

# 📝 Relatório Final de Contribuição – Projeto OWASP (EJ-Application)

Este relatório consolida as atividades, avanços e lições aprendidas ao longo das Sprints 0 a 5 do projeto OWASP/EJ-Application, com foco em Gestão de Configuração e Evolução de Software (GCES) e DevSecOps.

---

## 1. Resumo da Evolução do Projeto por Sprint

A equipe (Blue e Red Team) trabalhou para estabelecer um ambiente de desenvolvimento seguro, atacando a aplicação e implementando defesas em forma de DevSecOps.

| Sprint | Blue Team: Estudos e Modelagem | Red Team: Ataques e Exploração | Geral (Red + Blue) | Principais Entregas | MR/Issues Destacados |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **0** (Setup) | - | - | Configuração de ambiente (`docker-up`), estudo da infraestrutura (Docker, Gunicorn) e práticas de GCES no projeto. | Ambiente Local Configurado, Documentação Inicial. | - |
| **1** (Modelagem) | Estudo de *Frameworks* de Modelagem de Ameaças (**STRIDE**, DREAD, PATH). Início da Modelagem de Riscos e levantamento de documentação. | Estudo de vetores de ataque: **SQL Injection**, **CSRF**, **XSS**, **Clickjacking**, IDOR, Cookies Inseguros e Brute Force. | | Estudo de Frameworks e Vetores de Ataque. | - |
| **2** (Ataque/DFD/CI) | Conclusão do Diagrama de Fluxo de Dados (**DFD**) e da Modelagem de Riscos (STRIDE). Estudo e *PoC* de **GitLab CI/CD**, **Gitleaks** e **Bandit**. | Exploração dos ataques estudados (SQLi, CSRF, XSS, Clickjacking, IDOR, Cookies, Brute Force). **Vulnerabilidade XSS Persistente Encontrada**. | | DFD e Análise de Ameaças Concluídos. **PoC de CI/CD** com ferramentas de segurança. | - |
| **3** (Arsenal/Phishing) | Pesquisa e Seleção do Arsenal de Segurança (*open-source*): **Gitleaks** (Secrets), **Semgrep** (SAST), **pip-audit** (SCA), **Trivy** (IaC). **Início da Implementação** no pipeline. | Simulação de **Phishing** direcionado (*Spear Phishing*). Validação de Controle de Acesso (IDOR e JWT) - **sem vulnerabilidades encontradas**. | | Arsenal DevSecOps Selecionado e Implementado Parcialmente. Abertura da **Issue pública sobre XSS**. | [Issue #1505 (XSS)](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1505) |
| **4** (DevSecOps) | **Implementação Completa do Arsenal** no pipeline (Gitleaks, Semgrep, pip-audit, Trivy). Criação do estágio `security_scan`. Documentação técnica da integração e dos achados iniciais. | Ataque de **Session Hijacking** realizado com sucesso (falha na flag `Secure` do cookie). **Correção (Fix) da vulnerabilidade XSS** identificada. | | Pipeline DevSecOps Ativo. Fix da vulnerabilidade XSS. | [MR #392 (Fix XSS)](https://gitlab.com/pencillabs/ej/ej-application/-/merge_requests/392) |
| **5** (Documentação) | - | Documentação das TTPs (Táticas, Técnicas e Procedimentos) do XSS no formato **MITRE ATT&CK**. Criação de **Guia de Contribuição Segura para Jinja2** (para prevenir XSS). | | Relatório MITRE ATT&CK. Guia de Segurança para Contribuições. | [MR #395 (Guia Jinja2)](https://gitlab.com/pencillabs/ej/ej-application/-/merge_requests/395) |

---

## 2. Indicadores de Contribuição

### PR/MR e Issues Submetidos

| Tipo | Descrição | Referência | Referência na Sprint | Aceito |
| :--- | :--- | :--- | :--- | :--- |
| **Issue** | Reporte oficial da falha de XSS (Persistente). | [Issue #1505](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1505) | Sprint 3 | - |
| **MR** | Correção (Fix) da vulnerabilidade XSS. | [MR #392](https://gitlab.com/pencillabs/ej/ej-application/-/merge_requests/392) | Sprint 4 | Ainda não |
| **MR** | Guia de Contribuição de Segurança para Jinja2. | [MR #395](https://gitlab.com/pencillabs/ej/ej-application/-/merge_requests/395) | Sprint 5 | Ainda não |
| **Issue** | Reporte da falha de Session Hijacking | [Issue #1510](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1510) | Sprint 5 | Ainda não |
| **Issue** | Criar stage de segurança na pipeline e gerar relatório de vulnerabilidades| [Issue #1507](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1507) | Sprint 5 | Ainda não |


### Participação Individual

| Membro | Matrícula | PR/MRs | Issues | Contagem Total | Aceitos |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Mateus Vieira | 221008703 | [MR #392](https://gitlab.com/pencillabs/ej/ej-application/-/merge_requests/392) + [MR #395](https://gitlab.com/pencillabs/ej/ej-application/-/merge_requests/395) = 2 MRs | [Issue #1505](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1505) = 1 Issue | 3 | 0 |
| Marcus Vinicius Cunha Dantas | 211031403 | - | [#Issue 1510](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1510) = 1 Issue | 1 | 0 |
| Júlio César Costa | 211031744 | - | [#Issue 1510](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1510) = 1 Issue | 1 | 0 |
| Daniel Rocha Oliveira | 190104821 | [MR #394](https://gitlab.com/pencillabs/ej/ej-application/-/merge_requests/394) = 1 MR | [Issue #1506](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1506) + [#Issue #1507](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1507) + [Issue #1509](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1509) = 3 Issues | 3 | 0 |
| Miguel Arthur | 211062320 |[MR #394](https://gitlab.com/pencillabs/ej/ej-application/-/merge_requests/394) = 1 MR| [#Issue 1506](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1506) + [#Issue 1507](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1507) + [#Issue 1508](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1508) + [#Issue 1509](https://gitlab.com/pencillabs/ej/ej-application/-/issues/1509) + = 4 Issue | 4 | 0 |


---

## 3. Relato das Tomadas de Decisão de Organização da Equipe

A organização da equipe seguiu uma estrutura funcional de **Blue Team** (Defesa, Modelagem, DevSecOps) e **Red Team** (Ataque, Exploração, Simulação), promovendo uma abordagem *DevSecOps* e *Security* desde o início.

Todas as tomadas de decisão relacionadas a organização do calendário e aplicação dos métodos de ataque/modelagem a equipe seguiu de forma flexível o que foi passado pelo mentor no início do semestre com o que a professora da disciplina recomendou durante as apresentações.

Dessa forma, o **Blue Team** teve esse foco em *DevSecOps* com o desenvolvimento de uma pipeline de análise de segurança sobre a aplicação e o **Red Team** concentrou os esforços em atacar a aplicação com o objetivo de encontrar vulnerabilidades que poderiam ser corrigidas.

### Comunicação, Alinhamento e Documentação

* A equipe utilizou este **GitHub Público** como principal ferramenta de gestão e alinhamento.
* Para comunicação a equipe utilizou:
    * Mensagens: Whatsapp
    * Código e Pipelines: Fork no GitLab
    * Abertura de MR: Forks individuais no GitLab

#### Observações

* **Red Team**:
    * A decisão de abrir issues públicas sobre as vulnerabilidades foi um ponto de discussão ética. A equipe entendeu que esse era o caminho correto após consultar o mentor e os líderes da comunidade, os quais reforçaram a prática devido ao projeto ser de baixa escala.

---

## 4. Relato de Eventuais Dificuldades com o Projeto

### Dificuldades Técnicas e de Ambiente

* **Mapeamento da Arquitetura (Blue Team):** A equipe Blue Team relatou a dificuldade inicial em compreender detalhadamente o funcionamento interno do software, seus fluxos de dados e as superfícies de ataque, essencial para uma modelagem de risco eficaz.
* **Configuração de Ambiente (Sprint 0/2):** Embora o uso do Docker tenha facilitado, alguns membros encontraram dificuldades em configurar o ambiente no WSL (Windows Subsystem for Linux) e gerenciar o acesso via Kali Linux para os testes de ataque.
* **Integração de Ferramentas (Sprint 3/4):** A implementação das ferramentas de segurança no arquivo `.gitlab-ci.yml` apresentou complexidade, exigindo adaptação, especialmente na integração de `pip-audit` com `Poetry`.

### Dificuldades de Conhecimento e Ataque

* **Baixa Familiaridade com Segurança (Red Team):** A equipe de ataque inicialmente teve baixa familiaridade com certos vetores de ataque, o que dificultou a compreensão e a exploração.
* **Complexidade do Código:** Contornar a sanitização de entradas da API para explorar vulnerabilidades (como XSS) foi desafiador, indicando que a aplicação já possuía um bom nível de proteção em alguns *endpoints*.
