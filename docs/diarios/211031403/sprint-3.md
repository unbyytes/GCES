### **Diário de Bordo – Marcus**

**Disciplina:** [GERÊNCIA DE CONFIGURAÇÃO E EVOLUÇÃO DE SOFTWARE] **Equipe:** [OWASP] **Comunidade/Projeto de Software Livre:** Pencil Labs / EJ-Platform

---

### **Sprint 3 – 07/10/2025 – 22/10/2025**

#### **Resumo da Sprint**

O foco desta sprint foi a análise prática de duas classes de vulnerabilidades: **IDOR (Insecure Direct Object References)** e **Gerenciamento de Sessões Inseguras**. Nessa sprint, visei entender um pouco mais os conceitos e riscos, seguida pelos testes praticos no ambiente local da EJ-Platform para observar o comportamento da aplicação diante das tentativas de possiveis ataques.

#### **Atividades Realizadas**

| Data | Atividade | Tipo (Código/Doc/Discussão/Outro) | Link/Referência | Status |
| :--- | :--- | :--- | :--- | :--- |
| 09/09 | Estudo sobre vulnerabilidades IDOR | Estudo | [[OWASP - INDOR](https://owasp.org/www-project-top-ten/2017/A5_2017-Broken_Access_Control)] | Concluído |
| 12/10 | Estudo sobre Gerenciamento de Sessões Inseguras | Estudo | [OWASP - Session Management](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/01-Testing_for_Session_Management_Schema) | Concluído |
| 14/10 | Preparação do ambiente com usuários de teste | Configuração | - | Concluído |
| 17/09 | Teste prático de IDOR (Acesso Direto por URL) | Teste/Exploração | - | Concluído |
| 19/09 | Análise prática de Cookies de Sessão (Flags Segurança) | Estudo/Teste | - | Concluído |

#### **Ferramentas**

* **Navegador Web (OperaGX/Firefox) e Ferramentas de Desenvolvedor (DevTools):** Navegador padrão que utilizei para a análise. A aba "Network" foi usada para observar as respostas do servidor e a aba "Application" (ou "Storage") para inspecionar os cookies de sessão.
* **Múltiplos Perfis de Navegador/Janelas Anônimas:** Utilizados para manter sessões ativas de dois usuários diferentes (`user@user.com` e `admin@admin.com`) simultaneamente, passo crucial para testar o controle de acesso do IDOR.
* **Painel de Administração Django:** Acessado via `/admin/` com credenciais de administrador, foi utilizado para identificar os IDs numéricos dos usuários de teste.
* **OWASP ZAP (Zed Attack Proxy):** É uma ferramenta gratuita e open-source para encontrar vulnerabilidades em aplicações web, sendo um dos principais projetos da OWASP. Foi utilizado na tentativa de aprofundar o teste de IDOR. O ZAP foi configurado para interceptar a requisição de "Salvar Perfil" enviada pelo navegador. O objetivo era modificar o ID do usuário dentro dessa requisição antes de enviá-la ao servidor, para tentar alterar os dados de outro usuário.

#### **Passo a Passo e Exemplos com Aplicação na EJ**

O objetivo foi simular e observar o comportamento da aplicação frente a tentativas de exploração das vulnerabilidades estudadas, no caso dessa sprint, o foco maior foi na simulação via IDOR.

**A. Teste de IDOR (Insecure Direct Object Reference)**

O teste foi dividido em duas fases: tentativa de acesso direto via URL (GET) e tentativa de modificação de dados via interceptação de requisição (POST).

- Preparação: Garanti que os usuários de teste (admin@admin.com e user@user.com) existissem no banco através do comando inv docker-exec "inv db-fake".

- Identificação de IDs: Logado como admin, acessei o painel /admin/ e, na seção Profiles, identifiquei os IDs dos usuários (admin ID 34, user ID 35) inspecionando as URLs de edição (.../profile/<ID>/change/).

- Teste de Acesso Direto (GET): Em uma janela anônima logado como user@user.com, tentei acessar a URL http://localhost:8000/users/34/. O servidor respondeu com 404 Page not found, indicando que este endpoint não existe.

**B.Teste de Modificação (POST) com OWASP ZAP:**

- Configuração: Iniciei o ZAP e lancei seu navegador pré-configurado (Launch Browser).

- Ação: No navegador do ZAP, fiz login como user@user.com e naveguei até a página de edição de perfil (/profile/edit/). Alterei o campo "Nome" para um valor de teste (ex: "Joe User - HACKED").

- Interceptação: Ativei o breakpoint global no ZAP e cliquei em "Salvar" no navegador.

- Análise da Requisição: O ZAP interceptou a requisição POST para /profile/edit/. Analisei a URL e o corpo da requisição e não encontrei nenhum parâmetro contendo o ID do usuário (35) que pudesse ser modificado para o ID do admin (34). A requisição continha apenas os dados do formulário e tokens de segurança (CSRF, sessionid).

#### **Resultados**

A tentativa de acesso GET falhou com 404 Not Found, confirmando que a rota /users/<ID>/ não existe.

A análise da requisição POST interceptada pelo ZAP demonstrou que a funcionalidade de edição de perfil não utiliza um ID explícito na requisição para identificar o usuário a ser modificado. A aplicação provavelmente identifica o usuário alvo com base no cookie de sessão (sessionid).

**Conclusão sobre IDOR:** Para os endpoints testados (visualização direta via /users/<ID>/ e edição de perfil via /profile/edit/), a aplicação não se mostrou vulnerável a ataques de IDOR por manipulação de ID. O primeiro vetor não existe, e o segundo vetor não utiliza o ID na requisição, baseando-se na sessão para identificar o usuário.

#### **Maiores Avanços**

* Início da compreensão prática sobre vulnerabilidades web, focando em IDOR e Gerenciamento de Sessão Inseguro.
* Uso inicial da ferramenta OWASP ZAP como proxy de interceptação, conseguindo capturar e analisar requisições HTTP (POST) enviadas pelo navegador.
* Execução bem-sucedida dos testes manuais planejados para IDOR (tentativa de acesso GET via URL e tentativa de modificação POST via ZAP), mesmo que os resultados tenham indicado que a aplicação estava segura nestes pontos específicos.

#### **Maiores Dificuldades**

* Entender a interface e como funciona o OWASP ZAP, configurar o navegador, ativar breakpoints e analisar as requisições interceptadas exigiu tempo e pesquisa, por ser a primeira utilização da ferramenta.
* Teste prático de interceptação de sessão (para explorar a ausência da flag Secure) exigirá um conhecimento mais avançado sobre ataques Man-in-the-Middle e a configuração adequada de ferramentas de proxy para simular esse cenário.

#### **Plano Pessoal para a Próxima Sprint**

* ■ Preparar o ambiente para o teste de Cookies/Sessão.
* ■ Realizar um teste prático mais profundo sobre interceptação de sessão.
