---
title: Aquecedor por Indução
tags: [Indução, ZVS, Zero Volt Swith, LC, Oscilador, Tanque, MOSFET]
categories: [projeto,aquecedor]
layout: article
share: true
toc: true
comments: true
feature:
   category: true
   index: true
tagcloud: true
ads: 
   show: true
image:
   teaser: 
   feature: inducao/InductionCoilHeating.png
math: true
---

Um estudo que visa criar um aquecedor por indução, este aquecedor será usado para varias atividades em meu laborátorio, dentro elas, a cura do metal para correção da dureza de ferramentas como pontas de chaves de fenda e chaves philips, também para fundição de vidro, e outros experimentos.

<!--more-->

## Conceitos Básicos

Precisamos ver alguns conceitos importantes para o entendimento de nosso projeto. Como é meu costume, busco não apenas mostrar que é possível fazer algo, mas busco aprsentar os conceitos para que se apredam realmente como fazer.

### Frequência de resonância

No PDF que pode ser encontrado na lista de referência, podemos ver a indicação de 3 frequências adequadas para nosso projeto, no quadro abaixo listamos as frequências e o uso indicado.

<table>
 <thead>
  <tr>
   <th colspan="3">
   </th >
   <th colspan="4">Diâmetro mais pequeno, aproximado, para aquecimento eficiente a diferentes frequências de indução
   </th>
  </tr>
  <tr>
   <th>Material
   </th>
   <th>Condição
   </th>
   <th>Temperatura
   </th>
   <th>1Khz
   </th>
   <th>3Khz
   </th>
   <th>10Khz
   </th>
   <th>30Khz
   </th>
  </tr>
 </thead>
 <tbody>
  <tr>
   <td>Aço
   </td>
   <td>Abaixo da Temp.
   </td>
   <td>540°C
   </td>
   <td>8,89mm
   </td>
   <td>5,08mm
   </td>
   <td>2,79mm
   </td>
   <td>1,27mm
   </td>
  </tr>
  <tr>
   <td>Aço
   </td>
   <td>Acima da Temp.
   </td>
   <td>870°C
   </td>
   <td>68,58mm
   </td>
   <td>38,10mm
   </td>
   <td>21,59mm
   </td>
   <td>9,65mm
   </td>
  </tr>
 </tbody>
 <tfoot> 
  <tr>
   <td colspan="6">as temperaturas citadas a cima são referentes a temperatura de curie, veja que é indicado se deve ser acima ou abaixo.
   </td>
  </tr>
 </tfoot>
</table>

Caso tenha informações sobre materiais como latão, cobre e aluminio comente abaixo e eu adicionarei a tabela acima, não esqueça de citar a fonte.

Como podem ver quando mais fino é o material maior deve ser a frequência de resonância, isso se deve pelo fato que as frequências mais altas tendem a interagir melhor com a superficie de materiais.

Sendo um material de grosso calibre é preciso frequências mais baixas.

Para o nosso projeto iremos trabalhar com a frequência de 30khz em sua primeira versão. portanto será ideal para pequenas peças, mas lembre-se que quanto menos e mais fina a peça a ser aquecida maior deve ser a frequência, assim em alguns casos o ideal para cura do metal deverão ser frequências entre 30khz e 50khz para materiais entre 2 e 5mm.

### Como o aquecimento é produzido

Chama-se aquecimento por corrente turbilhonar. O Aquecimento ocorre devido a correntes parasitas produzidos no material ferroso e resistivo, portanto quanto mais resistivo o material for maior a tendência ao aquecimento, Já materiais altamente condutores como ouro e cobre é preciso uma maior corrente na bobina e quanto mais fino maior a frequência de ressonância.

O aquecimento também pode se dar por inversão continua da polaridade do material ferroso, chamado de aquecimento por histerese, mas tal aquecimento ocorre até que a permeabilidade magnética seja reduzida a 1, o que ocorre no processo de aquecimento, assim passasse ao aquecimento por corrente turbilhonar.

