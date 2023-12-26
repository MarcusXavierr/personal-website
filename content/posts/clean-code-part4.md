---
title: "Clean Code: Formatação"
date: 2022-11-11T20:25:36-03:00
draft: false
series: ['Fundamentos de Clean Code']
---

## Introdução
A primeira impressão que tive sobre o capítulo sobre formatação é que ele só teria obviedades, afinal, formatar código não tem muito segredo, mas eu vi algumas provocações interessantes que gostaria de compartilhar.

Por exemplo, se eu perguntasse que código funcionando é a coisa mais importante no trabalho de um Programador, a grande maioria das pessoas diria que sim. E o uncle bob no começo do capítulo lança uma provocação e diz que isso não é verdade.

Segundo o autor, comunicação eficiente (o que engloba uma formatação de código eficiente) é o principal trabalho de um desenvolvedor. E com "comunicação" eu entendo que ele também engloba código legível e fácil de manter. O Uncle bob expressa muito bem esse ideia nesse parágrafo abaixo.

>*"as funcionalidades que você cria hoje têm uma boa chance de serem alteradas na próxima release, mas a legibilidade do seu código vai ter um profundo efeito em todas as mudanças que estarão por vir. **Estilo de código** e **Legibilidade** setam precedentes que continuarão a afetar a mantenabilidade e extensibilidade do código muito depois do código original ser modificado a ponto de ser tornar irreconhecível. **Seu estilo e disciplina sobrevivem, mesmo que seu código não**"*.

## Antes de mais nada, a melhor formatação tende a ser aquela que a equipe inteira segue
Uma das primeiras coisas que o autor menciona nesse capítulo é que você deveria escolher algumas regras simples em conjunto com a sua equipe e todos seguirem essas regras. Parece bobo, mas padronizar esse tipo de coisa deixa o código mais uniforme e constante.

E em pleno 2022 você não precisa nem se preocupar em formatar seu código, existem inúmeras ferramentas (como o prettier por exemplo) que formatam seu código com base em algumas regras pré definidas.

Por isso também, é sempre bom procurar seguir convenções das linguagens e frameworks. Se todo mundo usa snake_case em python, por que você usaria camelCase?

## Cada "bloco" de código deveria funcionar da mesma forma que um parágrafo
Em um texto bem escrito, geralmente o ideal é que cada parágrafo represente uma ideia. Ou seja, ideias diferentes, ao longo do texto você vai desenvolvendo suas ideias, e vai separando elas em parágrafos, pois assim você terá um texto bem formatado naturalmente, ao invés de ter um bloco enorme de palavras que é péssimo de ler.

A mesma ideia se aplica à código. Eu sei que algumas pessoas não gostam de colocar espaços em branco no código, mas eu tenho uma opinião forte (e o autor corrobora com a minha opinião) de que "blocos" de código deveriam representar alguma "coisa", e "coisas" diferentes deveriam estar em blocos separados.

Por exemplo:

### Versão com tudo junto
```javascript
function ignoreUserThreads(data) {
  const threads = getThreads()
  threads.forEach((thread) => {
    if (data.needIgnoreThisUser(getAuthorName())) {
      thread.style = 'display: none;'
    }
    function getAuthorName() {
      return thread.getAttribute('data-author');
    }
  })
}
function muteBlacklist(data) {
  const threads = getThreads()
  threads.forEach(thread => {
    if (data.threadTitleIsBlacklisted(getTitle(thread))) {
      thread.style = 'display: none;'
    }
  })
}
function getThreads() {
  return document.querySelectorAll('.js-threadList>.structItem')
}
function getTitle(thread) {
  try {
      return thread.querySelector('.structItem-title').innerText
  } catch(e) {
      return ''
  }
}
```

### Versão com código separado
```javascript
function ignoreUserThreads(data) {
  const threads = getThreads()

  threads.forEach((thread) => {
    if (data.needIgnoreThisUser(getAuthorName())) {
      thread.style = 'display: none;'
    }

    function getAuthorName() {
      return thread.getAttribute('data-author');
    }
  })
}

function muteBlacklist(data) {
  const threads = getThreads()

  threads.forEach((thread) => {
    if (data.threadTitleIsBlacklisted(getTitle(thread))) {
      thread.style = 'display: none;'
    }
  })
}

function getThreads() {
  return document.querySelectorAll('.js-threadList>.structItem')
}

function getTitle(thread) {
  try {
      return thread.querySelector('.structItem-title').innerText
  } catch(e) {
      return ''
  }
}
```
O que você prefere? Eu particularmente prefiro a segunda opção

## Seja gente fina com o leitor do código, tente colocar coisas relacionadas o mais perto possível
Uma das coisas que eu mais detesto é ter que ficar indo pra cima e pra baixo no código pra entender o que está acontecendo nele. E uma das formas de mitigar isso é deixando códigos que se relacionam, o mais próximo um do outro.

Eu particularmente gosto de ler código de cima pra baixo, como se fosse um texto mesmo, então se a função *A* chama a função *B*, eu sempre tento deixar a função *A* em algum lugar por cima da função *B*, e nunca o contrário. Por exemplo:


```python
def a:
    b()

def b:
    #do something
```

Claro que nem sempre isso é possível por N questões, mas é sempre bom tentar fazer isso pra facilitar a vida do leitor 😁.

## TL;DR
Geralmente é melhor seguir um estilo de código já definido do que inventar moda. Baixe um formatador de código automático (como o prettier ou phpcbf, por exemplo).

Separe os os seus blocos de código com espaços, a leitura desse artigo ficaria péssima se não tivesse espaços separando os blocos de texto, por que você acha que seria diferente com o seu código?
