---
title: "Entendendo A Função Reduce - Parte 2"
date: 2022-11-28T21:28:07-03:00
draft: false
---
Eu dei uma relida no artigo sobre reduce que tinha publicado anteriormente, e percebi algumas coisas que eu gostaria de adicionar, vamos lá.

## Tipos de retorno no reduce
O reduce recebe uma lista do tipo `X` (onde `X` pode ser um objeto, um número, qualquer coisa) e geralmente te retorna um único valor do tipo `X`, vamos dizer que isso é do tipo `[X] -> X`.

Mas isso não é obrigatório. É bem possível que o reduce te retorne um outro tipo (nesse caso seria `[X] -> Y`), ou seja, pode receber uma lista de inteiros e retornar uma string ou um objeto, por exemplo.

Vou exemplificar com código logo abaixo. Aqui eu tenho duas funções. Na primeira eu recebo uma lista de números, e retorno um único numero (que é a soma de todos os valores dessa lista). Isso seria `[X] -> X`.

Mas na segunda função, eu recebo uma lista de strings e retorno uma lista de objetos, isso seria `[X] -> Y`.

```javascript
//Tipo [X] -> X
[1,2,3,4].reduce((acc, value) => acc + value)
//retorna 10

//Tipo [X] -> Y
//Aqui eu recebo uma lista de pessoas, e quero criar uma lista de objetos
//Onde cada pessoa está com o score zerado
['nome', 'aleatorio'].reduce((acc, value) => acc.concat({name: value, score: 0}), [])
//retorna [ { name: 'nome', score: 0 }, { name: 'aleatorio', score: 0 } ]
```

No Javascript, **geralmente**, se você chama seu reduce **sem** passar um valor inicial, ele será do tipo `[X] -> X`. Já se você quiser fazer algo do tipo `[X] -> Y`, **geralmente**, terá que passar um valor inicial, que é o segundo parametro da função reduce, logo após a sua função anônima. No exemplo acima eu coloquei um array vazio.

## Debuggando o reduce
Bom, a ideia aqui é criar um reduce, e ir passo a passo pra ver o que ele está fazendo. Primeiro vou reimplementar a função somador e ver como os valores vão mudando a cada passo, como aconteceria num debugger mesmo. Depois explicarei um exemplo um pouco mais complexo (e útil).

```javascript
const values = [1,2,3,4,5]
values.reduce((acc, value) => acc + value)

//isso aqui, na verdade é
[1,2,3,4,5].reduce((acc, value) => acc + value)
```
Agora vamos lá. Se você não passar um valor inicial (como eu não passei), o JS irá pegar o primeiro item do array pra ir pro `acc`, e vai começar a rodar a partir do segundo, ou seja, vai colocar o segundo item no `value`. Então dando um passo e nosso estado seria isso aqui

```javascript
//              1     2        1   +  2 = 3
[3,4,5].reduce((acc, value) => acc + value)
```
O resultado dessa soma irá sobrescrever o valor de acc, que passará a ser 3. Vamos dar mais um passo. Atualmente o nosso `acc` é um 3, e o nosso `value` irá pegar o valor do próximo item do array. Que por coincidência é um 3 também

```javascript
//            3    3         3   +  3 = 6
[4,5].reduce((acc, value) => acc + value)
//Agora vamos rodar até a lista esvaziar

//          6    4         6   + 4 = 10
[5].reduce((acc, value) => acc + value)

//          10   5        10  + 5 = 15
[].reduce((acc, value) => acc + value)
```
Certo, agora o nosso `acc` vale 15, e a nossa lista está vazia, o que acontece agora? Bom, quando a lista está vazia, o reduce simplesmente pega o que está no `acc` e retorna para o usuário. É por isso que o exemplo abaixo retorna o valor 1, e não o valor 500.

```javascript
//Eu vou passar 1 como valor inicial para a minha função.
//ou seja, o acc começará valendo 1
//Mas como a minha lista está vazia, o código return 500 nem rodará,
//o reduce vai retornar 1 logo
[].reduce((acc, value) => {return 500}, 1)
//retorna 1
```

