---
title: "Clean Code: Escolhendo bons nomes"
summary: "Bons nomes são demasiadamente importantes no desenvolvimento de software e este artigo visa cobrir as melhores dicas do livro Clean Code sobre a escolha de bons nomes"
date: 2022-08-06T10:10:58-03:00
draft: true
---
## Introdução
Clean code é provavelmente um dos livros mais famosos do desenvolvimento de software, provavelmente todo desenvolvedor já ouviu o nome desse livro.

A ideia dessa série de artigos é servir como um pequeno resumo pessoal sobre o que vi no livro. Eu achei vários insights interessantes enquanto lia e anotei eles para que eu pudesse revisar, mas senti que anotações soltas com zero contexto não são muito boas pra revisar.

Por isso pretendo não só compartilhar esses insights, mas também divagar sobre cada uma das dicas apresentadas e colocar um pouco de código pra contextualizar o que está sendo dito.

É sempre bom deixar claro que o livro Clean Code não é nenhum magnum opus da computação e nem tem pretensão de ser. O próprio autor do livro deixa bem claro que não se deve levar a ferro e fogo 100% do que é dito no livro. Então não vamos desapontar o uncle bob, ok 😉?

## Use nomes que revelam a intenção do código
Segundo o autor do livro, excelentes nomes são capazes de responder perguntas como essas aqui abaixo.

* Por que a classe "X" existe?
* Qual é a finalidade da função "Y"?
* O teste "A" testa o que exatamente?
* Eu consigo imaginar com um pouco de precisão a aparência do componente "Z" só com base no nome dele?

E eu concordo bastante com essa visão. Excelentes nomes te dão um certo contexto geral do que está acontecendo no código e isso pode poupar bastante tempo de leitura.

O problema é que criar excelentes nomes é uma tarefa bem difícil, raramente me vem à mente o nome perfeito quando estou codificando, principalmente se eu não tiver uma visão clara do que o código deve fazer.

Felizmente, hoje em dia quase todo editor te dá a possibilidade de renomear coisas no código de forma extremamente simples e rápida. Então, se você ainda não usa isso no seu editor, trate de configurar essa funcionalidade pra ontem. E se você tiver uma boa cobertura de testes, mudar o nome de algo é mais seguro ainda.

Pra mostrar outro exemplo real disso, vou pegar um trecho de código em Go de um projeto pessoal meu.
Eu troquei os nomes das funções e parâmetros que estou usando num trecho de código. Você consegue entender com facilidade o que está se passando no código abaixo?

```go
func sentence(item string, files ...string) {
	for _,file := range files {
		if testItem(item, file) {
			color.Red("sentence '%s' already exists on file %s\n", item, file)
			os.Exit(3)
		}
	}
}

func testItem(item, file string) bool {
	buffer := read(file)
	return compare(item, buffer)
}
```
Agora, vamos para o código original. Leia denovo o trecho de código.

```go

func CheckIfSentenceExists(sentence string, filePathList ...string) {
	for _,filePath := range filePathList {
		if hasSentence(sentence, filePath) {
			color.Red("sentence '%s' already exists on file %s\n", sentence, filePath)
			os.Exit(3)
		}
	}
}

func hasSentence(sentence, filePath string) bool {
	var buffer string = ReadFile(filePath)
	return verifyIsSentenceExists(sentence, buffer)
}
```
Os nomes mais descritivos ajudam mesmo a entender o funcionamento do código com o mínimo de contexto possível, não é?

Confesso que não acho esse trecho de código o mais legível que eu já escrevi, mas com uma boa nomenclatura de métodos (e claro, **testes**) eu consigo entender facilmente o que o código deveria fazer, mesmo depois de um tempo sem tocar no projeto.


## Use e abuse de enums, constantes e afins
Muitas vezes nos vemos na situação em que precisamos usar um valor hardcoded no nosso código, tipo, o nosso valor não vai mudar e só vamos usa-lo em algum lugar, logo bate a vontade de simplemente colocar o valor que você quer e pronto.
Quando sujir uma situação parecida, coloque esse valor dentro de uma constante ou variavel, ou então se você estiver mexendo com números crus que representam algo além de um simples número, use um Enum ou algo parecido que é fornecido na sua linguagem.

```javascript

function IgnoreVideos()
{
   let videos = Array.from(document.querySelectorAll("selector"));

   videos
   .filter(function (video) {
        return parseInt(video.querySelectoAll("selector").innerText.replace(".", "")) < 1000;
    })
    .forEach(function(video) {
        video.style.display = "none"
    });
}

```

