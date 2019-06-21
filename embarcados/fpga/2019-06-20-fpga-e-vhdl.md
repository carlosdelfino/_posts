---
title: FPGA o que é como funciona e as relações com VHDL
tags: [fpga, vhdl, asic, vhsic, psl, lógica temporal, Property Specification Language, DoD, IEEE, CAD, Verilog]
redirect_from: /embarcados/fpga
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

Inicialmente irei falar dos conceitos básicos envoltos no FPGA para depois trata-lo, não irei me aprofundar muito aqui, porém haverão (quem sabe) outros artigos onde irei apresentar mais informações em especial sobre o FPGA e depois sobre VHDL, aprofundando neste último o necessário para se ter sucesso no desenvolvimento de pequenas soluções com o DE0-Nano. E quem sabe chegando a desenvolver um pequeno microcontrolador Risc, baseado no RiscV ou no Cortex-M1, e posteriormente algumas ensaios com Redes Neurais em FPGA (se isso é uma boa ideia ou não veremos nos testes)

## Terminologia

* HDL - Hardware Description Language
* VHDL - VHDL (**V**HSIC **H**ardware **D**escription **L**anguage ou **Very **H**igh Speed ASIC **D**escription **L**anguage)
* VHSIC - Very High Speed Integrated Circuit
* Register Transfer Level (RTL) - Um típo de modelagem comportámental, com o proposito de síntese do hardware.
  * O Hardware está implícito ou é inferido
  * Sintetizavel
* Síntese - Traduz HDL para um circuito e então otimiza a representação do circuito.
* Processo – Unidade básica de execução no VHDL

## VHSIC

VHSIC (Very High Speed Integrated Circuit) foi um programa do governo americano onde investiu U$ 1 Bilhão (Um Bilhão de Dólares). O objetivo do programa desenvolvido pelo Departamento de Defesa Americano (DoD) era atender demandas de hardware de alto desempenho e qualidade para os serviços da Aeronáutica e Marinha Militar e o Exército, sendo um programa de interesse militar, resolvendo assim a crise do ciclo de vida dos projetos eletrônicos.

O Programa envolvia o desenvolvimento de soluções e materiais para produção de circuitos, seu empacotamento, teste e algoritmos, além de criar uma grande coleção de ferramentas de projeto auxiliado por computador (Computer-aided design - CAD), originando assim o VHDL (VHSIC Hardware Description Language) como descrito a seguir.

## VHDL

A VHDL  do inglês **V**ery **H**igh speed ASIC **D**escription **L**anguage em algumas publicações e em outras **V**HSIC **H**ardware **D**escription **L**anguage já que condiz com HDL que é a linguagem usada pelo programa VHSIC do DoD.

VHDL é uma Linguagem de descrição de hardware, inicialmente proposta para ASICS e hoje usada também para FPGA (mais detalhes logo a baixo). Além disso, permiti a sintetização do hardware conforme programado e é possível também descrever o processo de simulação do funcionamento proposto.

Criada com base na lingaugem ADA, para que não seja necessário criar uma nova linguagem do zero, porém o VHDL sofreu diversas revisões depois de sua especificação e padronização pelo IEEE, ela foi criada por engenheiros de Hardware Integrados da Texas Instruments, Engenheiros especialistas em projetos de sistemas computacionais da IBM e pela empresa Intermetrics conforme descrito acima quando foi feito o projeto do VHSIC.

Em uma das revisões do VHDL apresentadas, foi adicionado a capacidade de espanção por uma linguagem estruturada como o C/C++. Lembrando que VHDL é uma linguagem totalmente paralela, já que descreve o funcionamento de um hardware e suas intruções não são executadas sequêncialmente.

Além da VHDL hoje temos também o Verilog que é uma linguagem baseada em C e não iremos falar dela nesta série de artigo inicialmente, mas futuramente poderei adicionar um artigo especifico sobre ela.


