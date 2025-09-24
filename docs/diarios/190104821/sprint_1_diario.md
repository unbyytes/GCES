# Diário de Bordo – [Daniel Rocha Oliveira](https://github.com/DanRocha18)

**Disciplina:** Gerência de Configuração e Evolução de Software - T01 
**Equipe:** OWASP/Blue Team  
**Comunidade/Projeto de Software Livre:** OWASP/ej-application

---

## Sprint 1 – 10/09 até 23/09

### Resumo da Sprint

Os objetivos principais dessa sprint foram:
- Estudar os frameworks de modelagem (STRIDE, DREAD, PATH)
- Recolher o máximo de documentação do projeto
- Realizar a modelagem de riscos utilizando o STRIDE

### Atividades Realizadas

| Data  | Atividade                                   | Tipo (Código/Doc/Discussão/Outro) | Link/Referência | Status    |
| ----- | ------------------------------------------- | --------------------------------- | --------------- | --------- |
| 12/09 | Estudo/leitura da Documentação e Tecnologias           | Estudo                           | [OWASP](https://owasp-org.translate.goog/www-community/Threat_Modeling_Process?_x_tr_sl=en&_x_tr_tl=pt&_x_tr_hl=pt&_x_tr_pto=tc)               | Concluído |
| 15/09 | Pesquisa/inicio da modelagem | Doc                         |     | Em andamento |
| 20/09 | Revisão da Sprint   | Docs                      | [Sprint 1](/GCES/docs/sprints/1.md)  | Concluído |

### Maiores Avanços

O principal avanço nesta sprint foi o estudo aprofundado das tecnologias de modelagem de ameaças STRIDE, DREAD e PATH, fundamentais para a análise de segurança da EJ-Application.

- **STRIDE**: Foi compreendido como um framework robusto para a identificação e categorização de ameaças. Criado pela Microsoft, o modelo analisa os riscos em seis categorias bem definidas:
    - Spoofing (Falsificação de identidade): Ameaças que envolvem um ator malicioso se passando por outro.
    - Tampering (Violação de dados): Modificação não autorizada de dados.
    - Repudiation (Repúdio): Ameaças relacionadas à negação de uma ação realizada.
    - Information Disclosure (Divulgação de Informações): Exposição de informações a quem não deveria ter acesso.
    - Denial of Service (Negação de Serviço): Ataques que visam tornar um sistema ou recurso indisponível.
    - Elevation of Privilege (Elevação de Privilégio): Obtenção de acesso a recursos e permissões que normalmente seriam restritos.
- **DREAD**: O estudo deste modelo permitiu uma abordagem quantitativa para a avaliação de riscos. O DREAD classifica as ameaças com base em cinco fatores, atribuindo uma pontuação a cada um para priorizar as mais críticas:
    - Damage (Dano potencial): Qual o impacto do dano se a vulnerabilidade for explorada?
    - Reproducibility (Reprodutibilidade): Quão fácil é reproduzir o ataque?
    - Exploitability (Explorabilidade): Quão fácil é lançar o ataque?
    - Affected Users (Usuários Afetados): Quantos usuários seriam impactados?
    -Discoverability (Detectabilidade): Quão fácil é encontrar a falha?
- **PATH**: Este framework complementou os estudos ao focar na identificação de caminhos de ataque. A análise com PATH permite visualizar como um invasor poderia navegar através das defesas do sistema, explorando múltiplas vulnerabilidades em sequência para atingir seu objetivo.
- **Modelagem Inicial**: Aliado ao estudo teórico, foi iniciado o levantamento da documentação do projeto EJ-Application e os primeiros passos na modelagem de ameaças. A base teórica adquirida com os frameworks já está sendo aplicada na análise inicial da arquitetura da aplicação, direcionando o mapeamento da superfície de ataque.

### Maiores Dificuldades

Não houve grandes dificuldades, o conteúdo assimilado com clareza.

### Aprendizados

Os principais aprendizados desta sprint foram:

**Abordagem Estruturada para Análise de Riscos**: A compreensão aprofundada dos frameworks STRIDE, DREAD e PATH proporcionou o conhecimento de uma metodologia estruturada para identificar, classificar e priorizar ameaças de segurança em sistemas de software. Foi possível entender que a segurança não deve ser reativa, mas sim uma preocupação proativa desde as fases iniciais do desenvolvimento.

**Visão Abrangente das Ameaças**: O modelo STRIDE demonstrou ser uma ferramenta eficaz para não deixar "pontas soltas" na análise, garantindo que as principais categorias de ameaças sejam consideradas. O DREAD, por sua vez, mostrou como transformar a análise de risco em dados quantificáveis, o que é crucial para a tomada de decisões e alocação de recursos.

**Pensar como um Atacante**: O estudo do PATH foi fundamental para desenvolver uma mentalidade orientada à segurança ofensiva ("pensar como um atacante"). Compreender os possíveis vetores e caminhos de ataque é essencial para construir defesas mais eficazes e em camadas.

**Aplicabilidade no Projeto**: O conhecimento adquirido é diretamente aplicável ao projeto OWASP/ej-application. A utilização combinada desses frameworks será de grande valia para o mapeamento da superfície de ataque da aplicação, a identificação de vulnerabilidades e a proposição de contramedidas e estratégias de mitigação alinhadas com as melhores práticas de segurança.

-----

### Plano Pessoal para a Próxima Sprint

- Finalizar a Modelagem
- Dar inicio ao processo de segurança na pipeline
- Revisar documentação