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

Um breve tutorial copiado do site Stack Exchange (meta) que demonstra as práticas mais relevantes e mais usadas para escrita de fórmulas matemáticas usando o MathJax.

<!--more-->

## Entendendo como foi escrita

Para ver como a fórmula é escrita onde se usa o MathJax basta clicar com o botão direito sobre a fórmula e selecionar "Show Match As > Tex Commands".

## escrevendo fórmulas

Para escrever formulas na linha de texto, coloque a formula usando o formato Latex do Mathjax entre `$...$`. Já para exibir formulas mais complexas faça uma seção a parte usando dois `$$` como delimitador: `$$...$$`.

Por exemplo veja a mesma formula _em linha_ a seguir `$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$`: $\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$ ou então digite assim `$$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$$` para exibir como abaixo:

$$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$$

## Lidando com conflitos com o Jekyll

A forma mais simples de evitar conflitos com o jekyll é colocar tudo que for escrito entre as tags `\{\% raw \%\}` ... `\{\% endraw \%\}`, para que o conteúdo não seja interpretado.

Vejamos, aqui nesta linha se eu escrevo `$|x|$`, ele interpreta a barra vertical como delimitador de colunas para uso em tabelas, causando problemas na formatação e deixando confuso o Mathjax e o Jekyll, portanto escreva da seguinte forma `\{\% raw \%\}` `$|x|$` `\{\% endraw \%\}` assim o resultado será o esperado {% raw %}$|x|${% endraw %}

## Inserindo letras gregas

*  `\alpha`, `\beta`, ..., `\omega`: $\alpha$, $\beta$, ... $\omega$.  veja que basta usar o nome da letra grega prefixado com barra invertida.
* Se quiser a letra grega em maiúscula use `\Gamma`, `\Delta`, …, `\Omega`: $\Gamma, \Delta, …, \Omega$.

## Supeescrito ou subescrito

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

O MathJax decide automáticamente como adicionar espaço as formulas, usando um complexo conjunto de regras. `a_b` e `a____b` supondo que "_" sejam os espaços serão tratados da mesma forma e o resultado será $a          b$. Para colocar espaços extras nas formulas não basta inserir espaços comuns, para adiioncar mais espaços, é preciso usar `\,` (virgula) para um pequeno espaço $a\,b$ e `\;`  para espaçamentos mais largos $a\;b$. já as tags `\quad` e `\qquad` são espaços largos: $a\quadb\qquadc$

## Inserindo textos explicativos

Para inserir texto plano, que podem atuar como explicátivos usem `\text{…}`, por exemplo: $\{x\in s\mid x\text{ é um valor exremamente elevado}\}$. é possivel usar `$…$` encadeado dentro de `\text{…}`.

## Módulo e Limites 

O simbolo de módulo, `|` resultando {%raw%}$|x|${%endraw%}, que também pode ser `\vert` $\vert x \vert$, ou `\Vert` $\Vert x \Vert$, 

Para arredondamentos temos  `\lceil` e `\rceil` $\lceil x \rceil$,  `\lfloor` e `\rfloor` $\lfloor x \rfloor$. 

