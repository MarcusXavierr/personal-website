---
title: "Guia fundamental da programação orientada a objetos - Final"
date: 2022-08-01T09:30:48-03:00
tags: ['OOP', 'Code']
---
Agora que nós já vimos os pilares da POO e a sua importância, está na hora de abordar mais alguns aspectos importantes de orientação a objetos neste último artigo. Vamos falar um pouco sobre classes abstratas, interfaces e classes finais (conhecida como classe selada em algumas linguagens).

## O que são classes abstratas?

<img src="https://dev-to-uploads.s3.amazonaws.com/uploads/articles/59l8s16n32mccql3g3q7.png" alt="drawing" width="500"/>

Elas são classes que não podem ser instanciadas. Ou seja, você não pode criar objetos a partir de uma classe abstrata. Aí você pode pensar: “Mas pra que serve uma classe que não pode virar objeto?”. Bom, você ainda pode fazer herança com uma classe abstrata, e é aí que vem a grande utilidade delas. Imaginemos que eu queira implementar algumas classes de Pagamento. Uma pra pagamento via cartão de crédito, outra para pagamento com boleto, e por fim uma para pagamento via pix.

Muitas coisas nessas três formas de pagamento são bem parecidas, não? Sempre existe uma pessoa que paga, um horário em que o pagamento e também o seu valor. Essas informações podem, e devem ser **abstraídas**, não faz o menor sentido ficar repetindo coisas atoa.

E é aí que nós podemos criar uma classe **PagamentoBase** que não vai ser capaz de realizar um pagamento, mas terá a responsabilidade de guardar essas partes comuns a todos os tipos de pagamento.

E não faz o menor sentido criar uma instância de **PagamentoBase**, já que ele não é uma classe responsável por fazer o pagamento em si, mas para abstrair ao máximo coisas para as outras classes usarem. Então nesse caso, nós podemos marcar essa classe como sendo abstrata, então será **impossível** de criar um objeto a partir de PagamentoBase.

Claro, classes abstratas também podem ter outras responsabilidades dependendo de como você as implementa, mas na minha visão, a principal função delas é abstrair coisas para que outras classes “normais” consigam usar e se beneficiar, aumentando a reusabilidade do código.

Além disso, lembre-se que também é possível sobrescrever os métodos da nossa classe abstrata, então, por exemplo, eu posso sobrescrever o método de notificar o usuário quando o pagamento é feito com sucesso.

Abaixo implemento o exemplo citado nos parágrafos anteriores.
```php
<?php

abstract class PagamentoBase
{
    protected float $valor;
    protected DateTime $dataPagamento;
    //etc...

    public function notificarUsuarioSobrePagamento(): void
    {
        //imagine uma implementação
    }
    //Temos mais metodos abaixo que estão invisiveis...
}


class PagamentoViaCartao extends PagamentoBase
{
    private int $numeroCartao;
    //etc...

    //imagine que tem alguns métodos aqui
}

class PagamentoViaBoleto extends PagamentoBase
{
    //Imagine que eu implementei alguns metodos aqui também
}

class PagamentoViaPix extends PagamentoBase
{
    public function notificarUsuarioSobrePagamento(): void
    {
        //A implementação é diferente pois como o pix é instantaneo
        //eu preciso enviar essa notificação o mais rápido possivel.
    }

    //temos mais metodos e atributos nessa classe...
}
```

## O que são interfaces?

<img src="https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kbj6h0bhawkudys94dgo.png" alt="drawing" width="200"/>

A primeira coisa que você precisa ter em mente é que interface **NÃO** são classes, logo é **impossível** instanciar objetos a partir da interface. Mas você pode pensar, “se eu não consigo criar objetos a partir de interfaces, como que eu vou usar elas no meu código?”, e eu vou te responder que interfaces são usadas como se fossem **”contratos”**.

Quando você assina um contrato, você é **obrigado** pela lei a cumprir o que está escrito no contrato. Ou seja, o contrato é uma forma de **garantir** que tu cumpra o que prometeu cumprir. Interfaces funcionam de forma parecida.

