---
title: "Guia fundamental da programação orientada a objetos - Parte 2 | Os pilares"
date: 2022-08-01T09:29:19-03:00
tags: ['OOP', 'Code']
featuredImage: "https://res.cloudinary.com/practicaldev/image/fetch/s--JmAHtL5a--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6431uumz3l7wn7vtxs56.jpg"
---

É impossível falar de programação orientada a objetos sem citar seus famosos quatro pilares. Seguir esses pilares é muito importante para que você possa conseguir todos os maravilhosos benefícios da POO. No artigo de hoje eu vou falar exclusivamente sobre esses pilares, e sempre que pertinente vou exemplificar com código e imagens.
Seria ótimo que você deixasse seu feedback nos comentários.
## Quais são os pilares da POO?
A POO possui alguns pilares que devem ser seguidos para que possamos começar a obter os benefícios oferecidos por esse paradigma da maneira mais correta possível.
### Encapsulamento
Basicamente é o pilar que te permite deixar seu código mais isolado do resto, então quando você baixa uma lib no seu projeto e usa um objeto dessa lib com vários métodos úteis você não preciso fuçar na classe dela, já vem tudo encapsulado certinho pra você usar, só precisa chamar as funções desse objeto e deixar a mágica acontecer sem se preocupar se o seu código vai bugar o código da lib, porque se ela estiver encapsulada isso não vai acontecer.

Vou usar uma pilha como exemplo. Dentro da pilha tem vários elementos químicos, que poderiam danificar sua pele, por esse motivo nós colocamos eles dentro de uma **cápsula**, que tem dois terminais.
Então essa **cápsula** tem o único objetivo de te proteger da pilha, e ao mesmo tempo proteger a pilha de você, pois você poderia danificar a pilha caso ficasse tocando no catodo dela.
Essa **cápsula** da pilha só expõe o necessário dela (que são os terminais negativo e positivo) e esconde todo o resto. **Encapsulamento** na POO é exatamente isso.


<img src="https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zbdo67wdtqj5c3lghe32.jpg" alt="drawing" width="600"/>

### Herança
É o pilar que te permite reutilizar o seu código, fazendo uma classe reaproveitar o código de outra classe, como um filho que herda os genes dos pais, então a classe que herda é a **classe filha (child class)**, e a classe que serve como base na herança é a **classe mãe (parent class)**.

Vamos imaginar que você está escrevendo um programa para realizar cobranças de um e-commerce. E nesse caso você vai aceitar pagamento por cartão de crédito, boleto e pix. É possível perceber que parte dos pagamentos vão ser iguais (todos tem um valor, informações do usuário, etc), e têm partes que são diferentes (boleto tem data de vencimento e pagamento por cartão não tem).

Então ao invés de escrever o código do zero pros três tipos de pagamento, ou escrever tudo junto, de forma bagunçada, nós podemos criar uma classe mais genérica, chamada pagamento, e classes para os pagamentos com cartão de crédito, boleto e pix, fazendo essas classes herdarem a genérica, reaproveitando código.

Abaixo vou deixar uma imagem como exemplo e um trecho de código.

<img src="https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rgyn49pqpctmd63mhjzh.png" alt="drawing" width="500"/>


```c#
public class Pagamento
{
   public int Id;
   public string NomeDoPagador;
   public bool FoiPago;
   //… mais várias propriedades aqui

}

public class PagamentoCartaoCredito : Pagamento
{
   //Eu ainda tenho as propriedades do Pagamento aqui
   // ou seja, posso acessar o Id, e o nome do pagador
   public int NumeroCartao;

   public void VerificarCartao()
   {
       //verificando o cartão
   }
}

public class PagamentoBoleto : Pagamento
{
   //Eu ainda tenho as propriedades do Pagamento aqui
   // ou seja, posso acessar o Id, e o nome do pagador

   public DateTime vencimento;

   public void VerificarSeVenceu()
   {
       //verificando se o boleto não venceu
   }

}
```
### Abstração
O pilar da abstração vai deixar seu código o mais simples possível. Vamos imaginar que você precisa escrever o código de cadastro de alunos em uma escola, logo você irá precisar criar uma classe do tipo pessoa, mas uma pessoa tem milhares de características e pode realizar milhões de ações diferentes.

Então, pra evitar que a sua classe fique com 10000 linhas usamos o conceito de abstração, que é focar somente nas características e ações importantes no contexto do programa.

Para uma escola, a altura, peso ou cor da pele de um aluno não faz a menor diferença, mas é importante saber as notas desse aluno e o seu nome, então focaremos somente nas últimas características. A mesma ideia se aplica na hora de escolher quais ações representar usando os métodos.

