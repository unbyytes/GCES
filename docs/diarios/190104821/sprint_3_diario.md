# Diário de Bordo – [Daniel Rocha Oliveira](https://github.com/DanRocha18)

**Disciplina:** Gerência de Configuração e Evolução de Software - T01 
**Equipe:** OWASP/Blue Team  
**Comunidade/Projeto de Software Livre:** OWASP/ej-application

---

## Sprint 0 – [07/10/2025 – 22/10/2025]

### Resumo da Sprint

Foi realizado um estudo sobre as principais ferramentas de segurança aplicadas em pipelines DevSecOps.
Foram analisadas as funcionalidades do **Bandit** e do **Semgrep** na análise estática de código (SAST), o uso do **Gitleaks** para detecção de segredos expostos, do **pip-audit** para identificação de dependências vulneráveis e do **Trivy** para inspeção de containers.
Além disso, foi abordado o conceito de **camadas de defesa**, relacionando cada ferramenta às categorias do **OWASP Top 10**, com o propósito de compreender como cada uma contribui para a prevenção de diferentes tipos de riscos.
Como atividade individual, foi executado o **Gitleaks**.

### Atividades Realizadas

| Data  | Atividade                         | Tipo (Código/Doc/Discussão/Outro) | Link/Referência | Status    |
| ----- | --------------------------------- | --------------------------------- | --------------- | --------- |
| 14/10 | Reunião de allinhamento de demandas| Discurssão                       |                 | Concluído |
| 15/10 | Estudo e Utilização das ferramentas                           | Estudo                            |                 | Concluído |
|20/10  | Elaboração do diário de bordo e testes com resumo das ferramentas  | Documento  |     [Ferramentas de segurança](../../estudo/ferramentas.md)             | Concluído |

### Maiores Avanços

O principal avanço deste estudo foi compreender a lógica das camadas de defesa em um pipeline de segurança, reconhecendo que cada ferramenta é responsável por mitigar um tipo específico de risco.
Houve o compreendimento que o Gitleaks atua na prevenção de vazamento de segredos, o Semgrep identifica falhas de código, o pip-audit assegura a utilização de dependências seguras e o Trivy contribui para a proteção da infraestrutura.


### Maiores Dificuldades

Não foram notadas dificuldades, o uso do git leaks foi feito corretamente.

### Execução do gitleaks

![Daniel — GitlLeaks](../../imgs/Daniel-GitLeaks.png)

## Resumo da Execução do Gitleaks

Como o GitLeaks não havia sido utilizado no projeto, apenas foi necessário executar o scanner via comando diretório raíz do projeto. Por fim, foi feita uma  analise do arquivo `gitleaks-report.json`.


###  Comando Utilizado

```bash
docker run --rm -v "$(pwd):/repo" zricethezav/gitleaks:latest detect --source="/repo" --report-path="/repo/gitleaks-report.json"

```

Explicação: Ao rodar o comando, o Docker fará o download da imagem do Gitleaks (se for a primeira vez) e iniciará a varredura, podendo encontrar dois resultados. Caso não haja segredos, não haverá report de leaks, caso haja, serão reportados no arquivo `gitleaks-report.json`.

Para melhor verificação, foi realizada uma comparação com os demais estudantes que utilizaram o GitLeaks.

## Resultados do Scan

### Estatísticas Gerais

| Métrica | Valor |
|----------|--------|
| **Commits escaneados** | 3.462 |
| **Dados analisados** | 27.21 MB |
| **Vazamentos detectados** | 33 |

---

### Principais Regras Detectadas (RuleID)

| Regra | Ocorrências | Prioridade |
|--------|--------------|-------------|
| `generic-api-key` | 28 | Alta |
| `gcp-api-key` | 2 | Média |
| `slack-webhook-url` | 1 | Alta |
| `private-key` | 1 | Crítica |
| `jwt` | 1 | Média |

-----

###  Exemplos Relevantes do Relatório

-----

### 1\. Token de Autenticação JWT  JWT (JSON Web Token)