## Resolvendo um problema possivelmente real
Bom, agora vem o "debugging" mais complexo que citei anteriormente. Imaginemos que você recebe uma lista de objetos que representam a pontuação dos usuários, e você quer definir qual usuário ganhou. Existem várias formas de resolver esse problema, e uma delas é o reduce.

```javascript
const scores = [
{ name: 'Huguinho', score: 20},
{ name: 'Zezinho', score: 25},
{ name: 'Luizinho',	score: 15}]
```

Agora que nós temos a lista de pontuações, é só rodar no nosso reduce
```javascript
scores.reduce((previousValue, actualValue) => {
    if (actualValue.score > previousValue.score) return actualValue
    else return previousValue
})
```
Como eu não quero "acumular" um valor nesse caso, eu preferi chamar o meu primeiro parâmetro como "previousValue" (valorAnterior) e o segundo como "actualValue" (valoarAtual).

A ideia aqui é basicamente olhar se o meu `actualValue` tem um score maior que o `previousValue`, se ele tiver, eu retorno o `actualValue`, sobrescrevendo o que tem no `previousValue`.

Agora, caso o score do meu `previousValue` seja maior ou igual ao do meu `actualValue` eu retorno o `previousValue`, ou seja, eu basicamente estou deixando o mesmo valor que tinha no `previousValue`. Não estou tratando o caso de empate aqui.

Coloquei esse else pra deixar claro que retorna um ou outro, nesse caso ele é opcional.

```javascript
//Vamos dar o primeiro passo então
//como não passei valor inicial o reduce faz o mesmo esquema de antes
//que é pegar o primeiro item e colocar no acumulador (valor anterior),
//e botar o segundo item no valor atual

const scores = [
{ name: 'Huguinho', score: 20},
{ name: 'Zezinho', score: 25},
{ name: 'Luizinho',	score: 15}]

//                              {name: 'Huguinho'...} {name: 'Zezinho'...}
[{ name: 'Luizinho', score: 15}].reduce((previousValue, actualValue) => {
//                 25     >              20 é true
    if (actualValue.score > previousValue.score) return actualValue
    else return previousValue
    //retorno o meu actualValue (pontuação do zezinho)
})

```
Como o score do meu valor atual é **maior** do que o do valor anterior, eu retorno o meu valor atual, sobrescrevendo o `previousValue` com ele. Agora o nosso próximo passo está assim.

```javascript
//   {name: 'Zezinho'...} {name: 'Luizinho'...}
[].reduce((previousValue, actualValue) => {
//                 15     >              25 é false
    if (actualValue.score > previousValue.score) return actualValue
    else return previousValue
    //retorno o previousValue (pontuação do zezinho)
})

```
E como o meu valor atual não tem um score maior do que o anterior, vamos retornar o valor anterior para ser salvo no nosso acumulador (ou `previousValue` nesse caso), basicamente deixando do mesmo jeito que estava.

O reduce irá rodar novamente e verá que a lista está vazia, aí ele simplesmente retornará o que tiver no meu acumulador. E nesse caso isso é a pontuação do zezinho. `{ name: 'Zezinho', score: 25}`.

Se você quiser tirar a prova, abra o node ou o console do navegador e rode o código completo abaixo

```javascript
const scores = [
{ name: 'huguinho', score: 20},
{ name: 'zezinho', score: 25},
{ name: 'luizinho',	score: 15}]

scores.reduce((previousValue, actualValue) => {
    if (actualValue.score > previousValue.score) return actualValue
    else return previousValue
})
```

## Conclusão
Bom, esse artigo serviu mais para explicar melhor como o reduce computa os
valores da lista e reduz um `[X]` em outra coisa como um `X` ou um `Y`. Ainda
existem muitas coisas para serem abordadas, por exemplo: como map e filter são
derivados do reduce, como é uma recursão por trás do reduce, como usar os
outros dois parâmetros da função interna do reduce que nós ainda não usamos.
Mas por hoje ficamos por aqui. Eu posso abordar esses temas em artigos futuros.