```c#
public class Aluno
{
   //Aqui vou focar somente no essenssial
   //Um aluno é uma pessoa, logo tem centenas
   //de caracteristicas, mas vou focar em somente três
   public string Nome;
   public int Idade;
   public int[] Notas;
   //Isso acima é um vetor de notas
   //Ou seja, varias notas juntas na mesma estrutura

   public void Aprovar() { }

   public void Reprovar() { }
}
```
### Polimorfismo

Polimorfismo quer dizer **“muitas formas”**, e é exatamente isso que você entendeu, os métodos das classes podem ter muitas formas. Eu vou explicar de forma mais detalhada logo logo, mas preciso falar primeiro que existe mais de um tipo de polimorfismo. Nesse artigo vou focar nos dois principais, a **sobrescrita** e **sobrecarga**.

#### >Sobrescrita

Como o próprio nome já diz, sobrescrita é uma forma de escrever por cima de algo que já existe, e no nosso contexto, é a habilidade de uma classe filha para **modificar o comportamento** de um método (função) da classe mãe. A vantagem disso é poder reutilizar toda parte útil da nossa classe mãe, mas ao mesmo tempo ter a capacidade de alterar as partes que nós desejamos alterar por qualquer motivo que seja.

Isso é literalmente o melhor dos dois mundos, pois economizamos código escrito e também temos total flexibilidade para deixar nossas classes filhas do jeito que queremos. Abaixo vou mostrar um trecho de código onde faz sentido esse tipo de polimorfismo.

Eu tenho uma classe **Mamífero**, e você vai concordar comigo que todos (ou quase todos) os mamíferos emitem som, não é mesmo? Então, por isso criamos um método **Falar()** nessa nossa classe. Mas, caso eu crie um **Cachorro** e um **Gato** a partir dessa classe **Mamífero**, eu vou precisar modificar esse método **Falar()**, pois como todos sabemos, cachorros e gatos “falam” de formas diferentes, um late e outro mia.

```c#
public class Mamifero
{

   public string Especie;
   public int Idade;


   public virtual void Falar()
   {
       Console.WriteLine("Fala genérica de um animal");
       // printa Fala genérica de um animal na tela
   }
}

public class Gato : Mamifero
{
   public override void Falar()
   {
       Console.WriteLine("Miau");
       // printa Miau na tela
   }
}

public class Cachorro : Mamifero
{
   public override void Falar()
   {
       Console.WriteLine("Au au");
       //printa au au na tela
   }
}
```

#### >Sobrecarga de métodos

Esse é um conceito que te permite criar variações de um mesmo método, ou seja, você pode ter vários métodos com nomes iguais dentro de uma mesma classe, realizando ações diferentes. Pode ter ficado confuso, mas eu vou mostrar agora uma situação onde a sobrecarga pode ser útil, é um exemplo bem bobo, mas vai tornar esse conceito mais claro pra você.

Imagina que vamos criar uma classe **Calculadora**, e ela tem um método de **Somar()**. O que esse método faz é basicamente pegar dois inteiros e retornar a soma deles
```c#
public class Calculadora{
   public int Somar(int a, int b)
   {
       return a + b;
   }
}
```
Mas se eu quiser somar 3 números, como eu faço? Caso eu modifique esse método acima, todos os meus trechos de código que usavam essa função vão quebrar, pois eles só estavam somando dois números, e a função passou a querer somar três números.

E é nesse momento que a sobrecarga vem salvar o dia. Eu tenho a opção de simplesmente criar outro método chamado **Somar**, que recebe três números, e o C# é inteligente o suficiente pra saber quando ele chama o método que recebe dois parâmetros, e quando chamar o método que recebe três.

```c#
public class Calculadora
{
   public int Somar(int a, int b)
   {
       return a + b;
   }

   // Novo método
   public int Somar(int a, int b, int c)
   {
       return a + b + c;
   }
}
```
Você vai ver isso bastante em métodos “nativos” do .NET Core (uma plataforma de desenvolvimento que usa o C#), a sobrecarga de métodos (mais conhecida como **overload**) é algo extremamente comum nessa plataforma.

## Conclusão
No artigo de hoje nós vimos o coração da orientação a objetos: os seus famosos pilares. Entender bem esses conceitos é fundamental para aplicar bem a POO em problemas reais do dia a dia, por isso recomendo salvar esse texto para ler futuramente de novo depois que você tiver mais experiência programando nesse paradigma.

No próximo artigo veremos mais conceitos importantes da POO, como classes abstratas, interfaces, classes “seladas” e se sobrar tempo vou mostrar como os métodos GET e SET do C# podem ser escritos de forma elegante e enxuta.


