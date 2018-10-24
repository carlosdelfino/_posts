---
title: Três Principais Conceitos do JavaScript para quem vai Programar com React
tags: [react,javascript,arrow function,this,context,conceitos,old school,programação,atualização]
categories: [Series, React, JavaScript]
portal: [carlosdelfino, arduinominas]
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
   teaser: frameworks/react_redux.png
   feature: frameworks/react_redux.png
math: false
---

Quando se é um velho programador JavaScript e se pretende dedicar a novas bibliotecas e frameworks é preciso estar ciente de três novos conceitos da linguagem que são amplamente usados.

<!--more-->

Abaixo está a tradução de um texto sugerido pela documento do ReactJS, que está disponível no [GIST como nome Modern JavaScript in React Documentation](https://gist.github.com/gaearon/683e676101005de0add59e8bb345340c), no final desta publicação você encontra o original embarcado.


## Tradução:

Se você tem trabalhado com JavaScript nos últimos anos, estes três pontos devem fornecer a você o conhecimento necessário para confortávelmente ler a documentação do React.

Nos definimos variaveis com as declarações `let` e `const`. Para a proposta da documentação do React, você pode considerar, então, equivalente a `var`.

Nos usamos diretiva `class` para definir Classes do JavaScript. Há duas coisas que devemos relembrar então. Primeiro, difernte de objetos, você não precisa colcoar virgulas entre definições de métodos de classe. Segundo, diferente de outras linguagens que abordam Classes, em JavaScript o valor do `this` em um método depende de onde ele é chamado.

Algumas vezes usamos `=>` para definir "Arrow Functions". Elas são como funções regulares, mas encurtadas. Por exempo, `x => x * 2` é mais ou menos como a função `function(x) { return x * 2;}`. Não esqueça que, _arrow functions_ não tem seu próprio valor para `this` para ele lidar quando você deseja preservar o valor `this` da definição do método mais externo.

Não se preocupe se isso for muito para assimilar de uma só vez. O **MDN JavaScript Reference** é um recurso estelar, e você pode consulta-lo sempre que você se sentir confuso com alguma coisa.

Também, quando você tiver dúvida sobre alguma nova sintaxe significa, você pode usar o **Babel REPL** com as predefinições do ES2015 para verificar qual a velha sintaxe equivalente será gerada.


<script src="https://gist.github.com/gaearon/683e676101005de0add59e8bb345340c.js"></script>