#### Materiais Isolantes

Materiais isolantes para serem aquecidos precisa ter um receptacu-lo, veja o exemplo de fogões domésticos que usam este sistema, ele induz o calor na panela que transfere para o alimento, da mesma forma isso pode ser feito em materiais como borracha ou plastico que terão um recepiente metálico que irá receber o material e transferir o calor obitdo na indução.

### Indutores

Os indutores para este projeto irão lidar com grandes tensões e correntes, mesmo num projeto que trabalhemos com tensões na faixa de 12V e correntes em torno de 20A, precisamos considerar pelo menos o dobro para margem de segurança devido a transientes e a resonancia do circuito tank, veja detalhes em [Ressonancia do Circuito Tank](ressonancia-do-circuito-tank).

#### Bobinas de Indução

As bobinas especificas de indução tem modelos diversos, e podem ser construidas de forma a se ajustar a peça que deverá ser aquecida. Tais bobinas podem tomar formatos diversos, desde a mais comum helicoidal para uso em tubos comuns, ou outros formados como os exibidos abaixo:

![Bobina de indução moldada em "U" para encaixe, com sistema de refrigeção líquida](/images/inducao/9100-3613_CLINTON-NEVADA-a.jpg)

![Bobina de indução moldada em "U"](/images/inducao/braze-using-the-U-Braze-10-kW-400.jpg)

![Bobina de indução plana para uso em superficies planas diversas](/images/inducao/hqdefault.jpg)

![Bobina de indução plana para uso com panelas](/images/inducao/maxresdefault.jpg)

![Bobina de indução helicoidal de seção quadrada para alto desempenho](/images/inducao/InductionCoilHeating.png)

#### Resfriamento da Bobina de  indutção (Bobina de Aquecimento)

No caso do indutor que irá produzir o calor é preciso atentar que ele pode se aquecer pela transferêrncia termica do ar, e pela corrente também, alguns modelos podem ser resfriados a água para suportar maior tempo aquecendo o material.

Para isso podemos usar tubos de ar condicionado e assim injetar agua ou outro liquido para resfriamento, mas nosso projeto não irá contemplar tal uso.

### Capacitores indicados ao projeto

Da mesma forma que os indutores irão suportar picos de tensões, os capacitores sem dúvida irão receber pancadas mais fortes durante a inversão da polaridade da corrente, para tanto precisam ser capacitores com um isolamento de alta qualidade, é possível usar capacitores de poliester, neste caso usem capacitores com a tensão pelo menos 50 vezes maior que a tensão de trabalho, mas o mais indicado é o uso de capacitores MKP Polipropileno Metalizado.

Os capacitores do tipo MKP são muito comuns em fontes chaveadas de PC, algumas fontes pdoem ser muito uteis para tirar as peças necessárias para este projeto.


### Transistores Chaveadores


## Primeiro protótipo Oscilador ZVS

O primeiro protótipo de aquecedor por indução eletromagnética é o mais simples de ser construido porém com 90% apenas de eficiência, já que é baseado num Oscilador ZVS. A vangagem deste modelo é que ele não demanda microcontrolador e é bem barato de ser construido devido a simplicidade de seu circuito.

O Modelo ZVS usa o Oscilador de mesmo nome, e pode ser melhorado para ter mais de uma frequência de oscilação, porém, tipicamente é construido para atuar apenas numa frequência e com um único tipo de bobina.

### Oscilador ZVS

O oscilador que usaremos para controlador nosso Aquecedor por Indução é o "ZVS (Mazilli) Driver", usaremos uma versão melhorada para minhas necessidades e para reduzir o máximo a corrente e termos o melhor desempnho da bonbina no aquecimento, usaremos uma tenão mais elevada, eu pretendia nesta primeira versão usar a tensão da rede direta no circuito, apenas retificada, o que daria 340V para alimentações de 220V, poderia também usar um dobrador, mas irei adiar esta abordagem, e vou nesta etapa usar uma alimentação de 24V no máximo.

