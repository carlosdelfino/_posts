---

redirect_from: "/basicaodaeletronica/nivel%202/O_que_e_indutor/"
title: "Indutores, Construindo o Seu!"
excerpt: Indutores são componentes amplamente usados em circuitos analógicos, especialmente em receptores ou transmissores de rádio, mas também podem ser usados em circuitos de filtragem de sinais, como filtros passa-faixa, passa-altas e passa-baixas para caixas de som. Neste artigo, falarei um pouco sobre a construção de indutores, com foco no uso em um sensor capacitivo para Arduino.
tags: [eletrônica, Eletrônica Básica, Aprendizado, Rádio, RF, Arduino, Sensor, Capacitivo, Indutor, Choque, Reator, Oscilador, Passa Banda, Filtro, Passa Baixa, Tanque, Filtro Passa Alta, Conversor Analógico Digital, ADC, ACD]
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
 show: true
---

## O que são Indutores e seu funcionamento

Os indutores são componentes eletrônicos de fácil fabricação, construídos com diversos loops de fio, que formam um campo magnético que interfere no fluxo da corrente.

Quando a corrente passa pelo fio, ela induz um campo magnético que, ao ser interceptado pelo próprio fio condutor, interfere no fluxo da corrente.

<img src="/images/basicaodaeletronica/indutores/indutor_basico_e_ideal-364x200.png" />

Como o indutor é composto de diversos loops, a própria corrente sobre sua estrutura causa interferência em seu fluxo pelo campo magnético que ela mesma forma.

_Com isso, uma corrente contínua que passa através do indutor produz um campo magnético constante, assim a corrente continua a fluir livremente através do condutor. Porém, ao sofrer variação na corrente, o campo magnético também sofre alteração, fazendo com que uma corrente alternada em determinada frequência passe a sofrer resistência. Quanto mais elevada a frequência de oscilação da corrente, maior será a resistência, que pode ser medida como reatância e tem uma defasagem de 90 graus._

## Indutor ou Bobina?

Os indutores possuem outros nomes que também são adotados na literatura e podem variar conforme o circuito aplicado.

O termo Bobina, Choque e até mesmo Reator referem-se ao mesmo componente.

<img src="/images/basicaodaeletronica/indutores/magnetic_ballasts_237x200.jpg" />

Os _Reatores_ são um tipo de bobina utilizados especialmente em lâmpadas fluorescentes. São grandes e pesados e hoje, em muitos casos, são substituídos por circuitos especiais, cuja função é regular o fluxo de corrente.

<img src="/images/basicaodaeletronica/indutores/common-mode-choke-800x308.png" />

O termo _Choke_ ou _Choque_ vem do inglês _choking_, que significa bloquear, quando usado em filtros que eliminam altas frequências. Os choques são normalmente construídos em pares de forma que a carga imposta a eles tenha os dois polos ligados a cada bobina que estão em série com o circuito, ao contrário dos transformadores, que têm a bobina em paralelo com a carga e a fonte.

Já o termo bobina se refere apenas ao fato de um indutor ser um fio enrolado como uma bobina.

## Formatos de Indutores

Todos os indutores têm um formato similar, podendo variar na forma do enrolamento, seu núcleo e se são do tipo duplo.

<img src="/images/basicaodaeletronica/indutores/indutor_nucleo_ar-219x200.png" /> 
Os indutores podem ser enrolados sobre o ar ou si mesmos, sendo chamados simplesmente de "Indutores de Núcleo de Ar". Tais indutores não possuem núcleo, ou quando possuem são feitos de materiais que não possuem propriedades magnéticas como plástico, madeira ou resina. Tais núcleos, quando usados, têm apenas a função de dar suporte mecânico.

Há também os indutores que são construídos sobre suportes metálicos como ferro doce, usados em transformadores. São pesados e usados para altas tensões.

Já os indutores construídos sobre ferrite são usados para altas frequências, muito comuns em receptores ou transmissores de rádio ou circuitos de alta frequência.

Há indutores que são construídos de forma a terem seu núcleo ajustável, alterando suas características conforme o núcleo é inserido na bobina.

<img src="/images/basicaodaeletronica/indutores/indutor_nucleo_ferrite-248x200.png" />

Há indutores de ferrite que são construídos também em formato toroidal. São compactos e de excelente estabilidade. Uma das grandes qualidades dos indutores toroidais é o fato de sofrerem menor interferência de outros indutores próximos e também não causarem interferências em outros componentes e linhas de corrente, sendo chamados de indutores auto-blindados.

## Identificando os Valores dos Indutores

