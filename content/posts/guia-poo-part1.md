---
title: "Guia fundamental da programação orientada a objetos - Parte 1"
date: 2022-07-31T19:09:07-03:00
tags: ['OOP', 'Code']
featuredImage: "https://res.cloudinary.com/practicaldev/image/fetch/s--tId2cKmG--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/42a7ietajfinnnu15jb8.jpg"
series: ['Guia Fundamental de POO']
---

A ideia dessa série de posts é cobrir as partes que eu considero mais fundamentais na orientação a objetos, e no artigo de hoje iremos começar com os conceitos mais básicos desse paradigma.
## Afinal, o que são classes e objetos?
Explicando de forma resumida, objetos podem ser considerados estruturas que armazenam dados (propriedades), ações (métodos) e o estado atual desse objeto, então podem ter dois objetos do mesmo "tipo" com valores diferentes em suas propriedades, pois eles têm estados diferentes.

O seu uso mais comum é armazenar essas informações para representar coisas do mundo real, como uma pessoa, um carro, ou um pagamento pelo cartão de crédito.  Ou seja, eu posso ter um objeto do tipo Pessoa que vai armazenar dados como, por exemplo, nome, altura e idade, e esse objeto pode realizar ações como compartilhar esse artigo com seus amigos ;).

Já as classes são como o **molde** desses objetos, como se fosse um template pra eles, então na classe eu defino todas as informações que meu objeto irá armazenar (propriedades) e também posso definir todas as ações que meu objeto irá realizar (métodos).

Então já dá pra perceber que propriedades são parecidas com variáveis, e métodos são parecidos com funções.


![O molde roxo é uma “classe” e o biscoito é um “objeto”](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h3q2usjgf7rc8j80c3ju.png)

O molde roxo é uma **“classe”** e o biscoito é um **“objeto”**

Abaixo mostro visualmente o que são classes e objetos com exemplos em C#

```c#
public class Pessoa //Aqui eu defino o molde para os meus objetos
{

    //Em próximos artigos irei explicar pra que serve
    //esse "public" atrás das coisas

    //* Propriedades (Os dados que meu objeto irá armazenar)

    public string Nome;
    public int Idade;
    public string CPF;
    public double Altura;

    //* Métodos (As ações que meu objeto irá realizar)
    public void CompartilharPost()
    {
        //Compartilhando o post
    }
}
```

E por fim, essa imagem abaixo explica bem o que seria o **estado** de um objeto. Basicamente é como o objeto está agora. Temos vários bolos, um deles tem a propriedade **Sabor** no estado Morango, outro é do **Sabor** chocolate, e por aí vai.


![Estados no poo](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/okxeascc90a9ok4svagh.jpg)

## Por que eu deveria usar o paradigma de orientação a objetos?

Existem algumas vantagens em se usar POO (Programação Orientada a Objetos) e, na minha opinião, a principal vantagem é **facilitar a vida do desenvolvedor na hora de fazer manutenção**.
Não está muito claro agora, mas a POO permite que você reaproveite muito código e isso garante que seu software seja mais fácil de alterar. Se você ainda não linkou orientação a objetos com facilidade em manutenção não tem problema, com o decorrer dessa série isso vai ficar claro.

Outra grande vantagem é que a orientação a objetos pode ser usada para representar objetos do mundo real, sejam eles “**físicos**” como um pessoa, ou um carro, ou seja ele **abstrato**, como um pagamento do cartão de crédito.

A grande sacada é que muitas coisas no mundo real, senão todas, possuem características (propriedades) e realizam ações (métodos), logo, é possível representar essas coisas com POO.

Fora isso existem vantagens como deixar seu código mais organizado, por exemplo, principalmente se você aplicar o SOLID (assunto pra outro post), e isso te permite focar uma funcionalidade de cada vez no seu código, podendo muitas vezes fazer alterações que não vão repercutir no resto do seu programa de forma inesperada (olha aí mais uma coisa ajudando na manutenção).



## Por que meu código precisa ser fácil de dar manutenção?
A mensagem mais importante desta seção do artigo é que um software não é algo **estático**, o software é algo que está **sempre evoluindo**. Eu aposto que você vê seus aplicativos favoritos recebendo atualizações com uma certa frequência, sejam elas correções de bugs ou adições de novas funcionalidades, e isso é a coisa mais comum no mundo do desenvolvimento, pois como eu disse acima, software é uma coisa **dinâmica**.

