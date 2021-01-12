---
title: "Calculando Skin Depth"
tags: [eletrônica, Eletrônica Básica, Aprendizado, Rádio, RF, Arduino, Sensor, Capacitivo, Indutor, Choque, Reator,  Oscilador, Passa Banda, Filtro, Passa Baixa, Tanque, Filtro Passa Alta, Conversor Analógico Digital, ADC, ACD, Skin, Depth]
category: [BasicaoDaEletronica, Nivel 2]
layout: article
share: true
toc: true
comments: true
feature:
 category: true
 index: true
ads: 
 show: true
image:
  feature: basicaodaeletronica/indutores/indutores-1400x400.png
  teaser: basicaodaeletronica/indutores/indutor_toroidal_ferrite-400x321.jpg
  credit: Diversas Origens
  creditlink: 
tagcloud: true
coinbase:
 show: false
math: true
---

O Skin Effect é um fenômeno onde a corrente elétrica alternada não flui uniformimente com respeito a seção cruzada (cross-section) do elemento condutor, como um fio.

<!--more-->

Revisado em: Janeiro de 2021.
{: .notice }

A densidade de corrente é alta próxima a superfície do condutor e diminui exponencialmente com o aumento da  distância da superfície.

"Skin Depth" faz referência ao ponto em que a densidade de corrente atinge aproximadamente 37% do seu valor na superfície do condutor. Calculando a "skin depth" requer a frequência do sinal AC e a resistividade e permeabilidade relativa do material condutor. Para usar a calculadora, selecione o material e entre a frequência. A resistividade e permeabilidade do material selecionado será preenchido automáticamente.

## Equação de Calculo do Skin Depth

$$
\delta = \sqrt{\frac{\rho}{\pi f_{o} \mu_{r} \mu_{o}}}
$$

* $\delta$ => Skin Depth
* $\rho$ => Resistividade
* $f_{o}$ => Frequência do Sinal
* $\mu_{r}$ => Permeabilidade Relativa
* $\mu_{o}$ => permeabilidade do ar = $4π \times 10^{-7}$

| Selecione o Material: | <select class="form-control" id="mat"><option value="1">Personalizado</option><option value="2">Cobre</option><option value="3">Alumínio</option><option value="4">Ouro</option><option value="5">Prata</option><option value="6">Nickel</option></select> ||
| Resistividade $\rho$ | <input type="number" class="form-control" id="res" placeholder="Entre a Resistividade" disabled="disabled"> | (μΩ cm)
| Permeabilidade Relativa $\mu_{r}$ | <input type="number" class="form-control" id="rp" placeholder="Entre A Permeabilidade Relativa" disabled="disabled"> ||
| Frequência $f_{o}$ | <input type="number" class="form-control" id="fr" placeholder="Enter Frequency"> | <select class="form-control" id="fr-unit"><option value="1">GigaHertz (GHz)</option><option value="2">MegaHertz (MHz)</option></select> |
||<input type="button" id="calculate" class="btn btn-theme" value="Calcular">||

| Skin Depth $\delta$ | <input type="text" id="answer" class="form-control" placeholder="micrometro (μm)">| (μm) |

