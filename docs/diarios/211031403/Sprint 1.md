### **Diário de Bordo – Marcus**

**Disciplina:** GERÊNCIA DE CONFIGURAÇÃO E EVOLUÇÃO DE SOFTWARE **Equipe:** OWASP - Red Team **Comunidade/Projeto de Software Livre:** Pencil Labs / EJ-Platform

---

### **Sprint 1 – 11/09/2025 – 24/09/2025**

#### **Resumo da Sprint**

Nesta sprint, o foco foi o estudo de vulnerabilidades de segurança comuns em aplicações web. O objetivo foi compreender o funcionamento, os riscos e as formas de mitigação para quatro tipos de ataques principais: SQL Injection, Cross-Site Request Forgery (CSRF), Cross-Site Scripting (XSS) e Clickjacking.


#### **Atividades Realizadas**

| Data | Atividade | Tipo (Código/Doc/Discussão/Outro) | Link/Referência | Status |
| :--- | :--- | :--- | :--- | :--- |
| 15/09 | Estudo mais avançado referente aos mecanismos de contribuição do projeto | Estudo | [[https://gitlab.com/pencillabs/ej/ej-application](https://gitlab.com/pencillabs/ej/ej-application/-/blob/develop/README.md?ref_type=heads)] | Concluído |
| 16/09 | Estudo sobre vulnerabilidades web SQLi disponível na OWASP. |  Estudo | - | Concluído |
| 18/09 | Estudo sobre vulnerabilidades web XSS |  Estudo | - | Concluído |
| 21/09 | Estudo sobre vulnerabilidades web CSRF e Clickjacking |  Estudo | - | Concluído |

#### **Maiores Avanços**
O principal avanço desta sprint foi puramente teórico, mas muito importante: consegui entender a lógica por trás de ataques que antes pareciam complexos. A grande virada de chave foi perceber que vulnerabilidades como SQL Injection e XSS, por exemplo, nascem do mesmo erro fundamental: confiar na entrada do usuário.

#### **Maiores Dificuldades**

A principal dificuldade encontrada nesta sprint não foi a compreensão inicial dos ataques, mas sim a percepção do tamanho do desafio que vem pela frente. O plano inicial era ir direto para a prática, mas ficou claro que ainda há uma base teórica importante a ser construída.

O desafio agora é duplo:
- Preciso conectar os pontos entre todas essas vulnerabilidades para entender como elas se relacionam e lidar com o receio de ficar "preso" na fase de estudos e adiar demais o início dos testes práticos no Juice Shop e DVWA, que é onde o conhecimento realmente se fixa. Encontrar esse equilíbrio será o maior desafio da próxima sprint.

#### **Aprendizados**

O que ficou mais claro pra mim nesta sprint é que o grande "pecado" no mundo da segurança web é confiar cegamente no que o usuário digita. Basicamente, todo ataque que estudei se aproveita disso de um jeito ou de outro.

* SQL Injection:* É o clássico "texto vira comando". Se eu pego o que o usuário digitou e junto direto numa consulta pro banco de dados, eu abro a porta pra ele mandar o banco fazer o que ele quiser. A ficha que caiu foi que o código e os dados do usuário nunca podem se misturar.
* Cross-Site Scripting (XSS): Aqui, o "texto vira script". A falha é pegar um texto malicioso que alguém enviou e mostrar isso na tela para outros usuários. O navegador da vítima, inocentemente, acha que aquele script faz parte do site e o executa. É tipo deixar alguem invadir sua casa e dexar  pichar um recado na parede  para os próximos visitantes lerem e caírem.
* CSRF e Clickjacking: Esses são mais sobre enganar o navegador ou o próprio usuário. O CSRF explora o fato de eu já estar logado num site para fazer o meu navegador realizar uma ação sem a minha permissão. Já o Clickjacking me engana para clicar em algo que eu não queria, tipo um botão invisível sobreposto a um botão legítimo.

No fim das contas, a lição é sempre desconfiar, validar e "limpar" qualquer dado que venha de fora antes de usá-lo.

#### **Plano Pessoal para a Próxima Sprint**

Fase Teórica:
* ■ Estudar Insecure Direct Object References (IDOR)
* ■  Estudar sobre Sessões/cookies inseguros
* ■ Estudar sobre Brute force / rate limiting

Fase Prática:
* ■ Configurar o ambiente do OWASP Juice Shop e aplicar os conhecimentos em desafios práticos.
* ■ Instalar e explorar as vulnerabilidades presentes no Damn Vulnerable Web Application (DVWA).
