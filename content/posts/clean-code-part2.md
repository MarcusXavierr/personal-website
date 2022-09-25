---
title: "Clean Code: Fazendo bom uso das funções | Parte 1"
date: 2022-09-17T12:17:03-03:00
draft: true
---
Parafraseando o próprio uncle bob, funções são a primeira linha de organização de qualquer programa. Ou seja, na maioria das linguagens, uma função (ou método) é a "menor" forma de organizar seu código.

Você pode ter classes, interfaces e tudo mais, mas dentro disso tudo estarão funções mantendo sua codebase organizada. Ou bagunçada, depende de como você as usa. Eu diria que essa é uma das partes mais importantes dessa série de artigos justamente por isso.

## Pequenas!
Aqui o autor diz que funções deveriam ser pequenas, e eu concordo totalmente com isso. Raramente existe um motivo plausível para que as suas funções tenham mais do que vinte e cinco linhas. Nessa seção do livro, o uncle bob é mais extremista e diz que uma função pequena tem por volta de **quatro** linhas.

Você pode pensar que ele enlouqueceu, mas em algumas linguagens como haskell é até comum ver uma codebase inteira composta em sua maioria por minúsculas funções de quatro ou cinco linhas.

O código fica muito mais organizado e bonito dessa forma, na minha opinião. O problema é que muitas vezes acabamos o código de forma apressada e damos muitas responsabilidades pra mesma função. Não tem problema fazer isso, contanto que limpe o seu código depois. Praticamente todo editor hoje em dia fornece a opção de selecionar um bloco de código e extrair uma função (ou seja, pegar esse bloco e automaticamente jogar numa nova função). Trate de configurar essa opção no seu editor pra ontem, é **muito** útil pra refatorar o código.

## Nada de Hadouken, por favor
O autor sugere que as suas funções tenham somente um ou dois níveis de identação no máximo, ou seja, você deveria ao máximo evitar aninhados. Sério, se tem um if aninhado no seu código, ele muito provavelmente poderia ser abstraído em uma nova função.

Outra dica polêmica do uncle bob, da qual eu gosto bastante, é que deveria ter somente **uma** linha de código em blocos como `if`, `else`, `while`, etc. Eu acho essa dica um pouco extrema em algumas situações, mas se estiver um monte de coisa acontecendo dentro do seu `if`, isso provavelmente deveria ser abstraído dentro de um novo método (lembra que as suas funções deveriam ser pequenas? 🙃).

## Faça somente uma coisa, e faça direito
Fazer uma função que faz mais de uma coisa fere tantas "boas práticas" que é até difícil de listar todas, mas os principais problemas de ter uma função "multitarefa" são:
* Fere o princípio de responsabilidade única do SOLID
* Como a função faz várias coisas, fica muito mais difícil dar um nome descritivo pra ela
* Muito provavelmente não será uma função pequena

Essa dica de criar funções que fazem somente uma coisa já é algo extremamente antiga, desenvolvedores muito inteligentes já aplicavam isso nos anos 70. No desenvolvimento do Unix o programadores fizeram ferramentas que **faziam somente uma coisa, mas faziam direito**.

Por exemplo, o comando `history` serve para mostrar o seu histórico de comandos rodados no terminal. O comando `grep` pode ser usado para filtrar texto.
E pronto, caso eu queira ver todos os comandos do git que eu rodei no terminal, basta rodar o comando `history` combinado com o `grep` pra filtrar o resultado e me mostrar somente os comandos que contenham a palavra `git`.

Mas nós temos um problema, escrever funções que fazem somente uma coisa (e fazem isso direito), não costuma ser uma tarefa trivial. Pode ser complicado dizer se a sua função é multitarefa ou não.
O exemplo abaixo é uma função de um CLI meu que salva palavras em inglês que o usuário não conhece (para poder adicioná-las no anki depois), e dá ao usuário a opção de printar a definição dessa palavras desconhecida no terminal.

