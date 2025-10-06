# Estudo e Prática — CI/CD com GitLab


### Introdução

Este documento apresenta um estudo prático sobre CI/CD (Integração Contínua e Entrega Contínua) utilizando o GitLab. Aqui você encontrará os principais conceitos, benefícios, estrutura dos pipelines e exemplos de configuração, com o objetivo de facilitar a compreensão e a aplicação dessas práticas no desenvolvimento de software.

---

##  1. Conceito de CI/CD

### O que é CI/CD?
**CI/CD (Continuous Integration / Continuous Delivery)** é um conjunto de práticas que automatiza o processo de:

- **Integração Contínua (CI):** cada alteração de código é automaticamente testada e integrada ao projeto.
- **Entrega Contínua (CD):** o sistema é automaticamente preparado (e opcionalmente implantado) em ambientes de homologação ou produção.

**Leitura recomendada:**

[Introdução ao CI/CD - GitLab](https://docs.gitlab.com/ci/)

 **Benefícios principais:**

- Redução de erros humanos  
- Feedback rápido para os desenvolvedores  
- Implantação mais previsível e confiável  
- Automação de tarefas repetitivas

 **Leitura recomendada:**

- [Conceitos de DevOps e Pipelines](https://docs.gitlab.com/ci/pipelines/)

---

## 2. Estrutura e Conceitos-Chave do GitLab CI/CD

| Conceito | Descrição |
|-----------|------------|
| **Pipeline** | Conjunto de tarefas automatizadas (build, test, deploy). |
| **Stage** | Etapa do pipeline (ex: “test”, “build”, “deploy”). |
| **Job** | Uma tarefa específica executada dentro de uma stage. |
| **Runner** | Máquina (container, VM, etc.) que executa os jobs. |
| **Artifact** | Arquivo gerado por um job e salvo para uso posterior. |
| **Cache** | Mecanismo de reuso de dependências (ex: pacotes Python). |
| **Variable** | Variáveis de ambiente que armazenam dados e senhas. |

**Documentação Oficial:**  

- [Arquitetura do CI/CD](https://docs.gitlab.com/ee/ci/architecture/)  
- [Configuração YAML (`.gitlab-ci.yml`)](https://docs.gitlab.com/ee/ci/yaml/)
- [Job](https://docs.gitlab.com/ci/jobs/)
- [Runner](https://docs.gitlab.com/runner/)
- [job Artifact](https://docs.gitlab.com/ci/jobs/job_artifacts/)
- [Cache](https://docs.gitlab.com/ci/caching/)
- [Variable](https://docs.gitlab.com/ci/variables/)

---
## **Resumo dos passos acima**

### **Pipeline**

Um pipeline é o fluxo automatizado de execução das etapas do seu projeto.  
Ele representa a sequência de tarefas que o GitLab executa sempre que há uma mudança no código (ex.: *commit*, *merge request* ou *tag*).

**Exemplo de pipeline simples:**

```yaml
stages:
  - test
  - build
  - deploy
```

### **Stage**

As stages são as etapas principais do pipeline, agrupando jobs com um mesmo objetivo.
Os jobs dentro de uma mesma stage rodam em paralelo, e o GitLab só passa para a próxima quando todos terminam.

```
stages:
  - test
  - build
  - deploy
```


### **Job**
Um job é uma tarefa individual dentro de uma stage.
Define os comandos, variáveis e scripts a serem executados.


Exemplo:

```
test_job:           # nome do job
  stage: test       # define a etapa
  script:           # comandos executados dentro do runner
    - pytest        # executa os testes
```



### **Runner**

O Runner é o agente executor dos jobs.
É ele que realmente executa os comandos definidos no .gitlab-ci.yml.


| Tipo                | Descrição                                               |
| ------------------- | ------------------------------------------------------- |
| **Shared Runner**   | Disponível para todos os projetos no GitLab.com         |
| **Specific Runner** | Instalado manualmente e usado por um projeto específico |
| **Group Runner**    | Compartilhado entre projetos de um mesmo grupo          |


### **Artifact**

Um artifact é um arquivo ou diretório gerado por um job e salvo pelo GitLab.
Serve para armazenar logs, relatórios e resultados de execução.

Exemplo:

```
test:
  stage: test
  script:
    - pytest --junitxml=report.xml
  artifacts:
    when: always
    paths:
      - report.xml
    expire_in: 1 week
```

### **Cache**

O cache é um mecanismo de reuso de dependências entre jobs ou pipelines.
Ele armazena arquivos temporários (como bibliotecas instaladas) para acelerar execuções futuras.

```
cache:
  paths:
    - .cache/pip
```

### **Variable**

As variables são valores configuráveis usados dentro dos jobs.
Servem para armazenar senhas, tokens, chaves de API e URLs de serviços.

Exemplo:

```
variables:
  DATABASE_URL: "postgres://user:pass@db:5432/app"

deploy:
  stage: deploy
  script:
    - echo "Conectando ao banco $DATABASE_URL"
```

###  Como tudo se conecta

1. O **pipeline** organiza as etapas principais (*stages*).  
2. Cada **stage** contém **jobs** individuais.  
3. Os **runners** executam esses jobs em ambientes isolados.  
4. **Artifacts** e **cache** guardam resultados e dependências.  
5. **Variables** controlam configurações e credenciais com segurança.


## 3. O Arquivo `.gitlab-ci.yml`

Esse arquivo **controla todo o pipeline**. Ele é criado na **raiz do repositório** e descreve as etapas (stages), tarefas (jobs), imagens e comandos.

###  Estrutura Básica
```yaml
stages:
  - test
  - build
  - deploy

test_job:
  stage: test
  script:
    - echo "Executando testes automatizados..."

build_job:
  stage: build
  script:
    - echo "Gerando build da aplicação..."

deploy_job:
  stage: deploy
  script:
    - echo "Fazendo deploy no ambiente de produção..."
```