---
title: FPGA o que é, como funciona e as relações com VHDL
tags: [fpga, vhdl, asic, vhsic, psl, lógica temporal, Property Specification Language, DoD, IEEE, CAD, Verilog]
redirect_from: /embarcados/fpga/
categories: [Embarcados,FPGA]
layout: article
share: true
toc: true
comments: true
feature:
 category: true
 index: true
image:
 feature: embarcados/fpga/de0-nano/de0-nano.jpg
 teaser: embarcados/fpga/de0-nano/de0-nano.jpg
ads: 
 show: true
tagcloud: true
coinbase:
 show: false
---

Neste artigo comento e descrevo informações relevantes sobre oque é FPGA, sua história e sua relação com ASIC (e também o que é ASIC), sem entrar em detalhes de programação.

<!--more-->

Como escrito no [artigo sobre VHDL](/embarcados/vhdl), pretendia escrever um artigo apenas sobre VHDL e FPGA, porém o artigo foi ficando grande e resolvi dividi-lo de forma que cada tópico principal fique  com os detalhes importantes para seu entendimento.

Não pretendo me aprofundar muito, porém haverão (quem sabe) outros artigos onde irei apresentar mais informações em especial sobre o FPGA. Detalhes sobre [VHDL veja o artigo este link](/embarcados/vhdl), 

Estarei usando o DE0-Nano para apresentar inicialmente pequenos projetos e quem sabe chegando a desenvolver um pequeno microcontrolador Risc, baseado no Risc-V ou no Cortex-M1, e posteriormente algumas ensaios com Redes Neurais em FPGA (se isso é uma boa ideia ou não veremos nos testes), passaremos pelo uso do [FreeRTOS](/embarcados/RTOS). 

Não espere que a leitura deste texto seja agradável e simples, será usada muitas abreviações e para clareza não irei descrever todas elas, portanto procure outros artigos no site que as descreva (algumas farei um link) ou procure no google, se achar relevante use os comentários para pedir mais detalhes, quando eu tiver tempo atualizo com as principais demandas. Sempre que possível estarei organizandos as abreviações e uma breve descrição, [clique aqui para ver.](/fpga)

Este artigo é puramente teórico para introduzir os conceitos sobre FPGA e VHDL, para que em outros outros possamos nos dedicar apenas aos exemplos. Portanto para hobistas e makers em geral não é preciso dominar o conceito para fazer testes e experimentos a nível de distração, como eu mesmo fiz nos primeiros contatos com a tecnológia.

## FPGA

Vamos ao que interessa, já que conhecer o FPGA sem entender [VHDL](/embarcados/vhdl) não é nada, sugiro que seja lido antes o [artigo](/embarcados/vhdl) que falo especificamente da linguágem que é responsável pela descrição do projeto (design) do hardware..

Caso não conheça as tecnologias de base envoltas na construção do FPGA, irei escrever um artigo com conceitos mais básicos ainda, onde falo dos [circuitos lógicos básicos, como D-Flop, Flip Flop, Lach, Buffer, AND, OR e NOR como circuitos](/embarcados/circuitos_logicos).

### Tecnologias Relacionadas

Existem dez (pelo menos que eu ouvi falar) tecnológias relativas a construção de hardwares, que são importante termos noção de sua existência, no mínimo, sugiro, já que não pretendo aborda-las nem mesmo futuramente, pois fogem do meu objetivo de estudo, aprofundarem o quando possível, abaixo listo os nomes destas:

