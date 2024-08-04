---
title: "Como as linguagens de programação são criadas?"
date: 2024-07-29T18:46:11-03:00
draft: false
---
## Introdução
Nós usamos linguagens de programação diariamente, e as vezes pode parecer que o computador está fazendo uma mágica: você escreve um arquivo de texto e o computador magicamente executa as operações que esse arquivo descreve.

Quando eu era criança, existia um desenho animado chamado "De onde vem?". A cada episódio algo era explicado, como por exemplo, "De onde vem o vidro?". Era uma explicação simples e de fácil entendimento, porém bem satisfatória.

![Capa do programa "De onde vem?"](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5t5jlxsbgnhxcry2i2ci.png)

Com isso em mente, a ideia desse artigo, é explicar um pouco sobre *de onde vêm as linguagens de programação*. Nesse post, eu pretendo explicar um pouco sobre como uma linguagem pode ser implementada, e por fim, trazer alguns materiais de estudo bem interessantes para quem deseja se aprofundar e quem sabe programar a sua própria linguagem.

## Para que uma linguagem tome vida, é preciso *implementá-la*
Antes de mais nada, precisamos separar muito bem dois conceitos: a ideia abstrata da linguagem, e a sua implementação concreta (um interpretador ou compilador).

Uma linguagem nada mais é do que uma forma de se comunicar, é uma sequência de letras organizadas de forma a passar uma mensagem.

O problema é: computadores não entendem palavras, nem mesmo sequências esquisitas de letras (o comando `LDS`, por exemplo). Tudo o que o computador entende, são instruções pra sua CPU. Então é necessário ter algum software capaz de pegar as operações descritas na nossa linguagem (o `print('hello world')`, por exemplo), e transformar isso em instruções para o computador interpretar, de alguma forma.

Ou seja, precisamos de uma **implementação concreta** para que nossa **linguagem** possa ser executada. Nesse artigo, sempre que eu falar "linguagem", eu estou me referindo à essa implementação concreta e prática que traz utilidade para o amontoado de texto colorido que escrevemos.

E esses programas capazes de executar linguagens têm nome: **interpretadores e compiladores**.
## O que são interpretadores e compiladores? E qual a diferença entre eles?

### O que é um compilador?
De forma muito resumida, um compilador é um programa que transforma o seu código fonte, em outro tipo de representação, que geralmente é uma forma mais eficiente para o computador executar. O software que pega seu código C, e transforma em binário executável, é um compilador. Mas o software que pega o código typescript (`main.ts`), e transforma em javascript (`main.js`) para ser executado no navegador, **também** é um compilador.

Ou seja, um compilador não é necessariamente um programa que lê código numa linguagem X e cospe código binário. Esse programa basicamente tem a responsabilidade de transformar seu código em algo alguma representação que possa ser executada de alguma forma, mas essa representação pode ter várias formas.

### O que é um interpretador?
Por outro lado, um interpretador é um programa que lê o seu código fonte, e de alguma forma, executa as instruções que ele contém. Só isso, então quando você passa código javascript para o Nodejs, ele está agindo como um interpretador: lendo código fonte, e **interpretando-o**. E algumas **interpretadores** usam **compiladores** para os auxiliarem no processo de interpretador uma linguagem.

A linguagem PHP por exemplo, tem uma espécie de máquina virtual chamada Zend, que recebe uma representação compilada do seu código fonte, e executa as instruções dessa representação.

### Pera, então linguagens interpretadas também podem ser compiladas?
Sim, isso é algo um pouco confuso no início, mas a ideia principal é que interpretadores e compiladores não são opostos. Um compilador tem um papel muito claro, e esse papel é diferente de um interpretador.

Muitas linguagens interpretadas usam a técnica de ter uma espécie de máquina virtual, extremamente otimizada, que recebe uma coisa que chamamos de **bytecode** e executa isso. Então, a máquina virtual de linguagens como `PHP` e `Python`, não são capazes de entender seu código e por isso precisamos da ajuda de compiladores, para gerar `bytecode` a partir do seu código (imagine como um assembly para uma máquina virtual).

No fim das contas, quando falamos que uma linguagem é "interpretada", simplesmente queremos dizer que você precisa sempre passar o seu código fonte para um programa (o `node` ou `python`, por exemplo), e esse programa já vai rodar o seu código.

E quando uma linguagem é "compilada", primeiro você precisa passar seu código fonte para um programa (o `gcc` ou `tsc`, por exemplo), que irá gerar um novo arquivo, e daí sim poderemos executar esse arquivo de alguma forma.

## Ok, entendi de onde vêm as linguagens, mas como eu posso criar uma implementação?
Eu diria que o caminho mais prático pra quem está começando é criar um interpretador, que utiliza a técnica de tree walk para interpretar o código. Esse estilo de implementação é quebrada em 3 componentes principais: scanner, parser e o interpretador tree walk.

O seu código passará por esses 3 componentes nessa ordem mesmo, onde a cada etapa, o interpretador transforma o seu código em estruturas cada vez mais fáceis de serem interpretadas pelo computador.

Esse fluxograma simples mostra o caminho do código fonte da sua linguagem até a fase da interpretação.

