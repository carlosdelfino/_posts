---
redirect_from: /latex/mathjax/introducao/mathjax_latex_representacao_matematica/
title: Usando o MathJax para representar formulas matemáticas em Latex 
tags: [Latex, MathJax, Jekyll, Matemática, Formulas, como usar, tutorial, resumo]
categories: [Matematica, MathJax]
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
  teaser: matematica/mathjax_logo.jpg
  feature: matematica/mathjax_logo.jpg
math: true
---

Um breve tutorial baseado no que foi publicado no site Stack Exchange (meta) que demonstra as práticas mais relevantes e mais usadas para escrita de fórmulas matemáticas usando o MathJax, porém adaptad para meu site com Jekyll.

<!--more-->

## Entendendo como foi escrita

Para ver como a fórmula é escrita onde se usa o MathJax basta clicar com o botão direito sobre a fórmula e selecionar "Show Match As > Tex Commands".

## escrevendo fórmulas

Para escrever formulas na linha de texto, coloque a formula usando o formato Latex do Mathjax entre `$...$`. Já para exibir formulas mais complexas faça uma seção a parte usando dois `$$` como delimitador:

```
$$
...
$$
```

Por exemplo veja a mesma formula _em linha_ a seguir `$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$`: $\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$ ou então digite assim `$$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$$` para exibir como abaixo:

$$
\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}
$$

## Lidando com conflitos com o Jekyll

A forma mais simples de evitar conflitos com o jekyll é colocar tudo que for escrito entre as tags `\{\% raw \%\}` ... `\{\% endraw \%\}`, para que o conteúdo não seja interpretado.

Porém eu testei e não funcionou no meu Jekyll, então ainda estou em busca da solução.

## Inserindo letras gregas

*  `\alpha`, `\beta`, ..., `\omega`: $\alpha$, $\beta$, ... $\omega$.  veja que basta usar o nome da letra grega prefixado com barra invertida.
* Se quiser a letra grega em maiúscula use `\Gamma`, `\Delta`, …, `\Omega`: $\Gamma, \Delta, …, \Omega$.

No artigo [Letras Gregas escrito em Agosto de 2016]({{site.url}}{% post_url 2016-08-11-letras-gregas %}), listo além de letras gregas muitos outros símbolos matemáticos.

## Superescrito ou subescrito

Para inserir  **superescrito e subescrito**, use o sinal de circunflexo `^` e traço baixo `_`.  por exemplo, `x_i^2`: $x_i^2$, `\log_2 x`: $\log_2 x$.

## Agrupamentos

Um agrupamento pode ser apenas um símbolo ou uma fórmula envolvida por _chaves_ `{`...`}`. Se você escrever `10^10`, você irá obter $10^10$. Mas se você escrever `10^{10}` ai sim você irá obter o que deseja $10^{10}$, isso ocorre porque ele considera apenas o primeiro digito do número como símbolo. 

Use então as _chaves_ `{`...`}` para delimitar tudo que deseja subescrever ou sobescrever. Assim você pode evitar surpresas. Veja outro exemplo: `X^5^6`, assim poderá ter um erro; talvez você queria algo assim: `{x^y}^z` ou seja ${x^y}^z$, e para esta `x^{y^z}`  seria $x^{y^z}$, talvez envolver com parenteses possa ajudar a leitura `{(x^y)}^z` ou seja ${(x^y)}^z$, e para esta `x^{(y^z)}`  seria $x^{(y^z)}$

Veja também este outro exemplo e veja as diferenças: `x_i^2` $x_i^2$ e `x_{i^2}` $x_{i^2}$, com parenteses  `x_{(i^2)}` $x_{(i^2)}$, 

Vamos ver mais sobre parenteses a seguir.

## Parenteses

Parenteses e Colchetes `()[]` são usados como são na escrita comum, assim a formula `$(2+3)[4+4]$` resulta em $(2+3)[4+4]$, caso precise de usar chaves sem ser para agrupamentos de simbolos como acima você deve usar precedido com a barra de escape (barra invertida) `\{` e `\}` obtendo assim: $\{\}$.

Há também parenteses invisíveis denotados por `.`: `\left.\frac12\right\rbrace` resulta (obseve `\frac12`, vamos falar abaixo sobre este uso.) $\left.\frac12\right\rbrace$.

temos também `\langle` e `\rangle` $\langle x \rangle$

## Escalonamento 

a representação visual pode ter a escrita escalonada, para auxiliar neste processo há alguns macetes. Por exemplo escrevendo uma fórmula como esta `(\frac{\sqrt x}{y^3})` os parenteses podem ficar muito pequenos: $(\frac{\sqrt x}{y^3})$. Para resolver tal problema use `\left(`...`\right)` assim você vai obte parenteses que se ajustam automáticamente a fórmula envolvida: `\left(\frac{\sqrt x}{y^3}\right)` se torna $\left(\frac{\sqrt x}{y^3}\right)$.

