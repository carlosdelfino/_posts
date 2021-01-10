---
title: FPGA o que é como funciona e as relações com VHDL
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

Como descrito no [artigo sobre VHDL](/embarcados/vhdl), pretendia escrever um artigo apenas sobre VHDL e FPGA, porém o artigo foi ficando grande e resolvi dividi-lo de forma cada tópico principal ficar separadamente com os detalhes importantes para seu entendimento.

Não pretendo me aprofundar muito, porém haverão (quem sabe) outros artigos onde irei apresentar mais informações em especial sobre o FPGA, detalhes sobre [VHDL veja o artigo este link](/embarcados/vhdl), 

Estarei usando o DE0-Nano para apresentar inicialmente pequenos projetos e quem sabe chegando a desenvolver um pequeno microcontrolador Risc, baseado no RiscV ou no Cortex-M1, e posteriormente algumas ensaios com Redes Neurais em FPGA (se isso é uma boa ideia ou não veremos nos testes), passaremos pelo uso do [FreeRTOS](/embarcados/RTOS). 

Não espere que a leitura deste texto seja agradável e simples, será usada muitas abreviações e para clareza não irei descrever todas elas, portanto procure outros artigos no site que as descreva (algumas farei um link) ou procure no google, se achar relevante use os comentários para pedir mais detalhes, quando eu tiver tempo atualizo com as principais demandas.

Este artigo é puramente teórico para introduzir os conceitos sobre FPGA e VHDL, para que em outros outros possamos nos dedicar apenas aos exemplos. Portanto para hobistas e makers em geral não é preciso dominar o conceito para fazer testes e experimentos a nível de distração, como eu mesmo fiz nos primeiros contatos com a tecnológia.

## FPGA

Vamos ao que interessa, já que conhecer o FPGA sem entender [VHDL](/embarcados/vhdl) não é nada, sugiro que seja lido antes o [artigo](/embarcados/vhdl) que falo especificamente da linguágem que é responsável pela descrição do projeto (design) do hardware..

Caso não conheça as tecnologias de base envoltas na construção do FPGA, irei escrever um artigo com conceitos mais básicos ainda, onde falo dos [circuitos lógicos básicos, como D-Flop, Flip Flop, Lach, Buffer, AND, OR e NOR como circuitos](/embarcados/circuitos_logicos).

### Tecnologias Relacionadas

Existem dez (pelo menos que eu ouvi falar) tecnológias relativas a construção de hardwares, que são importante termos noção de sua existência, no mínimo, sugiro já que não pretendo aborda-las nem mesmo futuramente, pois fogem do meu objetivo de estudo, aprofundarem quando possível um pouco mais buscando nem que seja apenas na Wikipedia, abaixo listo os nomes destas:

* Application-specific integrated circuit (ASIC)
* Erasable programmable logic device (EPLD)
* Simple programmable logic device (SPLD)
* Macrocell array
* Programmable array logic (PAL)
* Programmable logic array (PLA)
* Programmable logic device (PLD)
* Complex Programmable logic device (CPLD)
* Generic array logic (GAL)
* Programmable Electrically Erasable Logic (PEEL)

Os FPGAs precisam, em média, uma área 40 vezes maior, consomem 12 vezes mais energia e executam suas funções programadas a um terço da velocidade das implementações ASIC correspondentes.

### Mas o que é FPGA

Bem vamos então entender o que é FPGA, como vimos acima temos muitas tecnologias envoltas e concorrentes, a mais importante é o ASIC, que é o hardware como conhecemos, um processador, um conversor analógico digital, um controlador de função bem especifica, típicamente não programável, pode até vir a ser parametrizável. (Em breve escrevo sobre a diferença entre [Programável e Parametrizável](/embarcados/programavel_vs_parametrizavel)).

Jà o FPGA é um conjunto de Células Lógicas que são programadas através de interconexões que fazem com elas em conjunto desenvolvam atividades especializadas sintetizando assim circuitos lógicos mais complexos.

Células lógicas também chamados de *Blócos Básicos de Construção* (Basic building block), há fabricantes que usam o termo *Blocos de Lógica Combinacional* (combinational logic blocks - CLBs), ou *Array de Blocos Lógicos* (logic array blocks - LABs), a Intel por exemplo chama tais blocos de **Elementos Lógicos** (Logic Elements - LE) ou **Módulos Lógicos Adaptáveis** (Adaptive Logic Modules - ALM).

![Exemplo de uma Célula Lógica Simplificada](/images/embarcados/fpga/FPGA_cell_example.png){: .wrap .align-center}
Exemplo de uma Célula Lógica Simplificada
{: .wrap .text-center .text-caption}

Uma Célula Lógica como pode ser visto na imagem acima, de forma simplificada, é composto por conjuntos de circuitos do tipo LUT (Lookup Table), FA - (Full Adder) e DFF (D-type flip-flop), detalhes sobre estes circuitos podem ser vistos no [artigo sobre Circuitos Lógicos](/embarcados/circuitos_logicos), e conexões, estas Células Lógicas são dispostas em uma matriz, chegando a milhares de células em um único chip.