<script type="text/javascript">
var _0xdbfa=["\x75\x73\x65\x20\x73\x74\x72\x69\x63\x74","\x68\x61\x73\x2D\x73\x75\x63\x63\x65\x73\x73","\x72\x65\x6D\x6F\x76\x65\x43\x6C\x61\x73\x73","\x64\x69\x76","\x63\x6C\x6F\x73\x65\x73\x74","\x68\x61\x73\x2D\x77\x61\x72\x6E\x69\x6E\x67","\x61\x64\x64\x43\x6C\x61\x73\x73","","\x76\x61\x6C","\x65\x61\x63\x68","\x23\x61\x6E\x73\x77\x65\x72\x2D\x66\x69\x65\x6C\x64\x73\x20\x3A\x69\x6E\x70\x75\x74","\x23\x63\x61\x6C\x63\x75\x6C\x61\x74\x65","\x68\x61\x73\x2D\x65\x72\x72\x6F\x72","\x64\x69\x73\x61\x62\x6C\x65\x64","\x61\x74\x74\x72","\x69\x73\x4E\x75\x6D\x65\x72\x69\x63","\x23\x74\x6F\x6F\x6C\x2D\x66\x6F\x72\x6D\x20\x3A\x69\x6E\x70\x75\x74","\x6B\x65\x79\x75\x70","\x63\x6C\x69\x63\x6B","\x70\x72\x65\x76\x65\x6E\x74\x44\x65\x66\x61\x75\x6C\x74","\x23\x72\x65\x73","\x23\x72\x70","\x23\x66\x72","\x31","\x23\x66\x72\x2D\x75\x6E\x69\x74","\x50\x49","\x70\x6F\x77","\x73\x71\x72\x74","\x74\x6F\x50\x72\x65\x63\x69\x73\x69\x6F\x6E","\x23\x61\x6E\x73\x77\x65\x72","\x6F\x6E","\x63\x68\x61\x6E\x67\x65","\x32","\x31\x2E\x36\x37\x38","\x30\x2E\x39\x39\x39\x39\x39\x31","\x33","\x32\x2E\x36\x35\x34\x38","\x31\x2E\x30\x30\x30\x30\x32","\x34","\x32\x2E\x32\x34","\x35","\x31\x2E\x35\x38\x36","\x30\x2E\x39\x39\x39\x38","\x36","\x36\x2E\x38\x34","\x36\x30\x30","\x23\x6D\x61\x74"];!function(_0xccc6x1){_0xdbfa[0];function _0xccc6x2(){_0xccc6x1(_0xdbfa[10])[_0xdbfa[9]](function(){_0xccc6x1(this)[_0xdbfa[4]](_0xdbfa[3])[_0xdbfa[2]](_0xdbfa[1]),_0xccc6x1(this)[_0xdbfa[4]](_0xdbfa[3])[_0xdbfa[6]](_0xdbfa[5]),_0xccc6x1(this)[_0xdbfa[8]](_0xdbfa[7])})}function _0xccc6x3(){_0xccc6x1(_0xdbfa[10])[_0xdbfa[9]](function(){_0xccc6x1(this)[_0xdbfa[4]](_0xdbfa[3])[_0xdbfa[2]](_0xdbfa[5]),_0xccc6x1(this)[_0xdbfa[4]](_0xdbfa[3])[_0xdbfa[6]](_0xdbfa[1])})}var _0xccc6x4,_0xccc6x5=_0xccc6x1(_0xdbfa[11]);_0xccc6x1(_0xdbfa[16])[_0xdbfa[17]](function(){_0xccc6x1(this)[_0xdbfa[4]](_0xdbfa[3])[_0xdbfa[2]](_0xdbfa[12]),_0xccc6x2(),_0xccc6x5[_0xdbfa[14]](_0xdbfa[13],!1),_0xccc6x1[_0xdbfa[15]](_0xccc6x1(this)[_0xdbfa[8]]())=== !1?(_0xccc6x1(this)[_0xdbfa[4]](_0xdbfa[3])[_0xdbfa[6]](_0xdbfa[12]),_0xccc6x5[_0xdbfa[14]](_0xdbfa[13],!1)):_0xccc6x1(this)[_0xdbfa[4]](_0xdbfa[3])[_0xdbfa[2]](_0xdbfa[12]),_0xccc6x4= 0,_0xccc6x1(_0xdbfa[16])[_0xdbfa[9]](function(){_0xdbfa[7]!= _0xccc6x1(this)[_0xdbfa[8]]()&& _0xccc6x1[_0xdbfa[15]](_0xccc6x1(this)[_0xdbfa[8]]())!== !1|| _0xccc6x4++}),_0xccc6x4> 0&& _0xccc6x5[_0xdbfa[14]](_0xdbfa[13],!0)}),_0xccc6x5[_0xdbfa[30]](_0xdbfa[18],function(_0xccc6x2){_0xccc6x2[_0xdbfa[19]]();var _0xccc6x4,_0xccc6x5,_0xccc6x6,_0xccc6x7,_0xccc6x8;_0xccc6x5= parseFloat(_0xccc6x1(_0xdbfa[20])[_0xdbfa[8]]()),_0xccc6x6= parseFloat(_0xccc6x1(_0xdbfa[21])[_0xdbfa[8]]()),_0xccc6x7= parseFloat(_0xccc6x1(_0xdbfa[22])[_0xdbfa[8]]()),_0xdbfa[23]== _0xccc6x1(_0xdbfa[24])[_0xdbfa[8]]()&& (_0xccc6x7*= 1e3),_0xccc6x8= 4* Math[_0xdbfa[25]]/ 1e7,_0xccc6x7*= Math[_0xdbfa[26]](10,6),_0xccc6x5*= Math[_0xdbfa[26]](10,-6),_0xccc6x4= Math[_0xdbfa[27]](_0xccc6x5/ (Math[_0xdbfa[25]]* _0xccc6x7* _0xccc6x6* _0xccc6x8)),_0xccc6x4*= Math[_0xdbfa[26]](10,5),_0xccc6x1(_0xdbfa[29])[_0xdbfa[8]](parseFloat(_0xccc6x4)[_0xdbfa[28]](4)),_0xccc6x3()}),_0xccc6x1(_0xdbfa[46])[_0xdbfa[30]](_0xdbfa[31],function(){_0xccc6x2();var _0xccc6x3=_0xccc6x1(this)[_0xdbfa[8]](),_0xccc6x4=_0xccc6x1(_0xdbfa[20]),_0xccc6x5=_0xccc6x1(_0xdbfa[21]);_0xccc6x4[_0xdbfa[14]](_0xdbfa[13],!0),_0xccc6x5[_0xdbfa[14]](_0xdbfa[13],!0),_0xdbfa[32]== _0xccc6x3?(_0xccc6x4[_0xdbfa[8]](_0xdbfa[33]),_0xccc6x5[_0xdbfa[8]](_0xdbfa[34])):_0xdbfa[35]== _0xccc6x3?(_0xccc6x4[_0xdbfa[8]](_0xdbfa[36]),_0xccc6x5[_0xdbfa[8]](_0xdbfa[37])):_0xdbfa[38]== _0xccc6x3?(_0xccc6x4[_0xdbfa[8]](_0xdbfa[39]),_0xccc6x5[_0xdbfa[8]](_0xdbfa[23])):_0xdbfa[40]== _0xccc6x3?(_0xccc6x4[_0xdbfa[8]](_0xdbfa[41]),_0xccc6x5[_0xdbfa[8]](_0xdbfa[42])):_0xdbfa[43]== _0xccc6x3?(_0xccc6x4[_0xdbfa[8]](_0xdbfa[44]),_0xccc6x5[_0xdbfa[8]](_0xdbfa[45])):(_0xccc6x4[_0xdbfa[8]](_0xdbfa[7]),_0xccc6x5[_0xdbfa[8]](_0xdbfa[7]),_0xccc6x4[_0xdbfa[14]](_0xdbfa[13],!1),_0xccc6x5[_0xdbfa[14]](_0xdbfa[13],!1))}),_0xccc6x1(_0xdbfa[24])[_0xdbfa[30]](_0xdbfa[31],function(){_0xccc6x2()})}(jQuery)
</script>