`\middle` pode ser usado para divisões adicionais $2\middle|2\middle(1$.

## Frações

Frações podem ser escritas de duas formas. `\frac ab` é aplicado aos dois próximos grupos de simbolos, e produz algo assim $\frac ab$; veja que se você fizer `\frac 478` ela irá apenas mostrar a divisão de 4 por 7 e o 8 seguindo a fração assim $\frac 478$, para fazer uma fração com números longos ou mais complexa no numerador ou no denominador use o agrupamento `{`...`}`: `\frac{a+1}{b+1}` assim terá $\frac{a+1}{b+1}$. se o numerador ou denominador são complicados você pode preferir usar `\over`, que divide o grupo no qual ele pertence `{a+1\over b+1}` resultado em ${a+1\over b+1}$.

## Somas e Integrais

Somas e integrais devem usar as tags `\sum` e `\int`; o sobreescrito o limite inferior e o superescrito é o limite superior, por exemplo `\sum_1^n` $\sum_1^n$. não se esqueça que `{`...`}` devem ser usados para limites mais complexos. Por exemplo: `\sum_{i=0}^\infty i^2` para que tenhamos $\sum_{i=0}^\infty i^2$. Similarmente `\prod` $\prod$, `\int` $\int$, `\bigcup` $\bigcup$, `\bigcap` $\bigcap$, `\iint` $\iint$, `\iiint` $\iiint$.


Estou editando o arquivo aos poucos aguarde!
{: .notice }

10. **Radical signs** Use `sqrt`, which adjusts to the size of its argument: `\sqrt{x^3}` $\sqrt{x^3}$; `\sqrt[3]{\frac xy}` $\sqrt[3]{\frac xy}$. For complicated expressions, consider using `{...}^{1/2}` instead.

11. Some **special functions** such as "lim", "sin", "max", "ln", and so on are normally set in roman font instead of italic font. Use `\lim`, `\sin`, etc. to make these: `\sin x` $\sin x$, not `sin x` $sin x$. Use subscripts to attach a notation to `\lim`: `\lim_{x\to 0}` $$\lim_{x\to 0}$$

12. There are a very large number of **special symbols and notations**, too many to list here; see [this shorter listing](http://pic.plover.com/MISC/symbols.pdf), or [this exhaustive listing](https://www.ctan.org/tex-archive/info/symbols/comprehensive/symbols-a4.pdf). Some of the most common include: 
  * `\lt \gt \le \ge \neq` $\lt\, \gt\, \le\, \ge\, \neq$.  You can use `\not` to put a slash through almost anything: `\not\lt` $\not\lt$ but it often looks bad.
  * `\times \div \pm \mp` $\times\, \div\, \pm\, \mp$. `\cdot` is a centered dot: $x\cdot y$
  * `\cup \cap \setminus \subset \subseteq \subsetneq \supset \in \notin \emptyset \varnothing` $\cup\, \cap\, \setminus\, \subset\, \subseteq \,\subsetneq \,\supset\, \in\, \notin\, \emptyset\, \varnothing$ 
  * `{n+1 \choose 2k}` or `\binom{n+1}{2k}` ${n+1 \choose 2k}$ 
  * `\to \rightarrow \leftarrow \Rightarrow \Leftarrow \mapsto` $\to\, \rightarrow\, \leftarrow\, \Rightarrow\, \Leftarrow\, \mapsto$
  * `\land \lor \lnot \forall \exists \top \bot \vdash \vDash` $\land\, \lor\, \lnot\, \forall\, \exists\, \top\, \bot\, \vdash\, \vDash$
  * `\star \ast \oplus \circ \bullet` $\star\, \ast\, \oplus\, \circ\, \bullet$ 
  * `\approx \sim \simeq \cong \equiv \prec \lhd` $\approx\, \sim \, \simeq\, \cong\, \equiv\, \prec, \lhd$. 
  * `\infty \aleph_0` $\infty\, \aleph_0$ `\nabla \partial` $\nabla\, \partial$ `\Im \Re` $\Im\, \Re$
  * For modular equivalence, use `\pmod` like this: `a\equiv b\pmod n` $a\equiv b\pmod n$.
  * `\ldots` is the dots in $a_1, a_2, \ldots ,a_n$ `\cdots` is the dots in  $a_1+a_2+\cdots+a_n$
  * Some Greek letters have variant forms:
`\epsilon \varepsilon` $\epsilon\, \varepsilon$, `\phi \varphi` $\phi\, \varphi$, and others. Script lowercase l is `\ell` $\ell$.

  [Detexify](http://detexify.kirelabs.org/classify.html) lets you draw a symbol on a web page and then lists the $\TeX$ symbols that seem to resemble it.  These are not guaranteed to work in MathJax but are a good place to start.  To check that a command is supported, note that MathJax.org maintains a [list of currently supported $\LaTeX$ commands](http://docs.mathjax.org/en/latest/tex.html#supported-latex-commands), and one can also check Dr. Carol JVF Burns's page of [$\TeX$ Commands Available in MathJax](http://www.onemathematicalcat.org/MathJaxDocumentation/TeXSyntax.htm).

14. **Accents and diacritical marks** Use `\hat` for a single symbol $\hat x$, `\widehat` for a larger formula $\widehat{xy}$. If you make it too wide, it will look silly. Similarly, there are `\bar` $\bar x$ and `\overline` $\overline{xyz}$, and `\vec` $\vec x$ and `\overrightarrow` $\overrightarrow{xy}$ and `\overleftrightarrow` $\overleftrightarrow{xy}$. For dots, as in $\frac d{dx}x\dot x =  \dot x^2 +  x\ddot x$,  use `\dot` and `\ddot`.

15. Special characters used for MathJax interpreting can be escaped using the `\` character: `\$` $\$$, `\{` $\{$, `\_` $\_$, etc. If you want `\` itself, you should use `\backslash` $\backslash$, because `\\` is for a new line. 

9. **Fonts** 

  * Use `\mathbb` or `\Bbb` for "blackboard bold": $\mathbb{CHNQRZ}$.
  * Use `\mathbf` for boldface: $\mathbf{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$  $\mathbf{abcdefghijklmnopqrstuvwxyz}$.
  * Use `\mathtt` for "typewriter" font: $\mathtt{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$ $\mathtt{abcdefghijklmnopqrstuvwxyz}$.
  * Use `\mathrm` for roman font: $\mathrm{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$  $\mathrm{abcdefghijklmnopqrstuvwxyz}$.
  * Use `\mathsf` for sans-serif font: $\mathsf{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$  $\mathsf{abcdefghijklmnopqrstuvwxyz}$.
  * Use `\mathcal` for "calligraphic" letters: $\mathcal{ ABCDEFGHIJKLMNOPQRSTUVWXYZ}$ 
  * Use `\mathscr` for script letters: $\mathscr{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$
  * Use `\mathfrak` for "Fraktur" (old German style) letters: $\mathfrak{ABCDEFGHIJKLMNOPQRSTUVWXYZ} \mathfrak{abcdefghijklmnopqrstuvwxyz}$.


## Fontes:


* https://math.meta.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference
* (Deutsch: [MathJax: LaTeX Basic Tutorial und Referenz](https://www.mathelounge.de/509545/mathjax-latex-basic-tutorial-und-referenz-deutsch))
* https://www.mathjax.org/