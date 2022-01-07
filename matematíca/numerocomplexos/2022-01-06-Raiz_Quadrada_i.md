---
date: 2022-01-05 20:00 +0300
title: Raiz Quadrada de 'i'
categories: [matematica,complexos]
layout: article
tags: [matemática, complexo, softisticados, imaginarios]
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

Aprendendo a obter a Raiz Quardada de _i_.

<!--more-->

Como todos sabem eu me forme no supletivo em 1998, o que me trouxe uma grande lacuna na minha formação matemática, eu até avanço bem em muitos conceitos matemáticos, porém muitas vezes escorrego e preciso rever alguns estudos, e agora estou revendo os cálculos com números complexos para me aprofundar nos estudos em Analise de "Sistemas e Sinais" no livro do Alan V. Oppenheim, "Signals and Systems".

O Material abaixo é uma tradução livre, acrecido com meus textos e observações,  do conteúdo do site [Milefoodt Matematics - Square Root i](http://www.milefoot.com/math/complex/squarerootofi.htm).

E preciso lembrar que o número imaginário _i_, representa $\sqrt{-1}$. Nos não podemos descrever $\sqrt{-1}$ com um número real, já que o quadrado de um número positivo é positivo, e o quadrado de um número negativo é também positivo. Não há número real ao quadrado que seja negativo. A raiz quadrada de número real nem sempre é um número real. Acontece que $\sqrt{-1}$ é um número bastante curioso, que pode ser encontrado em **Números Imáginários**.

Mas você tem pensado sobre $\sqrt{-1}$? Nós não precisamos de um j, ou alguma outra invenção para descreve-lo? No momento não. Acontece que $\sqrt{-1}$ é outro número complexo. não precisamos de um _j_.

Quando nos primeiro encontramos o número _i_, nós também aprendemos sobre números complexos, ou números na forma $a + bi$.

Acontece que raizes quadradas de números complexos são sempre raizes quadradas de outros números complexos.

Considere $2+3i$ por enquanto. nos podemos obter o quadrado deste número: 

$$
(2 + 3i)^2 = (2 + 3i)(2 + 3i) = 4 + 6i + 6i + 9i^2 = 4 + 12i -9 = -5 + 12i
$$

Portando, a raiz quadrada de $-5 * 12i$ é $2 + 3i$. portanto, agora nos temos demonstrado um caso onde a raiz quadrada de um número compleox é outro número complexo.

## Uma derivação Algebrica

Portando, permita assumir que há um número $a + bi$ que representa $\sqrt{-1}$. Desde que:

$$
(a+bi)^2 = a^2+2abi+b^2 i^2 = (a^2-b^2)+(2ab)i
$$

E desde que este resultado deve ser igual ao número $i$, nos obtemos o seguinte sistema de equações:

$$
\left\{ \begin{align}
a^2-b^2 &=0 \\
 2ab &=1
\end{align} \right.
$$

Quando a primeira equação é resolvida para a variável `a`, nos aprendemos que a variável `a` e `b` são ambas iguais e opostas. A partir da segunda equação, sabemos que o produto é uma metade positiva, porém a variável `a' e `b` devem ser iguais. (se elas são opositivas, o produto devem ser negativos.)

Portanto, a seguinda equação se torna $2a^2=1$, que tem a solução $a = \pm \dfrac{\sqrt{2}}{2}$.

Portanto, o número _i_ tem duas raizes quadradas (exatamente como dois números positivos fazem). Eles são $\dfrac{\sqrt{2}}{2} + \dfrac{\sqrt{2}}{2} i$ e $-\dfrac{\sqrt{2}}{2} - \dfrac{\sqrt{2}}{2} i$, você pode verificar ambos. eles funcionam!)

## Conclusões

Quando você se tornar acostumado com a ideia que _i_ é exatamente outro número, então ele se torna fácil de aceitar a ideia de usa-lo como número, ou qualquer outro número imaginário, na maioria das operações que você usa um número real. Você pode ler mais sobre Raizes quadras de números imaginários, Usando Algebra. Ou ler mais sobre encontranso maneiras de ralacionar raizes entre números imaginários e Trigonometria.

Futuramente farei anotações sobre tais estudos, incluindo traduções e exercicios com códigos.