![diagonal](https://github.com/user-attachments/assets/5cddaa6e-9ec3-43d6-b707-4815140d4fa3)

Vou explicar (com códigos de exemplo) cada uma dessas etapas.

### Scanner
Nessa fase o seu código fonte é lido como uma string (sequência de caracteres) e quebrado em vários tokens. Esse token é uma unidade da sua linguagem de programação, como por exemplo as palavras reservadas `while` e `if`, ou então os literais como `"isso é uma string"` ou `10`.

Então, isso:

![source code antes do scanner](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/l011jh1q45ajho2j1frf.png)


Vira isso:

![source code antes do scanner](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h8rab48e3u8m7qwcdb1j.png)

Essa imagem é só pra exemplificar a quebra do código fonte em tokens, geralmente o "token" é um objeto/estrutura contendo algumas informações importantes como:
1. O seu tipo (ser é um `for`, um número, operador `++` e etc.).
2. O seu lexema (a string que achamos na hora de escanear esse token).
3. Em qual linha ele foi encontrado (ajuda bastante na hora de aportar erros de sintaxe).

Um exemplo de token seria:
```php
class Token {
    public function __construct(
        public readonly TokenType $tipo,
        public readonly mixed $valor, // Válido somente para valores literais como "strings" e números (10.5) explícitos no código fonte
        public readonly string $lexema,
        public readonly int $linha
    ){}
}
```

### Parser
Nessa fase já possuímos os tokens, mas esse monte de tokens ainda não possui significado algum, não possui **semântica**. Então o parser vai transformar esse amontoado de tokens em algo mais palpável: uma árvore, representando as operações. Chamamos essa árvore de *"Parse Tree"*.

Uma parse tree é uma estrutura de capaz de representar fielmente a estrutura do seu programa, respeitando as regras de precedência (por exemplo, no código `const x = 5 * 5`, precisamos executar o código `5 * 5` *antes* de executar o código de atribuição `const x = ...` que irá receber o valor do resultado)

Por exemplo, imagine a expressão `10 + 5 * 2`. Ela pode ser representada assim na parse tree:


![Uma parse tree representando a operação acima](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/p45tyuytc6czy85x0q96.png)


Na parse tree, cada item (chamamos de nó da arvore) tende a representar uma operação feita no código. Onde cada nó por sua vez pode conter várias ramificações e se tornar uma árvore, com outros nós representando operações mais aninhadas na expressão.

Também é bom lembrar que a imagem acima é um exemplo, e um nó de uma parse tree pode ser um objeto/estrutura com várias informações úteis, como por exemplo, assim pode ser um nó que guarda operações binárias (uma operação com dois operandos, como soma ou multiplicação):

```php
class BinaryExpr extends Expr
{
    public function __construct(
        public readonly Expr $esquerda,
        public readonly Token $operador,
        public readonly Expr $direita
    ){}
}
```

Nesse nó eu salvo a ramificação de expressões à esquerda e direita, que podem ser uma variável (`x + 4`), uma chamada de função (`pegaNoBanco(x) + 4`) ou um literal (`10  + 4`). E também guardo o token que possui informações do operador dessa expressão (`+`, `*`, etc.).

### Interpretação Tree Walk
As duas primeiras fases são praticamente iguais pra *QUALQUER* implementação de uma linguagem, mas a partir dessa fase, cada linguagem vai escolher suas técnicas para dar vida à parse tree. Existe uma infinidade de técnicas, e nessa artigo vamos abordar uma das mais diretas: A Tree Walk.

Como o nome sugere, a ideia é percorrer a árvore de expressões e ir computando os nós mais profundos primeiro e indo subindo até a raiz da árvore.

Na implementação do Phortugol, eu usei pattern matching pra ter uma função que conseguia interpretar cada tipo de nó da árvore e me retornar um resultado

```php
    public function evaluate(Expr $expr): mixed {
        return match(true) {
            $expr instanceof BinaryExpr => $this->handleBinary($expr),
            $expr instanceof UnaryExpr => $this->handleUnary($expr),
            $expr instanceof LiteralExpr => $this->handleLiteral($expr),
            $expr instanceof LambdaExpr => $this->handleLambdaExpr($expr),
            ...
        };
    }
```

Então sempre que eu chamar a função `evaluate` passando uma ramificação da árvore, ele vai saber exatamente qual função chamar pra interpretar o nó atual, e no fim das contas essa função continuará sendo chamada até o resultado de todas as operações chegar à raiz da arvore e obtermos o resultado final da expressão.

Por exemplo, a parse tree anterior seria interpretada da seguinte forma:

![O processo de interpretação da parse tree anterior](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/pa1l4x1xjjuxu70zfwzh.png)

Um fato curioso, é que as primeiras versões de Ruby e PHP usavam essa técnica de tree walk. Então é totalmente possível construir uma linguagem funcional usando um tree walk interpreter.

## conclusão
Ufa finalmente chegamos ao fim. Eu expliquei a ponta do iceberg, tem várias técnicas que eu não cobri, e não falamos sobre coisas como compilação e nem otimização de código (essa parte é bem mindblowing). Mas eu espero que já seja possível ter um modelo mental mais acurado sobre o que acontece quando digitamos o código e mandamos o computador rodar.

Caso você tenha se interessado pelo assunto, tenho algumas recomendações:
- [O livro crafting interpreters](). É absurdamente bom, eu gostaria que mais áreas da computação tivessem um livro com a mesma qualidade, pragmatismo e simplicidade sem perder profundidade.
- [Essa playlist sobre parsers top down](https://www.youtube.com/watch?v=OIKL6wFjFOo&list=PLBlnK6fEyqRgPLTKYaRhcMt8pVKl4crr6), os dois primeiros vídeos são a melhor explicação sobre o "Recursive Descent Parser" que eu já vi. Recomendo usar como material de apoio
