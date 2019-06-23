---
redirect_from: /latex/mathjax/introducao/mathjax_latex_representacao_matematica/
title: Usando o MathJax para representar formulas matemáticas em Latex 
tags: [Latex, MathJax, Jekyll, Matemática, Formulas, como usar, tutorial, resumo]
categories: [Matemática, MathJax]
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
   teaser: redesneurais/rnn/lstm720x450.jpg
   feature: redesneurais/rnn/lstm720x450-2.jpg
math: true
---

Um breve tutorial copiado do site Stack Exchange (meta) que demosntra as práticas mais relevantes e mais usadas para escrita de formulas matemáticas usando o MathJax.

<!--more-->

Para ver como a formula é escrita onde se usa o MathJax basta clicar com o botão direito sobre a fórmula e selecionar "Show Match As > Tex Commands".


2. **For inline formulas, enclose the formula in `$...$`.  For displayed formulas, use `$$...$$`.**  
These render differently. For example, type
`$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$`  
to show $$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}#$ (which is inline mode) or  type  
`$$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$$`   
to show
$$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$$
(which is display mode).

3. For **Greek letters**, use `\alpha`, `\beta`, …, `\omega`: $\alpha, \beta, … \omega$.  For uppercase, use `\Gamma`, `\Delta`, …, `\Omega`: $\Gamma, \Delta, …, \Omega$.

4. For **superscripts and subscripts**, use `^` and `_`.  For example, `x_i^2`: $x_i^2$, `\log_2 x`: $\log_2 x$.

5. **Groups**. Superscripts, subscripts, and other operations apply only to the next “group”. A “group” is either a single symbol, or any formula surrounded by curly braces `{`…`}`.  If you do `10^10`, you will get a surprise: $10^10$. But `10^{10}` gives what you probably wanted: $10^{10}$. Use curly braces to delimit a formula to which a superscript or subscript applies: `x^5^6` is an error;  `{x^y}^z` is ${x^y}^z$, and `x^{y^z}` is $x^{y^z}$. Observe the difference between `x_i^2` $x_i^2$ and `x_{i^2}` $x_{i^2}$.

6. **Parentheses** Ordinary symbols `()[]` make parentheses and brackets $(2+3)[4+4]$. Use `\{` and `\}` for curly braces $\{\}$.

    These do *not* scale with the formula in between, so if you write `(\frac{\sqrt x}{y^3})` the parentheses will be too small: $(\frac{\sqrt x}{y^3})$.    Using `\left(`…`\right)` will make the sizes adjust automatically to the formula they enclose: `\left(\frac{\sqrt x}{y^3}\right)` is $\left(\frac{\sqrt x}{y^3}\right)$.

   `\left` and`\right` apply to all the following sorts of parentheses: `(` and `)` $(x)$, `[` and `]` $[x]$, `\{` and `\}` $\{ x \}$, `|` $|x|$, `\vert` $\vert x \vert$, `\Vert` $\Vert x \Vert$, `\langle` and `\rangle` $\langle x \rangle$,  `\lceil` and `\rceil` $\lceil x \rceil$, and `\lfloor` and `\rfloor` $\lfloor x \rfloor$. `\middle` can be used to add additional dividers. There are also invisible parentheses, denoted by `.`: `\left.\frac12\right\rbrace` is $\left.\frac12\right\rbrace$.

    If manual size adjustments are required:
`\Biggl(\biggl(\Bigl(\bigl((x)\bigr)\Bigr)\biggr)\Biggr)` gives
$\Biggl(\biggl(\Bigl(\bigl((x)\bigr)\Bigr)\biggr)\Biggr)$.

7. **Sums and integrals** `\sum` and `\int`; the subscript is the lower limit and the superscript is the upper limit, so for example `\sum_1^n` $\sum_1^n$. Don't forget `{`…`}` if the limits are more than a single symbol.  For example, `\sum_{i=0}^\infty i^2` is $\sum_{i=0}^\infty i^2$. Similarly, `\prod` $\prod$, `\int` $\int$, `\bigcup` $\bigcup$, `\bigcap` $\bigcap$, `\iint` $\iint$, `\iiint` $\iiint$.

8. **Fractions** There are two ways to make these. `\frac ab` applies to the next two groups, and produces $\frac ab$; for more complicated numerators and denominators use `{`…`}`: `\frac{a+1}{b+1}` is $\frac{a+1}{b+1}$. If the numerator and denominator are complicated, you may prefer `\over`, which splits up the group that it is in: `{a+1\over b+1}` is ${a+1\over b+1}$.

9. **Fonts** 

  * Use `\mathbb` or `\Bbb` for "blackboard bold": $\mathbb{CHNQRZ}$.
  * Use `\mathbf` for boldface: $\mathbf{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$  $\mathbf{abcdefghijklmnopqrstuvwxyz}$.
  * Use `\mathtt` for "typewriter" font: $\mathtt{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$ $\mathtt{abcdefghijklmnopqrstuvwxyz}$.
  * Use `\mathrm` for roman font: $\mathrm{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$  $\mathrm{abcdefghijklmnopqrstuvwxyz}$.
  * Use `\mathsf` for sans-serif font: $\mathsf{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$  $\mathsf{abcdefghijklmnopqrstuvwxyz}$.
  * Use `\mathcal` for "calligraphic" letters: $\mathcal{ ABCDEFGHIJKLMNOPQRSTUVWXYZ}$ 
  * Use `\mathscr` for script letters: $\mathscr{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$
  * Use `\mathfrak` for "Fraktur" (old German style) letters: $\mathfrak{ABCDEFGHIJKLMNOPQRSTUVWXYZ} \mathfrak{abcdefghijklmnopqrstuvwxyz}$.

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

13. **Spaces** MathJax usually decides for itself how to space formulas, using a complex set of rules. Putting extra literal spaces into formulas  will not change the amount of space MathJax puts in: `a␣b` and `a␣␣␣␣b` are  both $a    b$. To add more space, use `\,` for a thin space $a\,b$; `\;` for a wider space $a\;b$.  `\quad` and `\qquad` are large spaces: $a\quad b$, $a\qquad b$.

  To set plain text, use `\text{…}`: $\{x\in s\mid x\text{ is extra large}\}$. You can nest `$…$` inside of `\text{…}`.

14. **Accents and diacritical marks** Use `\hat` for a single symbol $\hat x$, `\widehat` for a larger formula $\widehat{xy}$. If you make it too wide, it will look silly. Similarly, there are `\bar` $\bar x$ and `\overline` $\overline{xyz}$, and `\vec` $\vec x$ and `\overrightarrow` $\overrightarrow{xy}$ and `\overleftrightarrow` $\overleftrightarrow{xy}$. For dots, as in $\frac d{dx}x\dot x =  \dot x^2 +  x\ddot x$,  use `\dot` and `\ddot`.

15. Special characters used for MathJax interpreting can be escaped using the `\` character: `\$` $\$$, `\{` $\{$, `\_` $\_$, etc. If you want `\` itself, you should use `\backslash` $\backslash$, because `\\` is for a new line. 

(Tutorial ends here.)

-------------

It is important that this note be reasonably short and not suffer from too much bloat. To include more topics, please create short addenda and post them as answers instead of inserting them into this post.


## Fontes:


* https://math.meta.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference
* (Deutsch: [MathJax: LaTeX Basic Tutorial und Referenz](https://www.mathelounge.de/509545/mathjax-latex-basic-tutorial-und-referenz-deutsch))
