---
title: "Brincando Com A Função Reduce"
date: 2022-08-01T16:38:45-03:00
draft: true
---

Faz um tempo que eu não escrevo nenhum blogpost. Nesse periodo eu andei estudando haskell e achei uma função que bem interessante chamada **fold**, mas essa função é mais conhecida como **reduce**. O que faz essa função ser bem interessante é o fato de que ela contém bastante informação em pouquissimas linhas de codigo.
Só pra mostrar um pouco do que o reduce é capaz de fazer, vamos imaginar um problema hipotetico: *Você recebe uma lista de compras que um cliente fez, e precisa somar todos os valores dessa lista.*

{{< admonition note "Aviso" >}}
Muitas linguagens fornecem funções pra somar os valores de uma lista, mas vamos implementar a nossa propria função só por questões didáticas.

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

```js
//Crio a lista
let lista = [1,2,3,4,5,6,7,8,9,10]

//Sim, a função só tem uma linha
lista.reduce((acumulador, valor) => acumulador + valor)
// Output: 55
```

#### Versão em haskell
```haskell
lista = [1..10] -- Cria uma lista de 1 até 10
foldl (\x acumulador -> x + acumulador ) 0 lista
-- Output: 55
-- Ou então nós poderiamos ter feito isso aqui que daria o mesmo resultado
foldl (+) 0 lista
```
Como você pode ver, o reduce torna seu código muito menos verboso, e na minha opinião, esse jeito minimalista e declarativo é bem elegante, mas creio que seja justamente por isso que a função **fold** (mais conhecida como **reduce**) seja tão incompreendida.
Tendo isso em vista, vamos então entender com calma o que a função reduce está fazendo por baixo dos panos.

