---
title: "Clean Code: Testes"
date: 2022-11-12T20:46:15-03:00
draft: true
---

Falar que testes são importantes para a mantenabilidade do código é chover no molhado, mas é sempre bom ter em mente que nem todo teste trás tanto benefício assim. Testes que quebram o tempo todo, são difíceis de estender, e complicados de entender, são mais prejudiciais do que benéficos para a sua codebase, e o autor explica o porquê nesse trecho.

>_"Ter testes 'sujos' é o equivalente ou até mesmo pior do que não ter testes. O problema é que testes precisam ser modificados enquanto o código de produção evolui e quanto mais os seus testes forem 'sujos', mas difícil será mudar eles."_

## O que faz um teste ser "limpo"? Legibilidade, Legibilidade, Legibilidade
Segundo o autor, a legibilidade talvez seja até mesmo mais importante no código dos testes do que o código de produção. Testes legíveis têm **clareza**, **simplicidade** e **densidade de expressão**. O dois primeiro pontos são meio óbvios, e o terceiro significa que os seus testes precisam expressar o máximo de informação usando o mínimo de linhas possíveis. O que nos leva pra segunda dica.

## Pode ser boa ideia tentar minimizar a quantidade de assertions nos seus testes
E isso não quer dizer que você precise deixar de testar algum caso, simplesmente significa que é sempre bom tentar abstrair as suas assertions. Se você der um nome descritivo para a função que está abstraindo as suas assertions, isso pode até mesmo ajudar o leitor a entender a intenção do seu código.

Vou mostrar um exemplo abaixo de um código Go que eu fiz para testar quando o usuário fazia uma requisição http para pegar a definição de uma palavra. É bom lembrar que em Go, nós verificamos os usando um `if` mesmo, e não chamando um `assertEquals` da vida. Então considere cada `if` como uma asserção no código.

```go
func TestGetDefinition(t *testing.T) {
	t.Run("User retrieve word definition successfully", func(t *testing.T) {
		server := makeFakeServer(200, []byte(wordDefinition))
		got, _ := GetDefinition(server.URL)
		response := createResponse(
			"To shrink, cower, tense or recoil, as in fear, disgust or embarrassment.",
			"He cringed as the bird collided with the window.",
			"cringe",
		)

		if !reflect.DeepEqual(got, response) {
			t.Errorf("got %v want %v", got, response)
		}

		if got.Normalize().Word == "" {
			t.Errorf("normalized word from response %v shouldn't be empty", got)
		}

	})

	t.Run("word dont exists", func(t *testing.T) {
		server := makeFakeServer(404, []byte("I dont know"))
		_, err := GetDefinition(server.URL)
		if err != IO.NotFoundError {
			t.Errorf("got %q want %q", err, IO.NotFoundError)
		}
	})
}
```
Eu não considero esse código de teste como o ideal, por que ele não tem muita **densidade de expressão**, ou seja, ele não passa a sua ideia de forma enxuta e isso obriga o leitor a ler bem mais coisa do que ele deveria. Nós podemos refatorar o teste para ficar dessa forma.

```go
func TestGetDefinition(t *testing.T) {
	t.Run("User retrieve word definition successfully", func(t *testing.T) {
		server := makeFakeServer(200, []byte(wordDefinition))
		got, _ := GetDefinition(server.URL)
		response := createResponse(
			"To shrink, cower, tense or recoil, as in fear, disgust or embarrassment.",
			"He cringed as the bird collided with the window.",
			"cringe",
		)

		checkSuccessResponse(t, got, response)
	})

	t.Run("word dont exists", func(t *testing.T) {
		server := makeFakeServer(404, []byte("I dont know"))
		_, err := GetDefinition(server.URL)

		checkFailResponse(t, err, IO.NotFoundError)
	})
}

//Meus testes acabaram, daqui pra baixo só temos funções auxiliares

func checkSuccessResponse(t testing.TB, got, want DictionaryApiResponse) {
	t.Helper()

	if !reflect.DeepEqual(got, want) {
		t.Errorf("got %v want %v", got, want)
	}

	if got.Normalize().Word == "" {
		t.Errorf("normalized word from response %v shouldn't be empty", got)
	}
}

func checkFailResponse(t testing.TB, got, want error) {
	t.Helper()

	if got != want {
		t.Errorf("got %q want %q", got, want)
	}
}
```
Nesse exemplo acima, eu "diminuí" a quantidade de assertions por teste simplesmente abstraindo a checagem das responses. Os testes ainda têm a mesma cobertura, mas o código ficou mais simples e enxuto.

Claro, é sempre bom lembrar de ter bom senso, eu não acho uma boa ideia criar um milhão de funções auxiliares só para que seus testes tenham uma única assertion.

## Cada teste deveria ter um único conceito
Nós não queremos um teste enorme que testa vários cenários, esses tipos de testes podem ser mais práticos de escrever, mas costumam ser bem chatas de manter, por que se esse teste quebrar, você pode precisar ter de debuggar o teste inteiro, o que irá te dar muito mais trabalho do que debuggar um código menor que testa somente um conceito.

Pegue como exemplo o código acima, se nós misturássemos todos os testes em um único teste, a própria Legibilidade desse teste ficaria comprometida, mesmo que os dois testes mostrados acima sejam bem simples.

## TL;DR
Bons testes tem uma boa legibilidade. Testes ruins mais atrapalham do que ajudam, pois eles tendem a ser bem mais difíceis de modificar à medida que o sistema evolui.
Abstrair as assertions dos seus testes **pode** ser uma boa ideia, mas é importante ter bom senso. Não misture conceitos diferentes no mesmo teste, se você está testando o caso de sucesso em um teste, é melhor criar um novo teste para testar o caso de exceção do que testar tudo no mesmo teste.
