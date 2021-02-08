---
title: Testador de Cabos
layout: article
category: [embarcados,testadores]
tags: [embarcados, testadores, multimetros, medidores, voltimetros, amperimetros, ohmimetros]
---

A muitos anos quero montar meus próprios testadores, desde um simples detector de cabos, até um testador e identificador de CI.

## Detector de Cabo de Energia 

O testador de cabo de energia e se este está quebrado é um dos projetos mais simples a serem feitos, é composto por 3 transitores BC 547, 1 LED, dois Resitor e uma bateria de 9V.

![Detector de Cabo de Energia](\images\embarcados\testador_de_cabos\Circuito-detector-de-Tensão-sem-fio.jpg)

| QTD | Decrição | Valor |
| ---: | :--- | ---: |
|   1  | Resistor 1/8W |       1M |
|   1  | Resistor 1/8W |     100K |
|   1  | Resitor  1/8W |     220R |
||||
|   3  | Transistores  |    BC547 |
|   1  | LED           | Vermelho |
||||
|   1  | Conector para Bateria 9V ||
|   1  | Bateria       |       9V |
|   1  | Chave pushbutton |       |
|      | fios em geral  ||

https://www.mouser.com/datasheet/2/149/BC547-190204.pdf

https://www.onsemi.com/pub/Collateral/P2N2222A-D.PDF

## Testador de Cabo de rede proposto pelo Elias Bernando

Um dos melhores projetos propostos para testador de cabos que encontrei até o momento é do [Elias Bernado](https://studylibpt.com/doc/4322809/testador-de-cabos-de-rede), seu projeto testa a continuidade do cabo, se ele está conectado como 568A ou 568B (cabo cruzado).

![Testador proposto pelo Elias Bernando](images/embarcados/testador_de_cabos/cabos_de_rede_elias_bernado.jpg)

| QTD | Descrição         | Valor  |
|----:|----------------------|-------:|
|   1 | Resitor de 1/8W      |   1,5K |
|   1 | Resitor de 1/8W      |   220R |
|   6 | Resitores de 1/8W    |   330R |
|   1 | Potênciometro Linear |     5K |
||||
|  25 | Diodos               | 1N4148 |
|  21 | LEDs, 4 são verdes os demais são vermelho | Verde e Vermelho |
|   1 | Transitor            |  BC548 |
||||
|   3 | Jack (Conectores)    |   RJ45 |
|   1 | Baterial             |     9V |
|   1 | Conector de Bateria  |        |
|   1 | Chave Liga Desliga   |        |



## TEstador de Cabo de Rede com PIC

http://picsource.com.br/archives/10057