### PSL

Em 2008 foi incorporado ao VHDL um subset básico do PSL (Property Specification Language), uma "lógica temporal" que estende a "lógica temporal linear" com uma faixa de operadores para facilitar a expressão e melhorar o poder expressivo.

 (PSL) is a temporal logic extending Linear temporal logic with a range of operators for both ease of expression and enhancement of expressive power. PSL makes an extensive use of regular expressions and syntactic sugaring. It is widely used in the hardware design and verification industry, where formal verification tools (such as model checking) and/or logic simulation tools are used to prove or refute that a given PSL formula holds on a given design.

PSL was initially developed by Accellera for specifying properties or assertions about hardware designs. Since September 2004 the standardization on the language has been done in IEEE 1850 working group. In September 2005, the IEEE 1850 Standard for Property Specification Language (PSL) was announced.

## Linha do Tempo

* 1980 - O Departamento de Defesa dos Estados Unidos (DOD) fundou o projeto para criar uma ingaugem padrão de descrição de hardware sobre o programa Very High Speed Integrated Circuit (VHSIC).
* 1987 - O Institute of Electrical and Electronics Engineers (IEEE) ratifica a linguagem como um padrão  IEEE de número 1076.
* 1993 - A Linguagem VHDL foi revisada e atualizada para IEEE 1076 ‘93, também foi publicado com o ISBN 1-55937-376-8
* 2000 - feita a revisão IEEE 1076-2000 onde foi introduzido tipos protegidos.
* 2002 - IEEE 1076-2002[6] revisão da 1076-2000. Regras para portas buffers foram flexibilizadas.
* 2004 - o IEC adota a IEEE 1076-2002 como sendo IEC 61691-1-1:2004
* 2008 - definida a IEEE 1076-2008 previamente conhecida como 1076-200x, sendo uma revisão maior, incorpora um subset básico do PSL (Property Specification Language) permite generics em pacotes e subprogramas e introduz o uso de nomes externos. IEC adota como IEC 61691-1-1:2011

## Outros padrões relacionados

IEEE 1076.1 VHDL Analog and Mixed-Signal (VHDL-AMS)
IEEE 1076.1.1 VHDL-AMS Standard Packages (stdpkgs)
IEEE 1076.2 VHDL Math Package
IEEE 1076.3 VHDL Synthesis Package (vhdlsynth) (numeric_std)
IEEE 1076.3 VHDL Synthesis Package - Floating Point (fphdl)
IEEE 1076.4 Timing (VHDL Initiative Towards ASIC Libraries: vital)
IEEE 1076.6 VHDL Synthesis Interoperability (withdrawn in 2010)
IEEE 1164 VHDL Multivalue Logic (std_logic_1164) Packages

* Verilog foi definido em 2005 como IEEE 1364-2005

* Two sets of constructs:
  * Synthesis
  * Simulation
* The VHDL Language is made up of reserved keywords.
* The language is, for the most part, NOT case sensitive.
* VHDL statements are terminated with a ;
* VHDL is white space insensitive. Used for readability. 
* Comments in VHDL begin with “--” to eol

### VHDL Design Units

* Entity
  * Used to define external view of a model. i.e. symbol
* Architecture
  * Used to define the function of the model. i.e. schematic
* Configuration
  * Used to associate an Architecture with an Entity
* Package
  * Collection of information that can be referenced by VHDL models. I.e. Library
  * Consist of two parts Package Declaration and Package Body.


Estou trabalhando neste artigo, pode demorar para conclui-lo, mas se desejar informações poste nos comentários.
{: .notice }

## Referências

* https://en.wikipedia.org/wiki/VHDL
* https://www.elprocus.com/fpga-architecture-and-applications/
* https://en.wikiversity.org/wiki/Understanding_FPGA_Design
* https://www.ics.uci.edu/~jmoorkan/vhdlref/Synario%20VHDL%20Manual.pdf

