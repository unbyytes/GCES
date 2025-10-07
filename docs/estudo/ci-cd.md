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


## 3. O Arquivo exemplo `.gitlab-ci.yml`

Esse arquivo **controla todo o pipeline**. Ele é criado na **raiz do repositório** e descreve as etapas (stages), tarefas (jobs), imagens e comandos.
O arquivo  abaixo é um exemplo completo de .yml, é controlador de todo o pipeline do sistema. Sendo cirado na **raíz do repositório**, o .yml descreve as etapas (stages), tarefas (jobs), imagens e comandos necessários para construir, testar e implantar a aplicação da EJ Plataform, com backend em Python (Flask) e frontend em React, além de fazer deploy em ambientes de homologação (staging) e produção. Esse arquivo exemplo está adaptado para a aplicação ao qual o projeto de GCES está vinculado. Para acessar o arquivo original, segue o link do arquivo criado [exemplo_git_lab.yml](/GCES/docs/estudo/exemplo_git_lab.yml)

###  Estrutura Básica
```yaml
stages:
  - setup
  - test
  - build
  - deploy_staging
  - deploy_production

# Define um cache global para acelerar a instalação de dependências.
# A chave "default" garante que o cache seja compartilhado entre todas as branches.
cache:
  key:
    files:
      - backend/requirements.txt
      - frontend/package-lock.json
  paths:
    - .cache/pip
    - frontend/node_modules/
  policy: pull-push

# =================================================================
# STAGE: SETUP
# =================================================================

install_dependencies:
  stage: setup
  image: node:18-alpine # Imagem leve com Node.js e Python pré-instalados
  script:
    - echo "Instalando dependências do Backend..."
    - pip install --cache-dir .cache/pip -r backend/requirements.txt
    - echo "Instalando dependências do Frontend..."
    - cd frontend && npm install
  # Este job atualiza o cache, os demais jobs (policy: pull) apenas o consomem.
  cache:
    <<: *cache # Reutiliza a definição de cache global
    policy: pull-push

# =================================================================
# STAGE: TEST
# =================================================================

backend_test:
  stage: test
  image: python:3.9-slim
  needs: [install_dependencies] # Garante que as dependências já foram instaladas
  script:
    - echo "Executando testes do Backend com Pytest..."
    - pip install --cache-dir .cache/pip -r backend/requirements.txt
    - cd backend
    - pytest --junitxml=report.xml # Gera um relatório de testes
  artifacts:
    when: always # Salva o artefato mesmo se o job falhar
    paths:
      - backend/report.xml
    expire_in: 1 week # O relatório ficará disponível por uma semana

frontend_lint_test:
  stage: test
  image: node:18-alpine
  needs: [install_dependencies]
  script:
    - echo "Executando linter e testes do Frontend..."
    - cd frontend
    - npm test
  # Opcional: se o seu teste gera um relatório de cobertura, salve-o como artefato.
  # artifacts:
  #   paths:
  #     - frontend/coverage/

# =================================================================
# STAGE: BUILD
# =================================================================

build_docker_images:
  stage: build
  image: docker:20.10.16
  services:
    - docker:20.10.16-dind # Docker-in-Docker para construir imagens
  needs: [backend_test, frontend_lint_test] # Só constrói se os testes passarem
  before_script:
    # $CI_REGISTRY_USER, $CI_REGISTRY_PASSWORD e $CI_REGISTRY são variáveis pré-definidas pelo GitLab
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
  script:
    - echo "Construindo e enviando a imagem do Backend..."
    # Usa a tag do commit para versionar a imagem
    - docker build -t "$CI_REGISTRY_IMAGE/backend:$CI_COMMIT_SHORT_SHA" ./backend
    - docker push "$CI_REGISTRY_IMAGE/backend:$CI_COMMIT_SHORT_SHA"

    - echo "Construindo e enviando a imagem do Frontend..."
    - docker build -t "$CI_REGISTRY_IMAGE/frontend:$CI_COMMIT_SHORT_SHA" ./frontend
    - docker push "$CI_REGISTRY_IMAGE/frontend:$CI_COMMIT_SHORT_SHA"
  rules:
    # Este job só executa na branch 'main' ou 'develop'
    - if: '$CI_COMMIT_BRANCH == "main" || $CI_COMMIT_BRANCH == "develop"'

# =================================================================
# STAGE: DEPLOY
# =================================================================

deploy_to_staging:
  stage: deploy_staging
  image: alpine:latest # Imagem mínima, pois só usaremos SSH
  needs: [build_docker_images]
  before_script:
    - 'which ssh-agent || ( apk add --update openssh )'
    - eval $(ssh-agent -s)
    # Adiciona a chave SSH (armazenada em uma variável de ambiente) ao agente
    - echo "$STAGING_SSH_PRIVATE_KEY" | ssh-add -
    - mkdir -p ~/.ssh
    - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'
  script:
    - echo "Fazendo deploy no ambiente de Staging (Homologação)..."
    - >
      ssh $STAGING_SSH_USER@$STAGING_SERVER_IP "
      cd /home/user/ej-platform &&
      export BACKEND_IMAGE=$CI_REGISTRY_IMAGE/backend:$CI_COMMIT_SHORT_SHA &&
      export FRONTEND_IMAGE=$CI_REGISTRY_IMAGE/frontend:$CI_COMMIT_SHORT_SHA &&
      docker-compose pull &&
      docker-compose up -d --force-recreate
      "
  environment:
    name: staging
    url: http://staging.ejplatform.org # URL do ambiente para acesso rápido no GitLab
  rules:
    # Este job só executa na branch 'develop'
    - if: '$CI_COMMIT_BRANCH == "develop"'

deploy_to_production:
  stage: deploy_production
  image: alpine:latest
  needs: [build_docker_images]
  before_script:
    - 'which ssh-agent || ( apk add --update openssh )'
    - eval $(ssh-agent -s)
    - echo "$PROD_SSH_PRIVATE_KEY" | ssh-add -
    - mkdir -p ~/.ssh
    - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'
  script:
    - echo "Fazendo deploy no ambiente de Produção..."
    - >
      ssh $PROD_SSH_USER@$PROD_SERVER_IP "
      cd /home/user/ej-platform &&
      export BACKEND_IMAGE=$CI_REGISTRY_IMAGE/backend:$CI_COMMIT_SHORT_SHA &&
      export FRONTEND_IMAGE=$CI_REGISTRY_IMAGE/frontend:$CI_COMMIT_SHORT_SHA &&
      docker-compose pull &&
      docker-compose up -d --force-recreate
      "
  environment:
    name: production
    url: https://app.ejplatform.org
  rules:
    # Este job só executa na branch 'main' e PRECISA de aprovação manual para rodar.
    - if: '$CI_COMMIT_BRANCH == "main"'
      when: manual # Ponto-chave para segurança em produção!
```