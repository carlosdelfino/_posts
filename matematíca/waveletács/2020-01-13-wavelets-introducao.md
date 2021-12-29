---
title: Introdução aos Wavelets
tags: [Wavelet, Haar, Daubechies, Biorthogonal, Coiflats, Symlets, Morlet, Mexican Hat, Meyer, octave, DSP, Signal, Signal Processing, BMD101, BD101, NeuroSkY]
categories: [Matematica, Wavelets]
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
   teaser: matematica/wavelets/wavelets.jpg
   feature: matematica/wavelets/wavelets.jpg
math: true
images:
  dir: /images/matematica/wavelets/
---

Wavelets são formas de onda efetivamente limitadas num curto espaço de tempo, que tem uma média de valores entre zero e não zero.

<!--more-->

Revisado em 27/12/2021
{: .notice }

Wavelets tendem a ser irregulares, não mantem uma recorrência como sinais senoidais ou composições de sinais senoidais, como proposto pelas Analises de Furrier. Porém, pode ser que algum sinal seja composto pela repetição de wavelets, o que pode gerar um pouco de confusão, mas vamos tentar entender um pouco isso abaixo.

Bem, se você está procurando a Banda Wavelets, não iremos falar dela aqui, você pode obter [mais informações neste link, mas acho que ela se aposentou](http://bit.ly/wavelets). Não poderia deixar de cita-la.

Na categória [wavelets]({{site.url}}/wavelets/) você vai encontrar todas as anotações que eu tenho feito sobre o tema.

Estou trabalhando nestas anotações, estudos retomados em 13/01/2020. E novamente retomado em 27/12/2021.
{: .notice-warning}

## Motivação

Meus estudos com Wavelets se deram mais intensos em 2017, quando comecei a busca de um algoritmo para detecção e analise de sinais de ECG (Eletrocardiograma), eu precisava além de identificar a pulsação do coração, identificar suas condições médicas através dos sinais obtidos. O sinal é coletado com o chip BMD101, um chip especializado da NeuroSky para coleta de ECG, porém precisava de mais detalhes, e com os dados puros (RAW) do ECG poderia fazer a analise, inicialmente pensei em usar Transformatas de Fourrier, porém, me pareceu muito pesado e inadequado para o uso, apesar de muitos indica-la como sendo o caminho.

De certa forma era o caminho, delas cheguei aos Wavelets, pequenas amostragens vetoriais e ortogonais que representam um formato de onda que desejo obter na amostragem, através do wavelets poderia procurar a "agulha no palheiro", ou seja aqueles pulsos que demonstram que algo está errado no coração, saber a taxa de pulsação do coração não seria suficiente, eu precisava saber de sua variabilidade e também a deformação de tais pulsos, além de a presença de outros pulsos que poderiam sugerir outras anormalidades.

O Wevelets me pareceu ser o caminho, ele me ajudaria de forma discreta e com um algoritmo rápido achar pulsos que estejam fora do comum, mas parece que falta algo mais, e não só dominar o tema seria suficiente.

Bem o projeto já não está mais sobre minha responsabilidade, mas minhas pesquisas continuam, e eu tenho buscado agora retomar os estudos especificamente sobre wavelets de forma geral, e entender sua aplicação e como construir novos algoritmos com base no contexto aplicado. 

## Preparando o Ambiente

Para desenvolver tal estudo, eu precisei preparar minha estação de trabalho, inicialmente com dois software, o **Octave** e o **VScode**, o primeiro é uma ferramenta simulações e calculos matemáticos similar ao *Mathlab* que permite desenvolver scripts matemáticos, produzida pela projeto GNU é _Open Source_ e gratuita, muito útil para o estudo e avanço no domínio da matemática, o segundo talvez mais conhecido é uma IDE aberta produzida pela Microsoft que permite integrar plugins e ferramentas como o Octave, entre muitas outras. Para usar o Octave junto com o VScode usei o plugin disponível neste link](https://marketplace.visualstudio.com/items?itemName=toasty-technologies.octave). Porém o editor "VI" pode também ser usado sem integração. O próprio Octave pode ser usado como editor através de sua interface de usuário grafica.

Para instalar ambas as ferrametnas use os links abaixo, ou então as ferramentas de instalação do sistema operacional que usa.

* [Clique aqui para baixar o Octave](https://www.gnu.org/software/octave/download.html)
* [E aqui para obter o VSCode](https://code.visualstudio.com/download)

Bem, instale ambos os programas, depois dentro do VSCode instale o plugin, não irei entrar em detalhes quanto a esta instalação.

Para usar o Octave no Linux o processo de instalação pode ser diferente, não irei entrar em detalhes para este processo de instalação, mas farei algumas anotações no [Instalando Octave no Linux]({{site.url}}{% post_url 2021-12-27-instalando-octave-no-linux %}).

<figure class="image">
  <img src="{{site.url}}/{{page.images.dir}}/ltflat-wavelet-plotwavelet-db8.png" alt="Um exemplo simples de uso da função FWT para wavelet 'db8'" >
  <figcaption> Um exemplo simples de uso da função FWT para wavelet 'db8' </figcaption>
</figure>

----
A imagem acima é obtida usando o script a seguir:

<code>
    [f,fs] = greasy;
    J = 10;
    [c,info] = fwt(f,'db8',J);
    plotwavelets(c,info,fs,'dynrange',90);
</code>
----

Para darmos continuidade, será preciso instalar alguns _toolbox_ no Octave. Iremos inicialmente instalar o [**Signal Processing**](https://octave.sourceforge.io/signal/index.html), este toolbox possui divresas funções que auxiliam em **DPS** e o [**LTFAT - Larger Timer/Frequency Analise Tools**](https://octave.sourceforge.io/ltfat/index.html), um toolbox que pode ser usado tanto para pesquisas acadêmicas como profissionais.

Para instalar ambos é muito simples abra o *Octave Cli*, e execute o seguinte comando, _atenção a detalhes no linux apresetado no artigo acima_:

```
pkg install signal ltfat
```

conforme o desempenho de sua estação de trabalho e de sua internet irá demorar alguns minutos e assim que terminar, correndo tudo bem, execute os seguintes comandos para ver uma Wavelet do tipo morlet:

```
pkg load signal

lb = -4;
ub = 4;
n = 1000;
[psi,xval] = morlet(lb,ub,n);
plot(xval,psi,"linewidth",4)
grid on
```

Os comandos acima executam o seguinte, primeiro carrega o toolbox *Signal Processing*. Declara três variáveis chamadas, `lb`, `ub`, `n` respectivamente, então chama a função `morlet` que irá gerar uma estrutura com os dados para reconstrução visual do [Wavelet do tipo Morlet]({{site.url}}{% post_url 2020-01-14-morlet-wavelets %}),, finalmente é chamado a função que irá plotar gráficamente o nosso Wavelet com o grid ligado no gráfico. (veja em referências onde obtive o exemplo.)

Como pode ver se você já tem alguma noção de programação, é bastante intuitivo. Teremos o seguinte resultado:

![Morlet Wavelet]({{site.url}}/images/matematica/wavelets/morlet-wavelets.png)

[Para mais funções veja este link](https://octave.sourceforge.io/signal/overview.html).

Vamos fazer mais um teste com o Wavelet Mexican Hat]({{site.url}}{% post_url 2020-01-14-mexican_hat-wavelets %}), basta mudarmos a função:

```
[psi,xval] = mexihat(lb,ub,n);
plot(xval,psi,"linewidth",4)
grid on
```

Então teremos o seguinte resultado:

![Mexican Hat Wavelet]({{site.url}}/images/matematica/wavelets/mexican-hat-wavelets.png)

[Para mais detalhes sobre ferramentas clique aqui.]({{site.url}}/{% post_url 2020-01-15-wavelets_octave_entre_outros %})

## Arbitrário

> que não segue regras ou normas; que não tem fundamento lógico; que apenas depende da vontade ou arbítrio daquele que age.

Wavelets são funções arbitrárias, ou seja você pode adotar a função que quiser para definir seu formato de onda, apenas é preciso se atendar para que seja uma representação real de uma janela de amostragem.

Wavelets tem assim uma número infinito de transformações (Transformatas);

## Referências

Com o objetivo de unificar as referências desta série de artigos criei um post para todas elas, [clique aqui para ve-las.]({{site.url}}/{% post_url 2020-01-15-Wavelets_referencias %})