Logo, o código que você escrever vai ser modificado no futuro,seja porque você está atualizando a versão do framework, ou porque deseja adicionar novas features, por exemplo. E se o seu código for **difícil** de dar manutenção e modificar, você estará atrapalhando a produtividade da equipe que está quebrando a cabeça para conseguir alterar o seu código. É por isso que a facilidade de dar manutenção é tão **importante**, ela pode diminuir bastante os gastos de tempo da sua equipe, diminuindo assim, os gastos da empresa.



![Tradução: Dando manutenção no código de outras pessoas](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tasyegpn3obg1rs4h1xn.png)

Tradução: *"Dando manutenção no código de outras pessoas"*

## Modificadores de acesso


Agora vamos para uma parte um pouco mais avançada, que são os modificadores de acesso. Até agora, todos os métodos e propriedades que utilizamos tinham a palavra **public** atrás, mas existem outros 2 modificadores principais, o **private** e o **protected**.
O modificador **public**, como o próprio nome já diz, torna o seu método ou atributo público a todo mundo. Por exemplo, quando eu criasse um objeto a partir da classe Pessoa aí de cima, eu poderia acessar diretamente os valores Nome, Idade, Altura e CPF, simplesmente fazendo:
```c#
Pessoa objetoPessoa = new Pessoa(); //É assim que se gera um objeto no C#
objetoPessoa.CPF; //Aqui, eu estou acessando diretamente o CPF da pessoa
```
O problema é que nem sempre queremos deixar tudo tão exposto assim, faz bastante sentido deixar o CPF da pessoa oculto, não é mesmo? E pra isso nós temos os outros dois modificadores citados acima, o **private** e o **protected**. Ambos são mais restritos que o **public**, a diferença é que **private** está disponível SOMENTE dentro da classe, enquanto o **protected** é compartilhado entre a classe e os filhos dela (grava bem essa última frase porque você vai entender no próximo artigo, eu juro).

Ou seja, um atributo **public** estaria disponível pra qualquer um. Já um atributo (ou método) **private** seria de uso interno da classe, por exemplo, minha classe pode precisar do CPF do aluno pra fazer algum tipo de validação, mas nós não precisamos expor o CPF dessa pessoa pra ser usado fora da classe. E por fim, um atributo **protected** funciona de forma parecida com o **private**, a diferença é que ele é pro uso interno tanto da minha classe, quanto da "família" dela, ou seja, os filhos que herdam dela.

Explicando de outra forma, **public** seria um telefone público onde absolutamente qualquer um pode acessar, **protected** seria um telefone fixo, onde você e a sua família têm acesso, e o **private** seria o seu smartphone que SOMENTE você pode acessar.


![Resumo dos modificadores de acesso](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qtumzh86j4wwxh4j6zxy.jpg)

Essa imagem que eu fiz sintetiza bem a ideia do parágrafo acima. Desculpe pela montagem de má qualidade, mas o mais importante é a didática aqui.

Esse conceito de public, private e protected vai ser muito importante pra gente quando começarmos a aplicar encapsulamento e abstração (vou explicar no próximo artigo). Mas pra dar um gostinho pra você sobre o que eu estou falando, vamos criar uma classe que vai calcular quantos segundos você já viveu!

```c#
class TempoVivido
{
    private int converteAnosParaSegundos(int anos)
    {
        //função verbosa de propósito
        int dias = anos * 365; //converti de anos pra dias
        int horas = dias * 24; //conv de dias pra horas
        int minutos = horas * 60; //conv de horas pra min
        int segundos = minutos * 60; //conversão final
        return segundos;
    }

    private int descobreIdadeAproximada(int anoNascimento)
    {
        return DateTime.Now.Year - anoNascimento;
        //A linha acima, pega o ano atual,
        //e subtrai pelo seu ano de nascimento
    }

    //Agora a gente pode FINALMENTE usar nossa função principal
    public void QuantosSegundosVivi(int anoNascimento)
    {
        int idadeAtual = descobreIdadeAproximada(anoNascimento);
        int segundosVividos = converteAnosParaSegundos(idadeAtual);
        Console.WriteLine($"Você viveu, aprox, {segundosVividos} segundos");
    }
}

var vidinha = new TempoVivido();
vidinha.QuantosSegundosVivi(2002);
//Resultado: Você viveu, aprox, 630720000 segundos
//p.s.: Essa ultima parte do codigo não vai funcionar
```
Nessa classe aqui, a função principal, é a **QuantosSegundosVivi**, não faz muito sentido deixar as funções “auxiliares” expostas, por isso nós deixamos elas como **private** e só deixamos o usuário acessar a função principal.