Duas coisas podem ser feitas para melhorar **muito** a clareza do código. Nós poderiamos jogar aquele valor do parseInt numa variavel que indica o que é o valor sendo guardado, e podemos usar um const para guardar aquele numero 1000 hardcoded.

```javascript

function IgnoreVideos()
{
   let videos = Array.from(document.querySelectorAll("selector"));

   videos
   .filter(function (video) {
        const MINIMUM_VIEWS = 1000;
        let numberOfViews = parseInt(video.querySelectoAll("selector").innerText.replace(".", ""));

        return numberOfViews < MINIMUM_VIEWS;
    })
    .forEach(function(video) {
        video.style.display = "none"
    });
}

```
Essa mudança não foi nada de extraordinário e sendo sincero eu nem achei que o nome da constante seja bom, mas o simples fato de adicionar nome a coisas que antes eram só um monte de código já trouxe muito mais clareza pra esse trecho de código.

Outra melhoria seria jogar esse código que pega o numero de views em uma função, pois além de tirar esse `innerText.replace` feio da nossa frente, também tornaria o código reaproveitável e nós poderiamos escolher um bom nome pra essa função, deixando ainda mais claro o que o nosso código faz.

## Procure nomear variaveis com nomes que indicam o que está sendo mensusado e a sua unidade
Imagine que eu preciso criar uma variavel para guardar o numero de dias que a minha entrega demorou pra chegar dos correios. Eu poderia usar uma variavel chamada `days`, é um nome OK, mas podemos achar um nome melhor ainda.
Para descobrir qual nome usar, vamos responder as duas perguntas abaixo.
1. O que essa variavel está guardando?
    > R: O numero de dias decorridos desde que eu fiz o meu pedido.
2. Qual é a "unidade" dessa variavel, ou seja, que tipo de valor eu estou guardando e qual é a sua ordem de grandeza?
    > R: Aqui quando eu digo "tipo" não estou me referindo se o valor é uma string ou um int, mas sim qual a ordem de grandeza do meu valor salvo, ou seja, esse tempo decorrido está sendo mensurado em dias, segundos ou milisegundos? Informar isso é importante para evitar confussões futuras (usar um valor em milisegundos achando que eram dias, por exemplo). Enfim, eu quero guardar o tempo decorrido em **dias**, então a unidade da minha variavel é um dia.

Pronto, com essas duas informações fica mais simples decidir um nome para nossa variavel: Por que ao invés de usar o nome `days`, nós não usamos o nome `elapsedTimeInDays`? É um nome que descreve o que nós guardamos (`elapsedTime` (Tempo decorrido)) e salva a unidade desse tempo que são dias.

Caso eu precisasse fazer uma variavel para guardar quanto tempo eu consegui ficar sem respirar, uma boa opção de nome seria `elapsedTimeInSeconds`.

## Se possível, use nomes que são "buscáveis"
Não sei quanto a você, mas eu busco nomes no meu código o tempo inteiro, seja porque eu tenho uma rota e quero descobrir qual Controller é usado por ela, ou então pra achar onde uma função é usada. O problema é que quando o nome é genérico demais, fica péssimo de fazer buscas assim no código porque você vai receber vários resultados inuteis para sua busca, simplesmente porque existem várias coisas no código com um nome parecido com o nome buscado.

Uma boa forma de resolver isso é evitar ao máximo usar nomes genéricos no código. Um exemplo de nome genérico seria a variavel `days` do trecho acima. Evite também usar variaveis de uma só letra porque buscar por essa variável depois será como procurar agulha no palheiro. A única situação onde que acho plausível usar uma variavel com esse tipo de nome é dentro de um for.

## Conclusão
Bom, esse artigo já está ficando um pouco grande então vamos parar por aqui. O tema de nomes é algo extremamente importante para o desenvolvimento de software, não é atoa que esse tema é abordado logo no segundo capitulo do livro, e por isso eu acho importante que praticar dar bons nomes às coisas no seu código, e ter sempre à mão alguma ferramenta para refatorar código e trocar o nome de algo sem muita dor de cabeça (testes pra garantir que nada foi quebrado são sempre bem vindos).
No próximo artigo eu pretendo abordar o assunto de funções. Em minha humilde opinião a função é a unidade de código mais importante em um software. Fazendo o bom uso das funções seu código tende a ser organizado e fácil de manter, não importando qual paradigma você está usando.