Como citado acima, os indutores podem ter diversos formatos, mas o que é mais relevante é seu comportamento elétrico, medido em Henries, chamado de indutância. Alguns incluem também um valor paralelo em Ohms, já que podem ser construídos sobre resistores ou oferecer tal característica ôhmica devido ao diâmetro do fio. Porém, indutores possuem também a reatância indutiva, como os capacitores possuem a reatância capacitiva, e esta reatância está defasada em 90 graus com relação à fase da corrente alternada aplicada sobre eles. Não entraremos em detalhes neste artigo sobre o assunto. Portanto, consideraremos aqui apenas seu valor medido em Henries e assumiremos os demais valores como sendo de um componente ideal.

<img src="/images/basicaodaeletronica/indutores/escala_indutancia_multimetro_minipa_et-2082c-800x480.jpg" />

Os indutores dificilmente terão valores maiores que 100mH (cem milésimos de Henry) ou menores que 1uH (um micro Henry). No entanto, você encontrará multímetros com escalas de leituras entre 20H e 2mH e outros até menores. 

## Obtendo novos valores com associação

Os indutores, quando montados em série ou paralelo, possuem seus valores somados ou divididos da mesma forma que os resistores.

## Construindo seus Próprios Indutores

Em análise das melhores opções para construção, consideraremos aqui primariamente os indutores de núcleo de ar, já que são mais fáceis de serem construídos devido à necessidade apenas do fio. Em segunda parte, sugeriremos estudos para construção utilizando um núcleo de ferrite.

Para construir um indutor, você precisa ter algumas informações muito importantes em mãos, como as medidas físicas que pretende usar.

Essas medidas são:

* C = Comprimento da Bobina em cm
* D = Diâmetro da Bobina em cm
* L = Indutância desejada em Henry 

Com essas informações, você chegará ao número de voltas (espiras) (n) que deve dar no fio para obter o indutor adequado. Veja que você pode ajustar as fórmulas abaixo para obter outros valores, por exemplo, considerando que já sabe o número de voltas que deseja usar (o que faz pouco sentido), poderá obter o diâmetro.

Normalmente, inicia-se a construção identificando o valor desejado. Em seguida, conforme o tipo de circuito, define-se o comprimento e o diâmetro.

### O Diâmetro/Seção do Fio

O diâmetro do fio não é muito importante para circuitos de alta corrente, mas claro, evite usar fios muito grossos ou muito finos, sem um bom motivo para isso. Procure usar um fio que apenas seja suficiente para dar sustentação mecânica à bobina, já que esta será de núcleo de ar. Veja, bobinas com núcleos de plástico podem usar fios mais finos, a resistência em fios de cobre é bem baixa e, portanto, não afetará seu projeto.

Além disso, fios de maior seção que venham a tornar a bobina mais comprida irão causar uma redução na indutância, já que o campo magnético gerado por uma seção de espiras irá atingir um menor número de espiras.

O uso de fios de seção retangular pode contribuir para o ajuste do fio e melhorar seu suporte mecânico, porém as fórmulas aqui apresentadas são para fios de seção circular. Além disso, o preço de tais fios é mais elevado e são difíceis de serem encontrados.

<figure>
<img src="/images/basicaodaeletronica/indutores/fio_secao_retangular_vs_circular-600x270.png" />
<figcaption>Imagem obtida no site: <a href="http://www.mecatronicaatual.com.br/educacao/1681-transformadores-de-baixa-tenso?utm_source=carlosdelfino&utm_medium=online&utm_content=text">http://www.mecatronicaatual.com.br/educacao/1681-transformadores-de-baixa-tenso</a></figcaption>
</figure>

### Formato da Bobina



A bobina deve ser construída em um formato circular, evitando formas ovais e quadradas, mesmo que com os cantos arredondados. Fios de seção retangular podem ser usados para um melhor ajuste mecânico, mantendo as voltas bem justas. Porém, os cálculos aqui apresentados serão para fios de seção circular. Faça seus testes e ajuste a fórmula.

Não usaremos aqui bobinas toroidais, apenas bobinas com núcleo de ar em formatos lineares.

### O Diâmetro da Bobina

O diâmetro da bobina é muito importante. Para simplificar, procuramos sempre usar um diâmetro de 0,7 cm (um lápis) a 1,2 cm (diâmetro de uma pilha AA), sendo o ideal 1 cm (equivalente a uma pilha AAA).

O exemplo comparativo com lápis e pilha não significa que estes serão mantidos como núcleos, pois sua composição metálica irá interferir drasticamente no campo magnético, mudando o valor obtido na prática.

_É importante observar que aumentando o diâmetro da bobina você terá que dar menos voltas no fio, conseguindo assim maiores valores para sua indutância em um menor comprimento._