O prefixo  `\left` e`\right` pode ser aplicado a todos os tipos  de parenteses e contornos: `(` e `)` $(x)$, `[` e `]` $[x]$, `\{` e `\}` $\{x\}$.

Se você precisar fazer  um ajuste manual do tamanho use: `\Biggl(\biggl(\Bigl(\bigl((x)\bigr)\Bigr)\biggr)\Biggr)` o que resulta: $\Biggl(\biggl(\Bigl(\bigl((x)\bigr)\Bigr)\biggr)\Biggr)$.

## Espaços

O MathJax decide automáticamente como adicionar espaço as formulas, usando um complexo conjunto de regras. `a_b` e `a____b` supondo que "_" sejam os espaços serão tratados da mesma forma e o resultado será $a          b$. Para colocar espaços extras nas formulas não basta inserir espaços comuns, para adiioncar mais espaços, é preciso usar `\,` (virgula) para um pequeno espaço $a\,b$ e `\;`  para espaçamentos mais largos $a\;b$, você pdoe repetir a tag `\;` par aobter mais espaços $a\quad b$. já as tags `\quad` e `\qquad` são espaços largos: $a\quad b\qquad c$

## Inserindo textos explicativos

Para inserir texto plano, que podem atuar como explicátivos usem `\text{…}`, por exemplo: 

$$
\{x\in s\mid x\text{ é um valor exremamente elevado}\}
$$

É possivel usar `$…$` encadeado dentro de `\text{…}`.

## Módulo e Limites 

O simbolo de módulo, `|` resultando $\vert x \vert$, usando o jekyll deve ser obtido com  `\vert` ou `\Vert` $\Vert x \Vert$, 

Para arredondamentos temos  `\lceil` e `\rceil` $\lceil x \rceil$,  `\lfloor` e `\rfloor` $\lfloor x \rfloor$. 

`\mid` pode ser usado para divisões adicionais {% raw %} $\mathrm{E}(\,X\mid X>0\,)$ {% endraw %}.

## Frações

Frações podem ser escritas de duas formas. `\frac ab` é aplicado aos dois próximos grupos de simbolos, e produz algo assim $\frac ab$; veja que se você fizer `\frac 478` ela irá apenas mostrar a divisão de 4 por 7 e o 8 seguindo a fração assim $\frac 478$, para fazer uma fração com números longos ou mais complexa no numerador ou no denominador use o agrupamento `{`...`}`: `\frac{a+1}{b+1}` assim terá $\frac{a+1}{b+1}$. se o numerador ou denominador são complicados você pode preferir usar `\over`, que divide o grupo no qual ele pertence `{a+1\over b+1}` resultado em ${a+1\over b+1}$.

## Somas e Integrais

Somas e integrais devem usar as tags `\sum` e `\int`; o sobreescrito o limite inferior e o superescrito é o limite superior, por exemplo `\sum_1^n` $\sum_1^n$. não se esqueça que `{`...`}` devem ser usados para limites mais complexos. Por exemplo: `\sum_{i=0}^\infty i^2` para que tenhamos $\sum_{i=0}^\infty i^2$. Similarmente `\prod` $\prod$, `\int` $\int$, `\bigcup` $\bigcup$, `\bigcap` $\bigcap$, `\iint` $\iint$, `\iiint` $\iiint$.

## Sinais de Radiciação

Use o `sqrt`, que auto ajusta para seus argumento: `sqrt{x^3}` $\sqrt{x^3}$; `\sqrt[3]{\frac xy}` $\sqrt[3]{\frac xy}$.

## Funções especiais

Funções como "lim", "sin", "max", "ln", e outras são normalmente definidas para usar a fonte "roman" no lugar de fonte "italica". Use `\lim`, `\sin`, etc. Assim: `\sin x` $\sin x$, e não `sin x` $sin x$. 

No caso de limite use subescrito para anexar a anotação para `\lim`: `\lim_{x\to 0}` 

$$
\lim_{x\to 0}
$$

## Acentos e marcas diacriticas

Use `\hat` para um simbolo simples como o circunflexo, $\hat x$, `\widehat` para uma formula mais longa  $\widehat{xy}$. Se você fizer uma formula muito longa irá ser inútil. 

De forma similar, temos  `\bar` $\bar x$ e `\overline` $\overline{xyz}$, e `\vec` $\vec x$ e `\overrightarrow` $\overrightarrow{xy}$ e `\overleftrightarrow` $\overleftrightarrow{xy}$. 

Para pontos, como em $\frac d{dx}x\dot x =  \dot x^2 +  x\ddot x$,  use `\dot` e `\ddot`.

## Caracteres Especiais

