### **Diário de Bordo – Marcus**

**Disciplina:** [GERÊNCIA DE CONFIGURAÇÃO E EVOLUÇÃO DE SOFTWARE] **Equipe:** [OWASP] **Comunidade/Projeto de Software Livre:** Pencil Labs / EJ-Platform

---

### **Sprint 4 – 22/10/2025 – 19/11/2025**

#### **Resumo da Sprint**

Nesta sprint, o foco foi planejar a execução do ataque de Session Hijacking na aplicação EJ-Platform. Como a execução prática ficou com minha dupla, minha responsabilidade foi definir os passos do teste, auxiliar na configuração do ambiente de rede (Docker e Máquinas Virtuais) e criar o roteiro passo a passo para garantir que a interceptação dos dados funcionasse corretamente.

#### **Atividades Realizadas**

| Data | Atividade | Tipo (Código/Doc/Discussão/Outro) | Link/Referência | Status |
| :--- | :--- | :--- | :--- | :--- |
| 09/09 | Estudo sobre vulnerabilidades Cookies | Estudo | [[OWASP - Cookies](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes)] | Concluído |
| 12/10 | Definição da arquitetura do ataque (Host vs VM Bridge) | Planejamento |  | Concluído |
| 14/10 | Preparação do ambiente com usuários de teste | Configuração | - | Concluído |
| 19/09 | Análise/Suporte do teste prático de Cookies de Sessão (Flags Segurança) | Estudo/Teste | - | Concluído |

#### **Ferramentas**

**Wireshark:** Ferramenta escolhida para a interceptação dos pacotes. Configurei os filtros necessários para limpar o ruído da rede e focar apenas no tráfego HTTP da aplicação.
**VirtualBox (Kali Linux):** Definimos o uso de uma máquina virtual para simular a vítima, garantindo um cenário mais realista de rede externa.
**Docker:** Utilizado para hospedar a aplicação EJ-Platform no sistema principal (Host).

#### **Passo a Passo e Exemplos com Aplicação na EJ**

Para que a exploração da vulnerabilidade desse certo, pensei um cenário que simulasse uma rede real. O passo a passo pensado para se executar foi:

##### 1. Ambiente (O Cenário): 

Em vez de rodar tudo na mesma máquina (o que pode complicar a leitura do Wireshark devido ao Loopback), defini a seguinte estrutura:

* Vítima: Uma Máquina Virtual configurada em modo Bridge. Isso faz com que ela tenha um IP próprio na rede, simulando um computador separado.
* Atacante/Servidor: O computador principal (Host), onde a aplicação EJ está rodando via Docker e onde o Wireshark está escutando.

##### 2. O Plano de Captura:

* A Vítima (VM) acessa a aplicação pelo navegador e faz login. Isso gera um tráfego de rede real saindo da VM para o Host.
* O Atacante (Host) usa o Wireshark para interceptar esse tráfego específico vindo do IP da VM.
* Como a aplicação não usa HTTPS (falta da flag Secure), o cookie sessionid apareceria em texto claro nos pacotes capturados.

#### **Resultados**

A ideia era seguir o roteiro que hávia pensado de inicio, e como resultado, conseguimos realizar o ataque.

* Foi possível identificar claramente os pacotes HTTP vindos da máquina virtual.
* O cookie sessionid foi capturado em texto plano, confirmando a falta de criptografia.
* O julio executou a injeção do cookie e obteve acesso administrativo imediato, validando o teste.

#### **Maiores Avanços**

Aprendi na prática como montar um laboratório de testes de segurança (Pentest Lab). Entendi a importância de separar o atacante da vítima (usando VMs) para facilitar a análise de tráfego de rede e tornar o teste mais fiel a um cenário real de ataque.