Além desses modificadores de acesso, o C# tem outros modificadores, mas não precisa se preocupar com eles agora.

## Métodos especiais

Agora é hora de falar dos métodos especiais. São eles o getter, setter e construct. Calma que eu vou explicar cada um.

Primeiro vamos falar dos métodos getter e setter, que são **métodos acessores**, ou seja, eles nos ajudam a acessar o código. *"Mas como eles fazem isso exatamente?"* É agora que eu explica cada um deles

### Getter

Getter vem da palavra "Get", que significa obter, pegar, conseguir. Ou seja, esse método vai **pegar** coisas pra gente. *"Mas o que ele vai pegar?"* Ele vai pegar os valores das propriedades pra gente. Então, ao invés de acessar diretamente uma propriedade, como estávamos fazendo com o CPF no começo do artigo, podemos setar a propriedade como privada, e usar o getter pra retornar o valor da propriedade.

*"E por que isso é útil?"*. Imagine que você está num banco (sem caixas eletrônicos e que armazena o dinheiro num cofre) e você deseja verificar o saldo da sua conta bancária. Você verifica diretamente dentro do cofre o seu saldo, ou você pede para um funcionário do banco ir lá no cofre e verificar o saldo da sua conta? Não é muito mais seguro a segunda opção? Esse é um dos principais motivos de usar o getter (além de que nós podemos fazer validações na hora de retornar o valor pro usuário).

### Setter
Setter é mais ou menos como o oposto do Getter. Se o Getter retornava o valor pra gente, o Setter vai gravar o valor que nós passarmos pra dentro da propriedade da classe desejada. Ele tem os mesmo benefícios dos getters citados acima.

### Como esses métodos são sintaticamente?

Agora você pode estar em dúvida sobre como esses métodos são escritos. Vou escrever aqui um exemplo bobo em C# mostrando a forma que parece ser a mais usual entras as linguagens de programação. Disclaimer: no C# nós temos formas muitos mais elegantes de escrever o getter e setter, mas vou escrever do jeito mais comum. Caso você deseje ver essa forma elegante tem aqui na [documentação do C#](https://docs.microsoft.com/pt-br/dotnet/csharp/programming-guide/classes-and-structs/using-properties)

```c#
class Modificadores
{
    private int saldoBancario;
    public int NumeroConta;

    public getSaldo()
    {
        //codigo imaginario pra verificar se a conta existe

        return saldoBancario;
    }

    public setSaldo(int valor)
    {
        //codigo imaginario pra verificar fraudes
        saldoBancario = valor;
    }
}
```

### Construtor
Agora, finalmente vamos falar do **constructor**. Ele é um método especial que é executado toda vez que você instancia um novo objeto a partir da classe. Ou seja, sempre durante a **construção** de um objeto a partir de uma classe, esse método especial será chamado, e ele pode ser usado para várias coisas.

Um dos exemplos mais comuns é já gravar valores dentro das propriedades do seu objeto, assim o objeto não nasce vazio e você não precisa setar as propriedades uma a uma.

Abaixo vou postar um exemplo de como usar construstores no C#, na sua linguagem pode ser diferente, mas uma rápida olhada na documentação vai sanar suas dúvidas (aspirantes a bons programadores não têm medo de documentação hein).

```c#
class Pessoa
{
    //Pra criar um construtor no C#, basta criar
    //um método que publico com o mesmo nome da classe
    //O meu construtor está recebendo vários valores
    //e salvando eles nas minhas propriedades;
    public Pessoa(string nome, int idade, string cPF)
    {
        this.Nome = nome;
        this.Idade = idade;
        _CPF = cPF;
    }
    public string Nome;
    public int Idade;
    private string _CPF;
}

Pessoa eu = new Pessoa("Marcus", 19, "0.00.000...");
// isso vai criar uma pessoa com o nome Marcus, idade 19
// e o CPF todo mal formatado, rs
//p.s.: Essa ultima parte do codigo não vai funcionar
```
## Conclusão
Esse artigo ficou maior do que eu imaginava, haha. Nesse post vimos o básico do básico da orientação a objetos, no próximo artigo veremos os **pilares** da programação orientada a objetos, e qual é o benefício de seguir eles.