Na imagem abaixo, é apresentado como as células lógicas são interconectadas através de uma matriz de barramentos, neste caso chamada "Interconexão Mesh", ou seja interconexão em malha. Também pode ser percebido que um FPGA é compostos por diversos outros elementos, não iremos nos aprofundar tanto.

![Interconexões detalhadas de uma arquitetura FPGA baseado em Interconexões Mesh](/images/embarcados/fpga/Detailed-Interconnect-of-Mesh-based-FPGA-Architecture_Q320.jpg){: .align-center}
Interconexões detalhadas de uma arquitetura FPGA baseado em Interconexões Mesh
{: .wrap .text-center .text-caption}

O Termo FPGA como intuímos acima, significa Field Programable Gate Array, ou seja é um array de "portas" que são programáveis, e o field? então, no caso do FPGA o fild quer dizer que podem ser programadas depois de o equipamento que o uso já estar totalmente montado e soldado podem ser programda como se faz com um microcontrolador através de portas de manutenção, como JTAG, Serial, ISP entre outras. O FPGA pode ter seu funcionamento inteiramente alterado mesmo depois do circuito ter sido entregue ao cliente, e ai se encontra uma brecha de segurança que alguns fabricantes resolveram adicionando circuitos de criptografia onde o código inserido pelo engenheiro que construiu o hardware sela o produto final, impedindo que estes seja alterado por qualquer um, assim qualquer tentativa de alterar o código do FPGA sem a chave criptográfica correta causa a quebra do funcionamento e impede que o circuito continue a funcionar, até que um código validado e auténtico seja recolocado na memória od chip. 

Da mesma forma que em microcontroladores podemos proteger nosso código para que este não seja clonado, no FPGA isso também pode ser feito, impedindo que depois de gravado tal código seja lido da memória do FPGA.


Com estas informações podemos deduzir então que através da descrição apresentada em VHDL podemos sintetizar o circuito que desejarmos em um FPGA, parametrizando as conexões de cada LC, e conectando assim os pinos de nosso circuito aos pinos de saida e entrada  do chip FPGA quando este precisar se conectar ao mundo externo.

Além das LC, podemos encontrar em FPGA outros circuitos com funções especificas que não seriam necessários nem producentes construí-los no FPGA por nós mesmo, senso assim memórias, geradores de clock e outros conversores já vem implementados para se economizar células. Além disso mémorias volateis e não volateis são presentes para armazenar nossa programação que é aplicada ao circuito todas as vezes que um FPGA é ligado, há outros circuitos similares ao FPGA que não tem o conceito de programação em campo (Field Programming, FP do FPGA), estes são programados durante sua produção e assim são mantidos por toda a vida, há os  que permitem serem apagados por sinais ultravioleta ou altatenção como nas EPROMs.

## História do FPGA

Xilinx co-founders Ross Freeman and Bernard Vonderschmitt invented the first commercially viable field-programmable gate array in 1985 – the XC2064.[9] The XC2064 had programmable gates and programmable interconnects between gates, the beginnings of a new technology and market.[10] The XC2064 had 64 configurable logic blocks (CLBs), with two three-input lookup tables (LUTs).[11] More than 20 years later, Freeman was entered into the National Inventors Hall of Fame for his invention


The 1990s were a period of rapid growth for FPGAs, both in circuit sophistication and the volume of production. In the early 1990s, FPGAs were primarily used in telecommunications and networking. By the end of the decade, FPGAs found their way into consumer, automotive, and industrial applications.[15]

Companies like Microsoft have started to use FPGAs to accelerate high-performance, computationally intensive systems (like the data centers that operate their Bing search engine), due to the performance per watt advantage FPGAs deliver.[16] Microsoft began using FPGAs to accelerate Bing in 2014, and in 2018 began deploying FPGAs across other data center workloads for their Azure cloud computing platform.[17]

In 2012 the coarse-grained architectural approach was taken a step further by combining the logic blocks and interconnects of traditional FPGAs with embedded microprocessors and related peripherals to form a complete "system on a programmable chip". This work mirrors the architecture created by Ron Perlof and Hana Potash of Burroughs Advanced Systems Group in 1982 which combined a reconfigurable CPU architecture on a single chip called the SB24.[citation needed]


## Emulando processadores 

An alternate approach to using hard-macro processors is to make use of soft processor IP cores that are implemented within the FPGA logic. Nios II, MicroBlaze and Mico32 are examples of popular softcore processors. Many modern FPGAs are programmed at "run time", which has led to the idea of reconfigurable computing or reconfigurable systems – CPUs that reconfigure themselves to suit the task at hand. Additionally, new, non-FPGA architectures are beginning to emerge. Software-configurable microprocessors such as the Stretch S5000 adopt a hybrid approach by providing an array of processor cores and FPGA-like programmable cores on the same chip.


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