Os caracteres especiais usados no MathJax podem ser ignorados usando a barra invertida `\`: `\$` $\$$, `\{` $\{$, etc. Se você deseja a própria barra `\`, você deve usar `\backslash` $\backslash$, porquê `\\` é para inserir uma nova linha. 

## Fonts 

Use `\mathbb` ou `\Bbb` para "blackboard bold": 

  $\mathbb{CHNQRZ}$.

Use `\mathbf` para boldface: 

  $\mathbf{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$

  $\mathbf{abcdefghijklmnopqrstuvwxyz}$.

Use `\mathtt` para "typewriter" font: 

  $\mathtt{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$ 

  $\mathtt{abcdefghijklmnopqrstuvwxyz}$.

Use `\mathrm` para roman font: 

  $\mathrm{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$  

  $\mathrm{abcdefghijklmnopqrstuvwxyz}$.

Use `\mathsf` para sans-serif font: 

  $\mathsf{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$  

  $\mathsf{abcdefghijklmnopqrstuvwxyz}$.

Use `\mathcal` para "calligraphic" letters: 

  $\mathcal{ ABCDEFGHIJKLMNOPQRSTUVWXYZ}$ 

Use `\mathscr` para script letters: 

  $\mathscr{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$

Use `\mathfrak` para "Fraktur" (old German style) letters: 

  $\mathfrak{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$

  $\mathfrak{abcdefghijklmnopqrstuvwxyz}$.

## Simbolos Especiais e Notações

Há um grande número de simbolos especiais e notações que podem ser usados aqui, é impossível lista-los aqui, por tanto veja esta [lista no arquivo clicando aqui](http://pic.plover.com/MISC/symbols.pdf), ou nesta [outra lista](https://www.ctan.org/tex-archive/info/symbols/comprehensive/symbols-a4.pdf). Alguns dos mais comuns estão abaixo: 
  * `\lt \gt \le \ge \neq` $\lt\, \gt\, \le\, \ge\, \neq$.  você pode usar `\not` para colocar uma barra sobre alguns: `\not\lt` $\not\lt$, mas pode não ficar legal.
  * `\times \div \pm \mp` $\times\, \div\, \pm\, \mp$. `\cdot` é o ponto centralizado: $x\cdot y$
  * `\cup \cap \setminus \subset \subseteq \subsetneq \supset \in \notin \emptyset \varnothing` $\cup\, \cap\, \setminus\, \subset\, \subseteq \,\subsetneq \,\supset\, \in\, \notin\, \emptyset\, \varnothing$ 
  * `{n+1 \choose 2k}` or `\binom{n+1}{2k}` ${n+1 \choose 2k}$ 
  * `\to \rightarrow \leftarrow \Rightarrow \Leftarrow \mapsto` $\to\, \rightarrow\, \leftarrow\, \Rightarrow\, \Leftarrow\, \mapsto$
  * `\land \lor \lnot \forall \exists \top \bot \vdash \vDash` $\land\, \lor\, \lnot\, \forall\, \exists\, \top\, \bot\, \vdash\, \vDash$
  * `\star \ast \oplus \circ \bullet` $\star\, \ast\, \oplus\, \circ\, \bullet$ 
  * `\approx \sim \simeq \cong \equiv \prec \lhd` $\approx\, \sim \, \simeq\, \cong\, \equiv\, \prec, \lhd$. 
  * `\infty \aleph_0` $\infty\, \aleph_0$ `\nabla \partial` $\nabla\, \partial$ `\Im \Re` $\Im\, \Re$
  * para equivalência modular, use `\pmod` como esta: `a\equiv b\pmod n` $a\equiv b\pmod n$.
  * `\ldots` são o pontos em $a_1, a_2, \ldots ,a_n$ `\cdots` são os pontos em  $a_1+a_2+\cdots+a_n$
  * Algumas letras Greek tem variações de forma:
`\epsilon \varepsilon` $\epsilon\, \varepsilon$, `\phi \varphi` $\phi\, \varphi$, e outras. o "l"  minúsculo e cursivo é `\ell` $\ell$.

O site [Detexify](http://detexify.kirelabs.org/classify.html) permite você desenhar um simbolo na página web e então ele lhe lista os simbolos $\TeX$ que ele julga parecer.  Não há garantias que as sugestões vão funcionar, mas é muito útil para encontrar os nomes dos simbolos até mesmo em outros casos. Para verificar se o comando é suportado, observe que o Matjax.org mantem uma [lista de lista comandos do $\LaTeX$ suportados](http://docs.mathjax.org/en/latest/tex.html#supported-latex-commands), e também há o site do Dr. Carol JVF Burns's [Comandos $\TeX$ disponível no MathJax](http://www.onemathematicalcat.org/MathJaxDocumentation/TeXSyntax.htm).


Estou ampliando o arquivo aos poucos, deixe suas dúvidas abaixo!
{: .notice }


## Referências


* https://math.meta.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference
* (Deutsch: [MathJax: LaTeX Basic Tutorial und Referenz](https://www.mathelounge.de/509545/mathjax-latex-basic-tutorial-und-referenz-deutsch))
* https://www.mathjax.org/