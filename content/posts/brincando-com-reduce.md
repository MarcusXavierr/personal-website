---
title: "Entendendo A Função Reduce"
date: 2022-08-05T16:38:45-03:00
summary: "A função reduce é bastante incompreendida por muitos programadores. Tendo isso em vista hoje vamos mexer um pouco com essa função e implementarmos a nossa própria versão para clarificar mais o que essa função faz debaixo dos panos"
tags: ['Code', 'Functional_programming']
featuredImage: "https://i.ibb.co/xCGThbJ/reduce.jpg"
---
A parte 2 se encontra aqui. [Entendendo a função reduce - Parte 2](https://marcusxavier.dev/posts/brincando-com-reduce-pt2/)

## Introdução
Faz um tempo que eu não escrevo nenhum blogpost. Nesse período eu andei estudando haskell e achei uma função que bem interessante chamada **fold**, mas essa função é mais conhecida como **reduce**. O que faz essa função ser bem interessante é o fato de que ela contém bastante informação em pouquíssimas linhas de código.

## Implementando um simples problema
Só pra mostrar um pouco do que o reduce é capaz de fazer, vamos imaginar um problema hipotético: *Você recebe uma lista de compras que um cliente fez, e precisa somar todos os valores dessa lista.*

{{< admonition note "Aviso" >}}
Muitas linguagens fornecem funções pra somar os valores de uma lista, mas vamos implementar a nossa própria função só por questões didáticas.

Primeiro eu vou implementar essa função no bom e velho PHP escrevendo sem usar reduce, só com um foreach mesmo.

Depois vou implementar usando Javascript e reduce, e por fim vou mostrar como seria essa função em haskell

{{< /admonition >}}

#### Versão no PHP
```php
// Crio uma lista simples só pra poder testar depois
$array = [1,2,3,4,5,6,7,8,9,10];

//Aqui vou fazer um foreach simples e somar os valores do meu array
function sumArrayValues($array) {
    $value = 0;
    foreach($array as $item) {
        $value += $item;
    }
return $value;
}
sumArrayValues($lista);
//Output: 55
```
#### Versão JS com reduce

```javascript
//Crio a lista
let lista = [1,2,3,4,5,6,7,8,9,10]

//Sim, a função só tem uma linha
let somador = (acumulador, valor) => acumulador + valor
lista.reduce(somador, 0)
// Output: 55
```

#### Versão em haskell
```haskell
lista = [1..10] -- Cria uma lista de 1 até 10
foldl (\x acumulador -> x + acumulador ) 0 lista
-- Output: 55
-- Ou então nós poderíamos ter feito isso aqui que daria o mesmo resultado
foldl (+) 0 lista
```
Como você pode ver, o reduce torna seu código muito menos verboso, e na minha opinião, esse jeito minimalista e declarativo é bem elegante, mas creio que seja justamente por isso que a função **fold** (mais conhecida como **reduce**) seja tão incompreendida.

Tendo isso em vista, vamos então entender com calma o que a função reduce está fazendo por baixo dos panos.
Pra entender como o reduce funciona, nós vamos implementar a nossa própria versão dele, da forma mais explicita possível.

## Implementando nosso próprio reduce
Vou usar javascript, mas funcionaria com praticamente qualquer linguagem.
Aqui está a definição original do array.reduce

```javascript
array.reduce(function(total, currentValue, currentIndex, arr), initialValue)
```
Mas como os parâmetros `currentIndex` e `arr` são opcionais, vou fazer uma definição mais simples para explicar de forma mais didática.

Obs: O parâmetro initialValue também é opcional, mas eu considero ele importante demais para omitirmos na nossa implementação

```javascript
array.reduce(function(acumulador, currentValue), initialValue)

//mas vamos criar uma função que recebe o array como ultimo parâmetro
meuReduce(function(acumulador, valorAtual), valorInicial, lista)
```
Agora vamos implementar logo a nossa versão simplificada do reduce

```javascript
function meuReduce(funcaoAnonima, acumulador, lista) {
    // No reduce eu basicamente vou percorrer a minha lista e ir salvando no meu acumulador
    // o resultado da minha função anônima
    // Eu usei um for, mas poderia ser feito usando recursão também.
    for(let i = 0; i < lista.length; i++) {
        //Aqui o meu acumulador vai receber o resultado da computação da minha função anônima
        // Veja que eu estou usando "=" e não "+=".
        // Por isso estou passando o meu acumulador pra minha função
        // A ideia é como se ele estivesse sendo incrementado
        acumulador = funcaoAnonima(acumulador, lista[i])
    }
    //Por fim eu simplesmente retorno o meu acumulador
    return acumulador
}
```
E se eu chamar a minha nova função, tudo deve funcionar

```javascript
//Se você quiser, pode copiar o codigo acima e esse aqui e colar no console do navegador pra ver o resultado também
const somador = (acumulador, valor) => acumulador + valor

const multiplicador = (acumulador, valor) => acumulador * valor

const dobra_array = function(acumulador, valor) {
    acumulador.push(valor * 2) //Aqui o meu valor inicial precisa ser um array vazio
    return acumulador
}

let lista = [1,2,3,4,5,6,7,8,9,10]
meuReduce(somador, 0, lista)
// Output: 55

meuReduce(multiplicador, 1, lista) //o valor inicial precisa ser 1, pois se for 0 vai zerar a multiplição
// Output: 3628800

meuReduce(dobra_array, [], lista)
// Output: [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]

//Se você fizer lista.reduce(dobra_array, []) o resultado será o mesmo
```

Sim, eu diria que uma das principais funções do reduce é pegar uma lista de itens e retornar um único item. Como nas duas primeiras funções onde nós pegávamos umas lista de inteiros e **REDUZÍAMOS** a lista a somente um inteiro.

Mas também é possível que nosso reduce receba uma lista e retorne outra lista. Basta que o valor inicial seja um array vazio, para que o acumulador consiga usar a função push para colocar itens dentro de si.

## Recapitulando
Bom, esse artigo já está ficando grande demais, então vamos somente recapitular o que aprendemos antes de finalizar:

* O reduce (mais conhecido como fold em algumas linguagens) é uma função que pode fazer muitas coisas com poucas linhas de código
* A função reduce é bastante usada para pegar uma lista de valores e **reduzir** essa lista a somente um item.
* O reduce recebe uma função anônima que será aplicada ao acumulador e a cada item da sua lista, recebe um valor inicial para o acumulador, e recebe a própria lista
* A função anônima deve receber como parâmetro, no mínimo o seu acumulador e o item atual da sua lista (lembre-se que a função vai ser chamada dentro de um loop que vai varrer toda a sua lista). Além desses dois itens obrigatórios algumas linguagens te permitem colocar mais parâmetros na sua função anônima, mas esses são opcionais.

E é isso pessoal. Foi bom escrever um pouco sobre essa função que é bem confusa de se entender quando vemos ela pela
