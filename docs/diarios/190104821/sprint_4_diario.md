# Diário de Bordo – Daniel Rocha Oliveira

**Disciplina:** Gestão de Configuração e Evolução de Software 

**Equipe:** OWASP (Blue Team)

**Comunidade/Projeto de Software Livre:** OWASP (ej-application)

---

## Sprint 3 – \[22/10/2025 até 19/11/2025\]

### Resumo da Sprint

O foco desta sprint está relacionado com a mudança do estado "Planejamento" para a própria execução, onde o ambiente de **CI/CD do GitLab** foi configurado para executar verificações de segurança automáticas a cada *push* com o novo estágio `security_scan`. Dentro do estágio, foram utilizadas 4 ferramentas de segurança, sendo elas: **Gitleaks, semgrep, pip-audit e trivy** . A equipe se dividiu em duas partes, a primeira parte focou no desenvolvimento de um relatório analisando os resultados obtidos das ferramentas enquanto a segunda parte ficou responsável por incrementar o uso das ferramentas na pipeline, além de testar localmente e gerar um realatório de cada ferramenta.

### Atividades Realizadas

| Data | Atividade | Tipo (Código/Doc/Discussão/Outro) | Link/Referência | Status |
| :--- | :--- | :--- | :--- | :--- |
| 05/11 | Planejamento da sprint | Discussão | - | Concluído |
| 07/11 | Desenvolvimento local das ferramentas (No meu caso trivy e pip-audit) e incrementação da pipeline do projeto | Código | - | Concluído |
| 07/11 | Configuração do novo estágio `security_scan` no `.gitlab-ci.yml` | Código | - | Concluído |
| 10/11 | Integração dos jobs de segurança (Gitleaks, Semgrep, pip-audit, Trivy) na pipeline | Código | - | Concluído |
| 12/11 | Finalização e entrega dos relatórios para o grupo responsável analisar e desenvolver a documentação  | Outro | - | Concluído |
| 19/11 | Preenchimento da documentação da equipe e elaboração do diário | Doc | - | Concluído |

### Maiores Avanços

* Uso local das ferramentas de segurança Trivy e pip-audit para entender seu funcionamento e resultados específicos para o projeto da EJ.
* Incrementação da pipeline via fork compartilhado juntamente com o Sebastian e Ana Calorina.
* Análise dos relatórios, verificando cada vulnerabilidade encontrada no projeto da Ej.

### Maiores Dificuldades

* Utilizar localmente as ferramentas, adequando-as para o projeto EJ.
* Entender as vulnerabilidades encontradas através de cada ferramenta.

### Aprendizados

- A utilização de ferramentas de segurança no CI/CD reduziu o risco de falhas não serem identificadas no projeto.
- A análise do histórico Git é tão importante quanto a análise do código atual — principalmente no caso de segredos.
- O Trivy se mostrou essencial para cobrir áreas além do código, como IaC e configurações, reforçando o conceito de segurança em múltiplas camadas.

### Plano Pessoal para a Próxima Sprint

* [ ] Auxiliar na correção das vulnerabilidades identificadas (segredos expostos, dependências vulneráveis, ajustes no Dockerfile).
* [ ] Implementar políticas de segurança no GitLab (Ex.: proteção de branches, aprovação obrigatória após scans).
* [ ] Refinar o pipeline com condições de bloqueio (failing pipeline quando encontrar CVEs críticas ou segredos).
* [ ] Iniciar documentação de boas práticas para o repositório (padrões de commits, PRs e configuração de secrets).
---
