---
title: Wavelets no Arduino - Sampling
tags: [Wavelet, Haar, Daubechies, Biorthogonal, Coiflats, Symlets, Morlet, Mexican Hat, Meyer, octave, DSP, Signal, Signal Processing]
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

## Analog Sampling no Arduino

O Arduino pode fazer em torno de 7000 (sete mil) leituras por segundo, em média, na porta analógica em um código comum, porém não mais que 300 (trezentas) leituras por segundo em úm código mais elaborado, ou seja, é um sampling máximo de 300Hz, claro estes valores são muito impressivos, não há como sermos determinístico, já que além de estarmos usando a porta serial (problemas com o buffer entre o arduino e o PC) e também interrupções de controle interno como a usada para contagem de milessegundos desde que o arduino foi ligado.

Poderíamos fazer um sampling em pacotes, porém para nossos testes não é interessante, pois seriam rajadas de dados, e não haveria uma constante leitura, havendo perdas durante os intervalos que serão criados no sampling para processa-lo, prepara-lo e envia-lo pela serial, neste tempo nõa haveriam coletas, mas vou analisar isso e postar abaixo os resultados.

Antes vejam o gráfico abaixo do teste que eu fiz de leitura de um piezo elétrico, o código usado está a seguir e pode ser criticado e sugerido melhorias.

![Gráfico Tempos e Resultados da primeira leitura]({{site.url}}images/matematica/wavelets/experimentos/arduino_uno/grafico3.jpg)

Abaixo o segundo inicial de leitura, representado pelo gráfio a seguir:

| Time(s) | Analog | Piezo | Piezo media | Intervalo geral(s) | Freq. geral(1Hz) | Freq. sample(1Hz) | Freq. sample media (1Hz) |
| ------- | ------ | ----- | ----------- | ------------------ | ---------------- | ----------------- | ------------------------ |
| 0.0050  |   0    |  542  |     541     |      0.0530        |      18.5185     |      7017.5439    |        7017.5439  |
| 0.0610  |   0    |  541  |     540     |      0.0530        |      18.5185     |      3508.7719    |        4678.3627  |
| 0.1170  |   0    |  540  |     540     |      0.0540        |      18.5185     |      2339.1813    |        3508.7719  |
| 0.1740  |   0    |  540  |     540     |      0.0530        |      18.8679     |      1769.9114    |        2816.9011  |
| 0.2290  |   0    |  540  |     540     |      0.0550        |      18.1818     |      1428.5715    |        2358.4904  |
| 0.2870  |   0    |  541  |     540     |      0.0560        |      17.8571     |      1186.9437    |        2025.3162  |
| 0.3460  |   0    |  541  |     540     |      0.0550        |      17.8571     |      1017.8117    |        1774.3978  |
| 0.4040  |   0    |  541  |     540     |      0.0550        |      17.8571     |       884.9558    |        1576.3546  |
| 0.4620  |   0    |  541  |     540     |      0.0560        |      17.5439     |       782.7788    |        1416.7650  |
| 0.5210  |   0    |  540  |     540     |      0.0530        |      18.8679     |       705.4674    |        1287.0010  |
| 0.5760  |   0    |  539  |     540     |      0.0530        |      18.5185     |       645.1613    |        1180.2574  |
| 0.6320  |   0    |  540  |     540     |      0.0540        |      18.1818     |       588.2353    |        1088.9291  |
| 0.6890  |   0    |  539  |     540     |      0.0530        |      18.8679     |       544.2177    |        1011.0830  |
| 0.7450  |   0    |  540  |     540     |      0.0520        |      18.8679     |       506.3291    |        943.8732  |
| 0.8000  |   0    |  540  |     540     |      0.0540        |      18.1818     |       472.2550    |        884.9558  |
| 0.8570  |   0    |  540  |     540     |      0.0530        |      18.8679     |       443.4590    |        833.1163  |
| 0.9120  |   0    |  540  |     540     |      0.0530        |      18.5185     |       417.1012    |        786.9459  |
| 0.9680  |   0    |  539  |     540     |      0.0530        |      18.8679     |       394.0887    |        745.6503  |
| 1.0250  |   0    |  540  |     540     |      0.0520        |      18.8679     |       374.1815    |        708.6246  |

![Gráfico Tempos e Resultados da primeira leitura]({{site.url}}images/matematica/wavelets/experimentos/arduino_uno/grafico3.jpg)

Este é o código inicial para nosso testes:

<script src="https://gist.github.com/carlosdelfino/609db07bf319465535086c4b576e987f.js"></script>

## Vamos melhor um pouco?

Vamos melhor o código usarmos interrupções para fazermos o sampling dos dados, usaremos um buffer-ring para obter dados da porta analógica e jogaremos em rajadas na porta sérial.