Para nosso oscilador a seguinte fórmula será usada para calcular os capacitores e indutores conforme afrequência indicada que teremos que trabalhar, lembrando que precisamos de uma frequência próxima as sugeridas na tabela cima.

$$
f = \frac{1}{( 2π * \sqrt{L * C} )}
$$

A Baixo listo os componentes do primeiro modelo que estudei para a construção do meu Aquecedor por indução. este modelo foi baseado no artigo publicado por Marlon Nardi, que pode ser visto no video do youtube na lista de referências.

### Ressonancia do Circuito Tank

Devido a mágica percebida como ressonancia do circuito tank, a tensão sobre o circuito LC tende a se elevar na proporção apresentada na formula abaixo:

$$
V_{tk} = \pi * V_p
$$

Onde $$ V_{tk} $$ é a tenSão que será percebida sobre o circuito LC (Circuito Tank), $$ V_p $$ é a tensão de alimentação do Circuito Tank.

Observe que os componentes que estão presentes no circuito tank como os capacitores e os transistores devem ser capaz de suprotar a tensão percebida sobre o circuito com margem de segurança, procure valores 20% superiores a este valor, ficando a formula portanto da seguinte forma:


$$
V_{tk} = 1.2 * ( \pi * V_p )
$$

A ressonância do circuito permite ser monitorada com um LED que irá aumentar seu brilho conforme a tensão se eleva no mesmo, é importante calcular a resistência do LED de forma que no maior pico da tensão de ressonância, a corrente não ultrapasse o limite do LED.

### O Circuito

Abaixo apresento duas formas de se construir nosso projeto, um usa uma bobina com derivação no meio como se fossem duas bobinas, e o segundo usa um divisor com capacitores, onde é retirada a derivação.

Os valores apresentados no circuito simulado são o ideias para que o software demosntre corretamente o funcionamento do circuito, porém na prática os valores serão outros.

#### Simulando a Derivação no indutor

{% include circuitjs1/body.html startCircuit="zvs-inductor-1.txt" width="800px" height="600px" %}

#### Simulando a Derivação por capacitor

{% include circuitjs1/body.html startCircuit="zvs-capacitor.txt" width="800px" height="600px" %}

### Lista de componentes

| 2 | IRFZ44N | MOSFET |
| 2 | Resistores 150R x 2W (Fio) | Resistor Fio |
| 2 | Resistores 1K x 1/4 W (carbono) | Resistor Carbono |
| 3 | Capacitores de Polieste 6,8uF x 250V | Capacitor Poliester |
| 2mt | Fio Rigido 4mm | Fio Rigido |
| 1 | Bateria de moto ou carro 12V x 8A | Bateria Acido Chumbo |
| 1mt | Fio flexivel 4mm | Fio Flexível |
| 10cm | Fio flexível 0.25mm | Fio Flexível |
| 1 | Tubo de 3cm um cano de meia polegada |  

### Segundo modelo usando Arduino


## Referências

* http://adammunich.com/zvs-driver/
* http://kaizerpowerelectronics.dk/general-electronics/royer-induction-heater/
* http://cdn2.hubspot.net/hubfs/508263/Ambrell_PDFs/411-0169-18.pdf
* http://www.onsemi.com/pub/Collateral/AND9056-D.PDF
* http://danyk.cz/induk3.html

### Outras Referências

* https://sites.google.com/site/roelarits/home/wireless-power-transfer/starthelp-for-zvs-mazilli-oscillator
* http://yamanashimasamune.blogspot.com/2016/03/modified-zvs-oscillator-with-centre.html
* http://yamanashimasamune.blogspot.com/2011/08/zero-voltage-switching-zvs-technique.html
* https://krishnamurthy1995.wordpress.com/2016/04/20/royer-oscillator-based-induction-heater/

* http://www.fusor.eu/HV.html

* https://www.youtube.com/watch?v=5w4elabXRVQ
