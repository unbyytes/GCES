### **Diário de Bordo – Marcus**

**Disciplina:** [GERÊNCIA DE CONFIGURAÇÃO E EVOLUÇÃO DE SOFTWARE] **Equipe:** [OWASP] **Comunidade/Projeto de Software Livre:** Pencil Labs / EJ-Platform

---

### **Sprint 0 – 08/09/2025 – 09/09/2025**

#### **Resumo da Sprint**
O objetivo principal desta sprint foi configurar o ambiente de desenvolvimento para o projeto EJ-Platform, utilizando o Windows com WSL 2 (Ubuntu). O trabalho consistiu em instalar e configurar as ferramentas essenciais para o projeto, como Docker e Pyenv para o gerenciamento da versão do Python. O processo exigiu a solução de diversos desafios técnicos, incluindo a correção de problemas de rede do Docker e a resolução de conflitos na instalação do Python. Ao final, o ambiente foi estabelecido com sucesso, permitindo que a aplicação seja executada localmente e que a suíte de testes seja rodada corretamente.
#### **Atividades Realizadas**

| Data | Atividade | Tipo (Código/Doc/Discussão/Outro) | Link/Referência | Status |
| :--- | :--- | :--- | :--- | :--- |
| 06/09 | Estudo inicial do `README.md` do projeto | Estudo | [Link para o README] | Concluído |
| 06/09 | Instalação e configuração do Docker Engine no WSL | Configuração | - | Concluído |
| 06/09 | Diagnóstico e correção de erros de DNS do Docker no WSL | Debug/Configuração | - | Concluído |
| 06/09 | Tentativa de uso do `pyenv` e diagnóstico do erro `cygpath not found` | Debug/Discussão | - | Concluído |
| 07/09 | Reinstalação e configuração da versão nativa do `pyenv` para Linux | Configuração | - | Concluído |
| 07/09 | Instalação das dependências de compilação para o Python | Configuração | - | Concluído |
| 07/09 | Instalação da versão específica do Python (3.11.9) com `pyenv` | Código | - | Concluído |
| 07/09 | Criação e ativação do ambiente virtual (`venv`) para o projeto | Código | - | Concluído |
| 07/09 | Instalação das dependências Python do projeto com `pip` | Código | - | Concluído |
| 07/09 | Diagnóstico e correção do erro de `docker-buildx` | Debug/Configuração | - | Concluído |
| 07/09 | Execução bem-sucedida da aplicação via `inv docker-up` | Código | `http://localhost:8000` | Concluído |
| 08/09 | Execução da suíte de testes do projeto com `inv docker-test` | Código | - | Concluído |

#### **Maiores Avanços**

* Ambiente Docker totalmente funcional dentro do WSL, com os problemas de rede resolvidos.
* Aplicação EJ-Platform rodando localmente, acessível via navegador.
* Execução bem-sucedida da suíte de testes do projeto, validando a integridade da configuração do ambiente.
* Compreensão da estrutura de comandos do projeto através do `invoke`.

#### **Maiores Dificuldades**

* A configuração inicial do Docker no WSL apresentou erros de DNS que impediam o download de imagens, exigindo configuração manual dos resolvedores de DNS, nesse caso, precisei ver como alterava o documento onde estáva localizado o DNS e alterar para o da GOOGLE.
* Um conflito de `PATH` com uma versão do `pyenv` para Windows (`pyenv-win`) impediu a execução de comandos `pyenv` no Linux, foi necessário uma reinstalação da ferramenta.
* A compilação do Python falhou (`BUILD FAILED`) por falta de bibliotecas de desenvolvimento  no sistema Ubuntu, então precisei ver como instalava.
* A inicialização dos contêineres foi impedida por um erro de caminho no plugin `docker-buildx`.

#### **Aprendizados**

* Processo detalhado de instalação e configuração do `pyenv` em um ambiente Linux, incluindo a importância das dependências de compilação.
* Diagnóstico de problemas de `PATH` no Linux utilizando comandos como `which` e `echo $PATH`.
* Funcionamento e depuração da integração de rede entre o WSL 2 e o Windows, especialmente em relação ao DNS.
* Uso de ambientes virtuais (`venv`) como prática essencial para isolar dependências em projetos Python.
* Fluxo de trabalho básico com Docker e Docker Compose, orquestrado por meio de um executor de tarefas como o `invoke`.

#### **Plano Pessoal para a Próxima Sprint**

* ■ Explorar o código-fonte do projeto para entender a arquitetura dos módulos do Django.
* ■ Escolher uma issue de baixa complexidade (ex: "good first issue") no repositório do projeto.
* ■ Contribuir ou tentar com pelo menos 1 Pull Request (PR) para o projeto.