* Application-specific integrated circuit (ASIC)
* Erasable programmable logic device (EPLD)
* Simple programmable logic device (SPLD)
* Macrocell array
* Programmable array logic ([PAL](/fpga/pal)
* Programmable logic array ([PLA](/fpga/pla))
* Programmable logic device ([PLD](/fpga/pld))
* Complex Programmable logic device ([CPLD](/fpga/cpld))
* Generic array logic ([GAL](/fpga/gal))
* Programmable Electrically Erasable Logic ([PEEL](/fpga/peel))

Os FPGAs precisam, em média, uma área 40 vezes maior que outros circuitos integrados, consomem 12 vezes mais energia e executam suas funções programadas a um terço da velocidade das implementações ASIC correspondentes. Porém tem a vantagem de serem fácilmente ajustados e atualizados mesmo no ambiente de uso.

### Mas o que é FPGA

Bem vamos então entender o que é FPGA, como vimos acima temos muitas tecnologias envoltas e concorrentes, a mais importante é o ASIC, que é o hardware como conhecemos, um processador, um conversor analógico digital, um controlador de função bem especifica, típicamente não programável, pode até vir a ser parametrizável. (Em breve escrevo sobre a diferença entre [Programável e Parametrizável](/embarcados/programavel_vs_parametrizavel)).

Jà o FPGA é um conjunto de Células Lógicas([Logic Cells - LC](/fpga/lc)) que são programadas através de interconexões que fazem com que elas, em conjunto, desenvolvam atividades especializadas sintetizando assim circuitos lógicos mais complexos.

Células lógicas também chamados de *Blócos Básicos de Construção* (Basic building block), há fabricantes que usam o termo *Blocos de Lógica Combinacional* ([combinational logic blocks - CLBs](/fpga/clb)), ou *Array de Blocos Lógicos* (logic array blocks - LABs), a Intel por exemplo chama tais blocos de **Elementos Lógicos** (Logic Elements - LE) ou **Módulos Lógicos Adaptáveis** (Adaptive Logic Modules - ALM).

![Exemplo de uma Célula Lógica Simplificada](/images/embarcados/fpga/FPGA_cell_example.png){: .wrap .align-center}
Exemplo de uma Célula Lógica Simplificada
{: .wrap .text-center .text-caption}

Uma Célula Lógica como pode ser visto na imagem acima, de forma simplificada, é composto por conjuntos de circuitos do tipo [LUT](/fpga/lut/) (LookUp Table), [FA](/fpga/fa/) - (Full Adder) e [DFF](/fpga/dff/) (D-type flip-flop), detalhes sobre estes circuitos podem ser vistos no [artigo sobre Circuitos Lógicos](/embarcados/circuitos_logicos), e conexões, estas Células Lógicas são dispostas em uma matriz, chegando a milhares de células em um único chip.

Na imagem abaixo, é apresentado como as células lógicas são interconectadas através de uma matriz de barramentos, neste caso chamada "Interconexão Mesh", ou seja interconexão em malha. Também pode ser percebido que um FPGA é compostos por diversos outros elementos, não iremos nos aprofundar tanto.

![Interconexões detalhadas de uma arquitetura FPGA baseado em Interconexões Mesh](/images/embarcados/fpga/Detailed-Interconnect-of-Mesh-based-FPGA-Architecture_Q320.jpg){: .align-center}
Interconexões detalhadas de uma arquitetura FPGA baseado em Interconexões Mesh
{: .wrap .text-center .text-caption}

O Termo FPGA como intuímos acima, significa Field Programable Gate Array, ou seja é um array de "portas" que são programáveis, e o field? então, no caso do FPGA o field quer dizer que podem ser programadas depois de o equipamento  já estar totalmente montado e soldado, podem ser programado como se faz com um microcontrolador através de portas de manutenção, como JTAG, Serial, ISP entre outras. O FPGA pode ter seu funcionamento inteiramente alterado mesmo depois do circuito ter sido entregue ao cliente, e ai se encontra uma brecha de segurança que alguns fabricantes resolveram adicionando circuitos de criptografia onde o código inserido pelo engenheiro que construiu o hardware sela o produto final, impedindo que seja alterado por qualquer um, assim qualquer tentativa de alterar o código do FPGA sem a chave criptográfica correta causa a quebra do funcionamento e impede que o circuito continue a funcionar, até que um código valido e auténtico seja recolocado na memória od chip. 

Da mesma forma que em microcontroladores podemos proteger nosso código para que este não seja clonado, no FPGA isso também pode ser feito, impedindo que depois de gravado tal código seja lido da memória do FPGA.

Com estas informações podemos deduzir então que através da descrição apresentada em VHDL podemos sintetizar o circuito que desejarmos em um FPGA, parametrizando as conexões de cada LC , e conectando assim os pinos de nosso circuito aos pinos de saida e entrada  do chip FPGA quando este precisar se conectar ao mundo externo.

Além das LC, podemos encontrar em FPGA outros circuitos com funções especificas que não seriam necessários nem producentes construí-los no FPGA por nós mesmo, memórias, geradores de clock e outros conversores já vem implementados para se economizar células. Além disso mémorias volateis e não volateis são presentes para armazenar nossa programação que é aplicada ao circuito todas as vezes que um FPGA é ligado, há outros circuitos similares ao FPGA que não tem o conceito de programação em campo (Field Programming, FP do FPGA), estes são programados durante sua produção e assim são mantidos por toda a vida, há os  que permitem serem apagados por sinais ultravioleta ou altatenção como nas EPROMs.

## História do FPGA

Os cófundadores da Xilinx, Ross Freeman e Bernard Vonderschmitt invetaram o primeiro FPGA comercialmente viável em 1985 - o XC2064. O XC2064 tem gates programaveis interconectados entre gates (portas). O XC2064 tem 64 CLBs (Configurable Logic Blocs), com dois LUTs de três portas (3-LUT). Mais de vinte anos depois, Freeman entrou para o Hall da fama dos inventores Nacionais (National Inventors Hall of Fame).

Os anos 1990 foi o periodo de rápido crescimento dos FPGAs, tanto quanto a sofisticação dos circuitos como também quanto o volume de produção. No início dos anos 1990, o FPGA foi inicialmente usado para telecomunicações e equipamentos de redes. No final da decada, FPGAs encontraram amplo uso no campo automotivo, e aplicações industrais.

Empresas como Microsoft iniciou o uso do FPGA para acelerar alta-performance, computabilidade de sistemas de alta densidade (como data centers que operam o sistema de busca Bing), devido a vantagem de  performance por Watt entregue. Microsoft iniciou usando os FPGAs para acelerar o Bing em 2014, e em 2018 iniciou o uso de FPGA para o datacenter da plataforma computacional Azure.

Em 2012 a abordagem arquitetural de alta densidade deu passos em direção a unir blocos lógicos de FPGAs tradicionais com microprocessadores e periféricos relacionados para formar um completo "System on a programmable chip" (Sistema em um chip programável). Este trabalho espelha a arquitetura criada por Ron Perlof e Hana Potash da Burroughs Advanced System Group em 1982 que combinou uma arquitetura de CPU reconfiguravel em um único chip chamado SB24.

Estou trabalhando neste artigo, pode demorar para conclui-lo, mas se desejar informações poste nos comentários.
{: .notice }

## Referências

* https://en.wikipedia.org/wiki/Field-programmable_gate_array
* https://en.wikipedia.org/wiki/VHDL
* https://www.elprocus.com/fpga-architecture-and-applications/
* https://en.wikiversity.org/wiki/Understanding_FPGA_Design
* https://www.ics.uci.edu/~jmoorkan/vhdlref/Synario%20VHDL%20Manual.pdf
* https://ieeexplore.ieee.org/document/5967868 entre outros relativos
* https://www.eeweb.com/profile/gina-smith/articles/beginners-guide-to-understanding-fpga-development

* [Exploration and optimization of a homogeneous tree-based application specific inflexible FPGA - Scientific Figure on ResearchGate. Available from: https://www.researchgate.net/figure/Detailed-Interconnect-of-Mesh-based-FPGA-Architecture_fig1_259506807 \[accessed 22 Jun, 2019\]](https://www.researchgate.net/figure/Detailed-Interconnect-of-Mesh-based-FPGA-Architecture_fig1_259506807)
* [Engenharia reversa do primeiro chip FPGA da Xilinx - XC2064](https://semiwiki.com/fpga/290990-reverse-engineering-the-first-fpga-chip-xilinx-xc2064/)