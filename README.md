# berkeleyRL3
Port para Python 3 do exercício de Aprendizado por Reforço de Berkeley. O material original, escrito em Python 2, está em http://ai.berkeley.edu/reinforcement.html.

O código foi portado do python 2 para 3 com o utilitário 2to3 e alguns ajustes manuais envolvendo tk e divisão inteira. Os testes manuais preliminares correram bem, mas alguns problemas ainda podem estar lá. Feedback e pull requests são bem-vindos! 

# Introdução

Neste projeto, você implementará iteração de valor e Q-learning. Você irá testar seus agentes primeiro no Gridworld (da aula) e, em seguida, aplicá-los a um controlador de robô simulado (Crawler) e Pacman.

Este projeto inclui um autograder para você verificar suas soluções em sua máquina. Ele pode ser executado em todas as questões com o comando: 

`python autograder.py`

Ele pode ser executado para uma questão em particular, como a q2, com:

`python autograder.py -q q2`

Ele pode ser executado para um teste em particular com comandos com a seguinte forma:

`python autograder.py -t test_cases/q2/1-bridge-grid`

Consulte o tutorial do autograder no Projeto 0 (http://ai.berkeley.edu/tutorial.html) para obter mais informações sobre como usar o autograder.

O código para este projeto contém os seguintes arquivos: 

**Arquivos que você vai editar:**

* valueIterationAgents.py: um agente de iteração de valor para resolver MDPs conhecidos.
* qlearningAgents.py: agentes Q-learning para o Gridworld, Crawler e Pacman.
* analysis.py: um arquivo para preencher com suas respostas às perguntas deste projeto.

**Arquivos que você deve ler, mas NÃO editar:**
* mdp.py   Define métodos gerais de MDPs.
* learningAgents.py   Define as classes base ValueEstimationAgent e QLearningAgent, que seus agentes irão estender.
* util.py   Utilitários, incluindo util.Counter, que é particularmente útil para Q-learners.
* gridworld.py   A implementação do Gridworld.
* featureExtractors.py   Classes para extrair recursos em pares (estado, ação). Usado para o agente Q-learning aproximado (em qlearningAgents.py). 

**Arquivos que você pode ignorar:**

* environment.py: Classe abstrata para ambientes gerais de aprendizagem por reforço. Usado por gridworld.py.
* graphicsGridworldDisplay.py: Exibição gráfica do Gridworld.
* graphicsUtils.py: utilitários gráficos.
* textGridworldDisplay.py: Plug-in para a interface de texto Gridworld.
* crawler.py: O código do crawler e o artefatos de teste. Você vai executá-lo, mas não vai editá-lo.
* graphicsCrawlerDisplay.py: GUI para o robô rastreador.
* autograder.py: autograder do projeto.
* testParser.py: Parser de arquivos de solução e testes do autograder
* testClasses.py: Classes de teste gerais de autocorreção (autograding) 
* test_cases/: diretório contendo os casos de teste para cada questão
* reforcementTestClasses.py: Classes de teste de autograding específicas do Projeto 3 


**Arquivos para editar e enviar:** Você preencherá partes de valueIterationAgents.py, qlearningAgents.py e analysis.py durante o exercício. Você deve enviar esses arquivos com seu código e comentários. Não altere os outros arquivos nesta distribuição nem envie qualquer um de nossos arquivos originais que não sejam esses arquivos. 

**Avaliação:** o autograder será executado no seu código para correção técnica. Não altere os nomes de quaisquer funções ou classes fornecidas dentro do código, ou você causará estragos no autograder. No entanto, a corretude de sua implementação - não os julgamentos do autograder - será o juiz final de sua pontuação. Se necessário, revisaremos e avaliaremos os envios individualmente para garantir que você receba o devido crédito pelo seu trabalho. 

**Desonestidade Acadêmica:** iremos comparar seu código com outros envios para verificação de plágio. Nós descobriremos se você copiar o código de outra pessoa e enviá-lo com pequenas alterações. Os detectores de plágio são muito difíceis de enganar, então, por favor, não tente. Confiamos que todos vocês enviarão apenas seus próprios trabalhos; por favor, não nos decepcione. Se você fizer isso, buscaremos as consequências mais sérias que pudermos.

**Conseguindo ajuda:** Você não está sozinho(a)! Se você empacar em algo, entre em contato com a equipe do curso para obter ajuda. O discord, o fórum de discussão e demais recursos existem para sua ajuda; por favor, use-os. Caso não precise de atendimento ao vivo, avise-nos e agendaremos. Queremos que esses projetos sejam gratificantes e instrutivos, não frustrantes e desmoralizantes. Mas não saberemos quando ou como ajudar, a menos que você peça. 

**Discussão:** Por favor tome cuidado para não postar spoilers (trechos de código com a solução).

# MDPs

Para começar, execute o Gridworld no modo de controle manual, que usa as teclas de seta: 

`python gridworld.py -m`

Você verá o layout da aula com duas saídas. O ponto azul é o agente. Observe que quando você pressiona para cima, o agente só se move para o norte 80% do tempo. Assim é a vida de um agente do Gridworld!

Você pode controlar muitos aspectos da simulação. Uma lista completa de opções está disponível executando: 

`python gridworld.py -h`

O agente default se move aleatoriamente:

`python gridworld.py -g MazeGrid`

Você deve ver o agente aleatório se batendo pelo grid até que aconteça de achar uma saída. Essa é uma vida ruim para um agente de IA.

*Nota:* O MDP do Gridworld é tal que você deve primeiro entrar em um estado pré-terminal (as caixas duplas mostradas na GUI) e então realizar a ação especial de sair ('exit') antes que o episódio realmente termine (no verdadeiro estado do terminal chamado TERMINAL_STATE, que não é mostrado na GUI). Se você executar um episódio manualmente, seu retorno total pode ser menor do que o esperado, devido à taxa de desconto (-d para alterar; 0,9 por padrão).

Observe a saída do console que acompanha a saída gráfica (ou use -t para tudo em texto). Você será informado sobre cada transição que o agente experimenta (para desligar, use -q).

Como no Pacman, as posições são representadas por coordenadas cartesianas (x,y) e quaisquer matrizes são indexadas por [x][y], com 'norte' sendo a direção de aumento de y, etc. Por padrão, a maioria das transições receberá uma recompensa de zero, embora você possa alterar isso com a opção de recompensa por viver (-r). 