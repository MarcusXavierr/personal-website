---
title: "Clean Code: Fazendo bom uso das funções | Parte 2"
date: 2022-09-20T22:44:22-03:00
draft: false
---

## Introdução
Passei alguns minutos pensando em como escrever uma introdução pra esse artigo, que é a continuação de um assunto que já foi introduzido anteriormente, mas acabei percebendo que isso não seria necessário, pois essa segunda introdução serviria somente para que o leitor não esbarrasse logo de cara com conteúdo do artigo de forma abrupta, e esse parágrafo de lero lero serviu perfeitamente para isso 😁.

## Um nível de abstração por função
Para garantir que a nossa função faz somente **uma** coisa, nós precisamos ter certeza que os pedaços de código dentro da função estão no mesmo nível de abstração. O que eu quero dizer com isso é que se uma parte da sua função está cuidando de coisas bem abstratas, não faz muito sentido misturar isso com detalhes de implementação logo abaixo. Vou mostrar um trecho de código para clarificar isso.

Esse método abaixo faz parte de uma classe que tem como responsabilidade buscar dados do usuário. Essa função em específico precisa pegar a primeira venda do usuário.

```typescript

public getFirstOrder(userId: number)
{
    cachedOrder = client.get(
                   `orders.userId`, (err, reply) => {
                      if (err) throw err;
                      return reply
                  })

    if (!cachedOrder) {
        return cachedOrder;
    }

    return this.getFirstOrderInDB(userId);
}
```
No código acima eu tento pegar o pedido no cache, se não tiver no cache eu pego o valor do banco de dados. No começo eu fiz algo como um nível muito baixo de abstração que no caso foi chamar o `client.get` e logo no fim da função eu chamei a função `getFirstOrderInDB` que é algo muito mais abstraído. Um código assim não fica muito bom, na minha opinião, porque estamos fazendo algo com pouca abstração num cenário onde poderia ter muito mais abstração.
Olhe agora o exemplo abaixo.

```typescript
public getFirstOrder(userId: number)
{
    cachedOrder = this.getOrderFromCache(userId);

    if (!cachedOrder) {
        return cachedOrder;
    }

    return this.getFirstOrderInDB(userId);
}
```
Não mudou praticamente nada, só a busca no cache que foi abstraída. Essa simples mudança deixou o código mais limpo e simples.

## Escreva código para ser lido de cima pra baixo
Eu gosto bastante dessa dica e tento sempre seguir ela. Me surpreende isso ser uma coisa tão pouco debatida. A ideia é você escrever o seu código com muita abstração no começo do arquivo, e a parte do código menos abstraída (ou seja, as funções que foram chamadas pelas funções no topo do arquivo) devem ficar mais abaixo no arquivo.

Por exemplo, no trecho de código anterior a função `getFirstOrder` é mais abstraída, logo deveria ficar mais acima. Já as funções `getOrderFromCache` e`getFirstOrderInDB` devem ficar no final do arquivo por que estão em um nível mais baixo de abstração (talvez essas duas últimas funções podem até mesmo ser métodos privados).

## Use nomes descritivos
O seus nomes precisam descrever o que a função faz, por isso é bom ter funções pequenas e que fazem somente uma coisa. Bons nomes são tão importantes que esse foi assunto do primeiro artigo dessa série de posts, você pode conferir lá ótimas dicas para escolher bons nomes.

## Cuidado ao passar booleanos como parâmetro da sua função
Nessa parte o autor diz que passar booleanos como parâmetro para a sua função pode ser um sinal de que a sua função faz mais de uma coisa, pois ela pode fazer uma coisa se o parâmetro for `true` e outra coisa caso seja `false`. Claro, nem sempre é possível evitar isso, mas caso você tenha uma situação com a citada acima, pode ser interessante fazer uma função para caso determinado valor seja `true` e outra para caso seja `false`

Você deve evitar códigos como o abaixo
```c#
public class CompanyRepository
{
    public void CreateOrUpdate(Company company, bool create)
    {
        if (create) {
            ...
        }
        else {
            ...
        }
    }
}
```
E preferir fazer códigos como esse aqui
```c#
public class CompanyRepository
{
    public void Create(Company company) { ... }
    public void Update(Company company) { ... }
}
```

## Error Handling é uma coisa
Lembra que no mundo ideal cada função faria somente uma coisa? então, error Handling é uma coisa. Segundo o autor, se a sua função tem um bloco de try catch ou algo do tipo, não deveria haver nada além disso. Ao invés de abrir o bloco try e colocar um monte de código nele, você deveria pegar todo o código dentro do try e colocá-lo em uma nova função.

## Conclusão
Ufa, finalmente terminamos de cobrir o capítulo sobre funções. Esse capítulo nem é tão grande, mas como ele é cheio de pérolas interessantes sobre como escrever funções "melhores", eu não quis deixar ele muito resumido.
É sempre bom lembrar que nem mesmo o autor do livro escreve funções boas logo de cara (ele fala sobre isso no final do capítulo), a ideia é primeiramente fazer algo que funciona, depois se preocupar em fazer o seu código seguir as boas práticas citadas acima.

E pra finalizar esse artigo, deixo um insight do uncle bob que achei bem interessante.

*"**Programadores experientes veem os sistemas como histórias a serem contadas** em vez de programas a serem escritos.[...]Mas jamais se esqueça de que **seu objetivo verdadeiro é contar a história do sistema**, e que as funções que você escrever precisam estar em perfeita sincronia e formar uma linguagem clara e precisa para lhe ajudar nessa narração"*