Com base no diâmetro ou mesmo no raio da bobina, você obterá o tamanho da área da circunferência que uma volta de fio terá.

### O Comprimento da Bobina

O comprimento da bobina é algo que impacta bastante em seu funcionamento, pois pode sofrer danos no manuseio, alterando seu valor. A sustentação mecânica evita que ela sofra deformação em seu diâmetro e forma circular. Já no comprimento, evita que as voltas se afastem umas das outras e que não seja linear, podendo se curvar sobre seu próprio peso. É importante para que o campo magnético atinja igualmente todos os fios.

Depois de uma bobina pronta, você pode fazer um ajuste fino, aumentando ou diminuindo o espaçamento entre os fios, porém mantendo todas as espiras no mesmo espaçamento.

### O Cálculo

Como já mencionado, o cálculo será feito para bobinas de núcleo de ar, mas com um pequeno ajuste, este cálculo poderá ser usado para bobinas de ferrite. No entanto, não trataremos desse formato inicialmente.

Consideraremos neste cálculo apenas fios de seção circular, já que fios de seção retangular podem ter densidades diferentes conforme são enrolados, resultando em uma bobina de maior ou menor comprimento, quando for preciso enrolar os fios bem juntos.

### A área da Espira

A bobina é composta por espiras, e todas devem ter o mesmo diâmetro. Para iniciar o cálculo, é preciso saber qual área cada espira irá ocupar, e a fórmula é simples e padrão, basta usar a fórmula de cálculo da circunferência.

Assim, "S", que representa na fórmula tal área, pode ser calculada com a seguinte fórmula:

<figure>
<img src="/images/basicaodaeletronica/indutores/calculo_area_circunferencia-500x300.png" />
<figcaption>
O cálculo ao lado é o cálculo padrão para obter a área da circunferência, sendo π o valor padrão da constante PI, ou seja, 3,14 (para simplificar).

O cálculo pode ser facilmente ajustado caso se tenha o diâmetro ou o raio. 
</figcaption>
</figure>

### Calculando o Número de Espiras

Para uma bobina que tenha apenas um nível de espira, ou seja, os fios não serão enrolados em camadas, deve-se usar a fórmula abaixo.

<figure>
<img src="/images/basicaodaeletronica/indutores/calculo-indutancia-800x330.png" />
<figcaption>
Finalmente, o cálculo. Na figura, temos uma bobina de comprimento "C", área da espira representada por "S". E o resultado é dado em voltas "n".
<br />
<br />
Caso queira saber qual a indutância de uma bobina com base nas medidas físicas, a segunda fórmula deve ser usada. 
</figcaption>
</figure>

## Participe de nossa comunidade
<a href="https://basicaodaeletronica.com.br/">
<figure>
<img src="https://basicaodaeletronica.com.br/wp-content/uploads/2024/07/Logo_Completo-e1721425728659.webp" />
<figcaption>
Participe na Comunidade Basicão da Eletronica e aprenda eletrônica de um jeito diferene e gratuito
</figcaption>
</figure>
</a>
## Fontes

* [https://basicaodaeletronica.com.br](https://basicaodaeletronica.com.br)
* [http://www.circuitstoday.com/how-to-make-an-air-core-inductor](http://www.circuitstoday.com/how-to-make-an-air-core-inductor?utm_source=carlosdelfino&utm_medium=online&utm_content=text)
* [http://pt.wikipedia.org/wiki/Balastro_(eletricidade)](http://pt.wikipedia.org/wiki/Balastro_(eletricidade)?utm_source=carlosdelfino&utm_medium=online&utm_content=text)
* [http://en.wikipedia.org/wiki/Choke_(electronics)](http://en.wikipedia.org/wiki/Choke_(electronics)?utm_source=carlosdelfino&utm_medium=online&utm_content=text)
* [http://pt.wikipedia.org/wiki/Indutor](http://pt.wikipedia.org/wiki/Indutor?utm_source=carlosdelfino&utm_medium=online&utm_content=text)
* [http://www.newtoncbraga.com.br/index.php/como-funciona/4097-art560](http://www.newtoncbraga.com.br/index.php/como-funciona/4097-art560?utm_source=carlosdelfino&utm_medium=online&utm_content=text)
* [http://www.newtoncbraga.com.br/index.php/artigos/49-curiosidades/4151-art572.html](http://www.newtoncbraga.com.br/index.php/artigos/49-curiosidades/4151-art572.html?utm_source=carlosdelfino&utm_medium=online&utm_content=text)
* [http://www.radioamadores.net/indutancias.htm](http://www.radioamadores.net/indutancias.htm?utm_source=carlosdelfino&utm_medium=online&utm_content=text)
