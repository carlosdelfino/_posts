---
date: 2022-01-07 22:00 +0300
title: Raiz Quadrada De Numeros Imaginarios Usando Algebra
categories: [matematica,complexos]
layout: article
tags: [matemática, complexo, softisticados, imaginarios, algebra, raiz quadrada, raiz]
comment: true
share: true
feature:
 index: true
 category: true
ads:
 show: true
math: true
coinbase:
  show: false
image:
   teaser: /extra/ensino_de_matematica_com_contexto_e_aplicacoes-400x267.jpg
   feature: /extra/formulas_matematicas-1200x778.jpg
---

Aprendendo a obter a Raiz Quardada de _i_, usando algebra, se aprofundando no assunto um pouco mais.

<!--more-->

Como todos sabem eu me formei no supletivo em 1998, o que me trouxe uma grande lacuna na minha formação matemática, eu até avanço bem em muitos conceitos matemáticos, porém muitas vezes escorrego e preciso rever alguns estudos, agora estou revendo os cálculos com números complexos para me aprofundar nos estudos em Analise de "Sistemas e Sinais" no livro do Alan V. Oppenheim, "Signals and Systems".

O Material abaixo é uma tradução livre, acrecido com meus textos e observações,  do conteúdo do site [Milefoodt Matematics - Square Root i](http://www.milefoot.com/math/complex/squarerootofi.htm).

Espero que você já tenha lido a publicação [anterior, Números Imaginários]({{site.url}}{% post_url 2022-01-05-Numeros_Imaginarios %}), onde faço uma breve introdução sobre o assunto, leia também o artigo [Raiz Quadrada _i_]({{site.url}}{% post_url 2022-01-06-Raiz_Quadrada_i %})


Para encontrarmos a $\sqrt{i}$, a raiz quadrada de i. De fato, de fato o número _i_ tem duas raiz quadradas, $\dfrac{\sqrt{2}}{2} + \dfrac{\sqrt{2}}{2} i$ e $-\dfrac{\sqrt{2}}{2} - \dfrac{\sqrt{2}}{2} i$. Porém, você pode encontrar a raiz quadrada de outros números imaginários? com certeza.

## Primeiro Exemplo

Vamos tentar encontrar a raiz quadrada de $3 + 4i$:

$$
(a+bi)^2 = a^2+2abi+b^2 i^2 = (a^2-b^2)+(2ab)i
$$

Nos precisamos resolver o sistema de equações

$$
\left\{ \begin{align}
  a^2-b^2 &=3 \\
  2ab &=4
\end{align} \right.
$$

Nos não podemos resolver este sistema tão rapidamente como quando nos encontramos a raiz quadrada da de _i_, mas pode ser feito. Resolvendo a segunda equação para a variável `b`, nos pegamos $b=\dfrac2a$. Substituimos esta quantidade na primeira equação, nos pegamos $a^2-\dfrac{4}{a^2}=3$. Anulamos a fração dada com $a^4-3a^2-4=0$. O que pode ser resolvido fatorando, com $a^2=4$ ou $a^2=-1$. Mas, nos  desejamos valores reais para a váriavel `a`, assim, descarmos $-1$. com isso, $a^2=4$, e o valor de `a` são `2` e `-2`, usando $b=\dfrac2a$, nos encontramos $b=\pm1$. obtendo as duas raizes quadradas de $3+4i$ são $2+i$ e $-2-i$.