Digamos que eu tenha uma classe **Carro**, e uma classe **Moto**, e você concorda comigo que todo veículo precisa andar para ser útil, não é mesmo? Então, nessa situação, se essas classes não tiverem um método andar(), elas serão inúteis pra mim, pois um veículo que não anda é inútil.

E é justamente nesse cenário que as interfaces aparecem pra salvar o dia, eu posso criar uma interface chamada **“ContratoVeiculo”** e lá colocar o método andar(). Logo após isso eu faço a minha classe implementar essa interface. Isso vai fazer com que essa classe seja **obrigada** a implementar os métodos definidos na interface.

A interface não tem a obrigação de dizer como um método deve funcionar (e nem pode fazer isso), por isso em interfaces nós não implementamos o método, apenas escrevemos a assinatura do método, ou seja, escrevemos o seu nome, quais parâmetros ele recebe, e qual o tipo do retorno desse método.

É **obrigação** da classe que implementa esse interface dizer como vai ser toda a lógica do meu método, mas esse método precisa respeitar a assinatura da interface, então ele precisa receber os mesmos tipos de parâmetros e retornar o tipo de valor definido na interface.

A vantagem em ter essa abordagem (interfaces serem proibidas de ditar o funcionamento dos métodos) é que eu vou poder usar a mesma interface para classes diferentes, que terão lógicas de funcionamento diferentes. Como você já deve saber, carros e motos andam de formas diferentes, mas como a minha interface **ContratoVeiculo** é “agnóstica”, eu consigo usar ela tanto na classe **Carro** quanto na classe **Moto**.

Abaixo eu mostro um trecho de código PHP que implementa essas classes e interface citados acima.
```php
<?php
interface ContratoVeiculo
{
   public function andar(): void;
   //Aqui eu digo que quero uma função chamada andar
   //que não recebe nenhum parametro e retorna void
   //Ou seja, não retorna nada
}

class Carro implements ContratoVeiculo
{
   //Imagine que temos varios atributos
   //e varios metodos aqui
   public function andar(): void
   {
       print("O carro está andando\n");
   }
}

class Moto implements ContratoVeiculo
{
   //Imagine que temos varios atributos
   //e varios metodos aqui
   public function andar(): void
   {
       print("Andando como uma moto\n");
   }
}
```
## E o que são classes seladas?
E por fim, vamos falar um pouco sobre as classes finais, ou classes seladas, o nome varia de acordo com a linguagem de programação que você estiver usando (em PHP e Java é classe final e no C# é classe selada).
Lembra que as classes abstratas não podem ser instanciadas, mas podiam ser herdadas? Com as classes finais acontece o oposto, elas podem ser instanciadas, mas não podem ser herdadas.

A razão para se usar uma classe desse tipo é que a herança também tem o seu ônus, e pode ser que em algum caso especifico você quer evitar que algum outro programador extenda a sua classe. Deixo abaixo a [opinião de um usuário do stackoverflow](https://pt.stackoverflow.com/a/227216) sobre o assunto.

_“É uma forma de dizer que esta classe não pode ser herdada, o que costuma ser uma boa ideia. Desenvolver classes preparadas para serem herdadas é muito mais difícil, a manutenção seguinte se torna um fardo. Acho até que o padrão deveria ser a classe não herdável.”_

Abaixo deixo um exemplo de classe final no PHP.
```php
<?php

abstract class CarroGenerico
{
   protected int $quilometragem;
   public function ligar(): void
   {
       //logica para ligar o carro
   }
}

final class ToyotaCorolla extends CarroGenerico
{
   //implementa coisas especificas de um Corolla
   //A ideia aqui, é que chegamos na nossa classe final
   //Não faz muito sentido extender um Corolla
   //pois ele já é o produto FINAL
}
 ```


## Conclusão
Bom, com esse artigo nós cobrimos todas as partes que eu considero fundamentais para que você comece a usar a orientação a objetos. Existem muitas coisas que não foram cobertas nessa série e que estão relacionadas com POO, como por exemplo funções amigas e generics. Mas falar disso fugiria um pouco da finalidade dessa série, que é cobrir os fundamentos. Vejo vocês em próximos artigos! :)
