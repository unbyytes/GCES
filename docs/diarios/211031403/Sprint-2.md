### **Diário de Bordo – Marcus**

**Disciplina:** [GERÊNCIA DE CONFIGURAÇÃO E EVOLUÇÃO DE SOFTWARE] **Equipe:** [OWASP] **Comunidade/Projeto de Software Livre:** Pencil Labs / EJ-Platform

---

### **Sprint 2 – 23/09/2025 – 07/10/2025**

#### **Resumo da Sprint**

O foco desta sprint foi o estudo e a análise de duas classes de vulnerabilidades: IDOR (Insecure Direct Object References) e Gerenciamento de Sessões Inseguras. A pesquisa foi focada em entender o conceito, os riscos associados e, principalmente, em como essas falhas poderiam ser exploradas no contexto da aplicação EJ-Platform, rodando em um ambiente de desenvolvimento local.

#### **Atividades Realizadas**

| Data | Atividade | Tipo (Código/Doc/Discussão/Outro) | Link/Referência | Status |
| :--- | :--- | :--- | :--- | :--- |
| 26/09 | Estudo sobre vulnerabilidades IDOR | Estudo | [[OWASP - INDOR](https://owasp.org/www-project-top-ten/2017/A5_2017-Broken_Access_Control)] | Concluído |
| 01/10 | Estudo sobre Gerenciamento de Sessões Inseguras | Estudo | - | Concluído |
| 03/10 | Exploração manual da aplicação com múltiplos usuários | Teste/Configuração | - | Concluído |
| 05/09 | Análise de cookies de sessão e seus atributos de segurança | Estudo | - | Concluído |
| 05/09 | Tentativa de exploração de IDOR entre usuários | Teste/Exploração| - | Concluído |

#### **Ferramentas**

* Navegador Web (OperaGX/Firefox) e Ferramentas de Desenvolvedor (DevTools): Foi essencial para a análise. A aba "Network" foi usada para observar as requisições e a aba "Application" (ou "Storage") para inspecionar os cookies de sessão.
* Múltiplos Perfis de Navegador/Janelas Anônimas: Utilizados para manter sessões ativas de dois usuários diferentes (user@user.com e admin@admin.com) simultaneamente, extremamente importante pra testar o controle de acesso do IDOR.

#### **Passo a Passo e Exemplos com Aplicação na EJ**

O objetivo foi simular como um invasor poderia explorar essas vulnerabilidades dentro do contexto da aplicação "Empurrando Juntas".

**A. Teste de IDOR (Insecure Direct Object Reference)**

- Cenário de Exploração na EJ-Platform: A hipótese foi que um usuário comum poderia acessar informações de um dashboard ou perfil que pertencem a outro usuário, como um administrador, se o sistema não validasse a permissão corretamente. Por exemplo, se a URL para ver um perfil fosse http://localhost:8000/users/1/profile.

Método de Teste:

- `Preparação:` Utilizando o comando inv docker-exec "inv db-fake", garanti que os usuários de teste, incluindo admin@admin.com e user@user.com, existissem no banco de dados para a simulação.

- `Login Simultâneo:` Para isolar as sessões e simular dois atores diferentes, fiz o login como admin@admin.com em uma janela de navegador e como user@user.com em uma janela anônima separada.

- `Identificação de IDs:` Olhando os perfis, mostrou que as URLs de perfil públicas (ex: /profile/) não mostravam os IDs dos usuários. A estratégia foi então acessar o painel de administração do Django em /admin/ com o usuário administrador. Dentro do painel, na seção de perfis (Profiles), foi possível listar todos os usuários e obter seus IDs numéricos únicos ao inspecionar a URL de edição de cada um (ex: .../profile/34/change/).

- `Tentativa de Acesso Direto:` Com o ID do administrador, na sessão do navegador logado como user@user.com, fiz uma requisição direta à URL hipotética do perfil público do administrador, no formato http://localhost:8000/users/X/.

- `Análise do Resultado:` A aplicação respondeu com um erro Page not found (404). A análise da página de erro, listava todas as rotas válidas, confirmando então que a aplicação não possui um endpoint no formato users/<ID>, indicando que este vetor específico para um ataque de IDOR não é aplicável.

**B. Análise de Sessões / Cookies Inseguros**

Esta análise verifica se os cookies usados para gerenciar a sessão do usuário estão configurados com atributos de segurança essenciais.

- Cenário de Exploração na EJ-Platform: A hipótese era verificar se o cookie de sessão (sessionid) estava sendo transmitido sem as flags de segurança recomendadas, o que poderia facilitar ataques como roubo de sessão (Session Hijacking), especialmente em conjunto com um ataque de XSS.

Método de Teste:

- `Login na Aplicação:` Fiz login com qualquer usuário.

- `Inspeção do Cookie:` Abri as Ferramentas de Desenvolvedor (F12) -> aba "Application" -> "Cookies".

- `Análise dos Atributos:` Localizei o cookie sessionid e verifiquei a presença das seguintes flags:

- `HttpOnly:` Presente, e impede que o cookie seja acessado por scripts JavaScript no lado do cliente, importante contra XSS.

- `Secure:` Garante que o cookie só seja enviado em requisições HTTPS, caso exita.

- `SameSite:` Ajuda a proteger contra ataques de CSRF (Cross-Site Request Forgery).

#### **Resultados**

A. Teste de IDOR (Insecure Direct Object Reference) : A tentativa de acesso direto a perfis de usuário através de URLs com IDs (ex: /users/X/) que é do Admin resultou em um erro 'Page not found (404)'. Isso indica que a aplicação não expõe perfis de usuário através deste tipo de URL, o que a protege contra esta forma específica de ataque de IDOR."

B. Análise de Sessões / Cookies Inseguros: A análise dos cookies de sessão revelou que a aplicação utiliza corretamente as flags HttpOnly e SameSite=Lax, que são boas práticas que protegem contra ataques de XSS e CSRF. No entanto, a flag Secure não está ativada, o que no caso representa um risco de segurança para o ambiente de produção.

#### **Maiores Avanços**

* Configurar o mkdocs dentro do projeto para que não tenha mais problemas na hora de dar commit.
* Começar a entender sobre essas formas de ataque.
* Também compreendi o significado e a importância dos atributos de segurança de cookies (HttpOnly, Secure, SameSite) das quais tive que utilizar IA e pesquisas para aprrender como inspecioná-los corretamente usando ferramentas que já vêm no navegador.

#### **Maiores Dificuldades**

A principal dificuldade foi gerenciar duas sessões de usuários distintas de forma simultânea para realizar os testes de IDOR, exigindo o uso de diferentes navegadores ou perfis para evitar o compartilhamento de cookies.
Outra dificuldade encontrada que ainda permanece e entender realmente como funciona esses ataques baseado no que estamos estudando, aplicar isso na pratica está sendo mais complicado do que eu imaginei. 

#### **Aprendizados**

* Aprendi mais na prática como executar uma análise basica de vulnerabilidade IDOR. O processo envolveu o uso de duas sessões de usuário com privilégios diferentes, a descoberta de IDs de recursos através do painel de administração da aplicação e a tentativa de acesso direto a esses recursos via manipulação da URL para verificar as defesas de controle de acesso.
* Compreendi como inspecionar cookies de sessão usando as ferramentas do navegador para analisar sua segurança. 

#### **Plano Pessoal para a Próxima Sprint**

* ■ Aprofundar o teste de IDOR para incluir requisições de modificação (POST/PUT).
* ■ Realizar um teste prático de interceptação de sessão.