```go
func(cmd *cobra.Command, args []string) {
    definition, _ := cmd.Flags().GetBool("definition")
    sentence := getSentenceFromArgs(args)

    if definition {
        url := "https://api.dictionaryapi.dev/api/v2/entries/en/" + sentence
        response, err := dictionary.GetDefinition(url)
        if err == IO.NotFoundError {
            IO.PrintRed(os.Stdout, "word not found on dictionary api\n\n")
        }
        if err == nil {
            PrettyPrintDefinition(response.Normalize())
        }
    }

    saveSentence(sentence)
}

```

Esse função é multitarefa? Parece ser, não é? E se nós abstrairmos o código dentro desse `if`?

```go
func(cmd *cobra.Command, args []string) {
    definition, _ := cmd.Flags().GetBool("definition")
    sentence := getSentenceFromArgs(args)

    if definition {
        printDefinition(sentence)
    }

    saveSentence(sentence)
}

func printDefinition(sentence string) {
	url := "https://api.dictionaryapi.dev/api/v2/entries/en/" + sentence
	response, err := dictionary.GetDefinition(url)
	if err == IO.NotFoundError {
		IO.PrintRed(os.Stdout, "word not found on dictionary api\n\n")
	}
	if err == nil {
		PrettyPrintDefinition(response.Normalize())
	}
}
```
Será que agora a função faz somente uma coisa? Bom, vamos listar o que a nossa função está fazendo.
1. Pegando os dados que o usuário passa, e ver se o usuário passou a flag `definition` no comando, para ver no terminal a definição da palavra que ele está adicionando
2. verificar se o usuário quer ver a definição da palavra, caso ele queira, printar isso na tela.
3. salvar a sentença que o usuário está adicionando para estudar no anki mais tarde.

Bom, como podemos ver, o nosso código ainda está "fazendo" três, será que nós conseguimos abstrair esse código ainda mais pra que ele faça somente **uma** coisa? Vamos ver

```go
func(cmd *cobra.Command, args []string) {
    // finalmente a minha função faz SOMENTE uma coisa
    saveSentenceAndPrintDefinition(cmd, args)
}

// Mas espera, agora eu gerei uma função com assinatura igual à anterior
func saveSentenceAndPrintDefinition(cmd *cobra.Command, args []string) {
    // Esse código está exatamente igual antes, agora somente mudou de lugar
	definition, _ := cmd.Flags().GetBool("definition")
	sentence := getSentenceFromArgs(args)

	if definition {
		printDefinition(sentence)
	}

	saveSentence(sentence)
}

func printDefinition(sentence string) {
	url := "https://api.dictionaryapi.dev/api/v2/entries/en/" + sentence
	response, err := dictionary.GetDefinition(url)
	if err == IO.NotFoundError {
		IO.PrintRed(os.Stdout, "word not found on dictionary api\n\n")
	}
	if err == nil {
		PrettyPrintDefinition(response.Normalize())
	}
}
```
Viram? Existe um ponto onde **não** vale mais a pena ficar extraindo métodos da sua função principal para que ela fique "menos multitarefa". Depois de certo ponto você está simplesmente jogando o código para outro canto, sem melhorar em nada a sua legibilidade e mantenabilidade.
Ou seja, uma boa forma de saber se seu código chegou no "ponto certo" e está seguindo a regra do *"Faça somente uma coisa"* é ver se mais uma extração de função irá melhorar seu código ou se irá simplesmente pegar o seu código atual e jogá-lo em outro canto, como aconteceu no nosso último exemplo.

## Conclusão
Eu pretendia fazer somente um artigo para abordar o bom uso da função, mas as funções são tão importante para a programação, que existem inúmeras boas práticas e dicas para se fazer um excelente código com elas. Colocar tudo em um único artigo deixaria ele enorme.
Por isso pretendo fazer mais um ou dois artigos abordando mais dicas para escrever boas funções, como por exemplo, evitar efeitos colaterais e evitar receber um milhão de parâmetros.