## Aplicações do Skin Depth

Skin depth é uma forma conveniênte para identificar a região do condutor em que a a maior parte da corrente flui. É desnecessário (ou, em alguns casos, desperdício) usar um fio com um raio significativamente maior que o Skin Depth, porque a maior parte da corrente flui na região da Skin Depth, independentemente do tamanho do condutor.

O conceito de Skin Depth, pode ser melhor apreciado com a ajuda de um exemplo do mundo real. Considere sinais de RF para WiFi ou Bluetooth, que operam a 2,4 GHz. Usando a calculadora, vemos que a skin depth de um condutor de cobre é de 1,331 micrômetros. Isso significa que, mesmo com um fio muito fino (por exemplo, 30 AWG), apenas uma pequena fração do fio transporta uma quantidade significativa de corrente.

## Referências

* https://www.allaboutcircuits.com/tools/skin-depth-calculator/ 
* [Textbook - Introduction to Conductance and Conductors](https://www.allaboutcircuits.com/textbook/direct-current/chpt-12/introduction-conductance-and-conductors/)
* [Textbook - Skin and Proximity Effects of AC Current](https://www.allaboutcircuits.com/technical-articles/skin-and-proximity-effects-of-ac-current/)
* [Worksheet - Fundamentals of Radio Communication](https://www.allaboutcircuits.com/worksheets/fundamentals-of-radio-communication/)