Este é um token de autenticação que foi "hardcoded" (fixado no código), provavelmente como um exemplo em um arquivo de documentação. Se esse token ainda for válido, ele pode permitir que um invasor se passe por um usuário legítimo.

  * **Regra**: `jwt`
  * **Arquivo**: `docs/development-guides/pt-br/api.rst:30`
  * **Autor**: David Carlos ([ddavidcarlos1392@gmail.com](mailto:ddavidcarlos1392@gmail.com))
  * **Data do commit**: 2024-05-22
  * **Trecho identificado**:
    ```
    eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImV4cCI6MTcxNjMwODkwOSwiaWF0IjoxNzE2MjIyNTA5LCJqdGkiOiI2MDNmYTYzOGRiNjU0ZDc5ODA5NjU3NWUxYjgwY2NiOCIsInVzZXJfaWQiOjc5Nn0.3QZdVL9A_EcAb5LJFWdcSHXRQ8ZWJ2P5RGq8yE9JzRc
    ```

-----

### 2\. Chave de API do Google Cloud Platform (GCP)

Essa chave de API, específica para serviços do Google Cloud, foi encontrada em um arquivo JavaScript. Chaves como essa podem ser usadas para abusar de serviços da GCP (como Maps, AI, etc.), gerando custos financeiros e permitindo acesso não autorizado a recursos na nuvem.

  * **Regra**: `gcp-api-key`
  * **Arquivo**: `lib/js/legacy/utilsPushNotification.ts:38`
  * **Autor**: Fábio Macêdo Mendes ([fabiomacedomendes@gmail.com](mailto:fabiomacedomendes@gmail.com))
  * **Data do commit**: 2019-03-17
  * **Trecho identificado**:
    ```
    AIzaSyDf2GEdXj1Kn5krFrRYjU_LMfTKVSOafjc"
    ```

-----

### 3\. Chave Secreta do Django

Embora detectada pela regra genérica, esta é a **`DJANGO_SECRET_KEY`**, uma das credenciais mais críticas de uma aplicação Django. Ela é usada para assinatura criptográfica de sessões e tokens de CSRF. Sua exposição permite que um invasor forje sessões de usuário e execute ataques graves.

  * **Regra**: `generic-api-key`
  * **Arquivo**: `docker/config.env:18`
  * **Autor**: Fábio Macêdo Mendes ([fabiomacedomendes@gmail.com](mailto:fabiomacedomendes@gmail.com))
  * **Data do commit**: 2019-07-14
  * **Trecho identificado**:
    ```
    DJANGO_SECRET_KEY=al238gd93glx6o32nl02l1gi12678xz903
    ```

-----

### 4\. Senha de Servidor de E-mail

Esta credencial, `EMAIL_HOST_PASSWORD`, é a senha para um serviço de envio de e-mails (provavelmente um servidor SMTP). Com ela, um invasor pode usar o servidor de e-mail da aplicação para enviar spam, phishing ou e-mails maliciosos em nome do sistema.

  * **Regra**: `generic-api-key`
  * **Arquivo**: `docker/env/email.env:4`
  * **Autor**: Gabriela Alves ([gabrielaalvesdagama@gmail.com](mailto:gabrielaalvesdagama@gmail.com))
  * **Data do commit**: 2018-09-25
  * **Trecho identificado**:
    ```
    EMAIL_HOST_PASSWORD=195a1656bd6681b58c7923a510b8844a-db1f97ba-64fe177d
    ```

-----

### 5\. Chave Privada do Google reCAPTCHA

Esta é a chave privada (`private key`) do serviço reCAPTCHA do Google. A exposição dessa chave pode permitir que um invasor contorne as proteções contra bots em formulários (como cadastro e login), abrindo caminho para ataques de força bruta, criação de contas falsas e spam.

  * **Regra**: `generic-api-key`
  * **Arquivo**: `config/settings/base.py:335`
  * **Autor**: Bruno Martin ([bruno.martin@hacklab.com.br](mailto:bruno.martin@hacklab.com.br))
  * **Data do commit**: 2017-09-21
  * **Trecho identificado**:
    ```
    DJANGO_RECAPTCHA_PRIVATE_KEY', '6LeIxAcTAAAAAGG-vFI1TnRWxMZNFuojJ4WifJWe'
    ```


### Plano Pessoal para a Próxima Sprint

Na próxima sprint, o foco será direcionado para adicionar novas ferramentas e tentar abrir issues 

---