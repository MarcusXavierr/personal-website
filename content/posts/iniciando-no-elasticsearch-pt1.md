---
title: "Iniciando no Elasticsearch: Conceitos básicos"
date: 2024-04-17T20:38:33-03:00
draft: false
series: ['Iniciando no Elasticsearch']
tags: ['Database']
---

## Introdução
Segundo o site oficial do Elasticsearch, ele é uma engine RESTFul distribuída de busca. Esse monte de palavras basicamente querem dizer que ele é um banco de dados que funciona de forma distribuída por natureza (pode usar várias máquinas em paralelo de forma super fácil) e que expõe uma API RESTFul para que o usuário interaja com o banco, seja escrevendo ou buscando dados, entre outras coisas.

O Elasticsearch é uma ferramenta incrível quando o assunto é trabalhar com uma quantidade massiva de dados e fazer buscas em textos longos (diga adeus aos `select like %texto%` lentos). Ele é a base do sistema de buscas de milhares de empresas, e muito provavelmente você tem contato diariamente com algum software que usa Elasticsearch por baixo dos panos.

## Antes de mais nada, um pouco de história
Sempre que estou estudando uma tecnologia nova, gosto de entender **porque essa ferramenta foi criada** e em qual contexto ela nasceu. O Elasticsearch foi criado em 2010, mas podemos dizer que sua história começa em 2004 quando Shay Banon iniciou um projeto chamado [Compass](https://web.archive.org/web/20080207094229/http://www.compass-project.org/), com o intuito de ser usado no sistema de buscas em um [app de receitas para sua esposa](https://devm.io/databases/elasticsearch-founder-interview-112677). O projeto nasceu de forma despretensiosa e só foi se tornar open source tempos depois.

Um fato curioso: nesse mesmo ano (2004) nascia o projeto que atualmente é um dos principais concorrentes do elastic, o [Apache Solr](https://solr.apache.org/).

Todos esses projetos nasceram mais ou menos com o mesmo objetivo em mente: realizar buscas de texto de forma **muito** eficiente usando por baixo dos panos o [Apache Lucene](https://lucene.apache.org/), projeto criado em 1999 usando Java. O Apache Lucene é a base de quase todos os softwares de busca atualmente, sendo por exemplo usado pelo twitter [para buscar milhões de tweets diariamente](https://lucidworks.com/post/how-twitter-uses-apache-lucene-for-real-time-search/).

Mas voltando ao assunto, o Shay Banon criou o Compass em 2004, e quando estava criando a versão 3.0 do projeto em 2010, reescreveu várias partes do projeto para que ele fosse distribuído por natureza e altamente escalável. E daí nasce o Elasticsearch.

O Elasticsearch foi criado então, **para ser uma forma fácil de usar todo o poder do Apache Lucene**, enquanto fornece uma forma extremamente simples de escalar seu projeto horizontalmente, **usando e abusando de computação distribuída**.

## Termos comuns do Elasticsearch
Ao iniciar os estudos sobre Elasticsearch é normal se deparar com vários termos novos, e achar a ferramenta complicada por causa disso. A ideia aqui é explicar os principais termos deste banco de dados, e mostrar quando esse termo tem algum paralelo no mundo dos bancos relacionais.

#### Documento
É basicamente como uma row/linha no banco de dados relacional. A diferença está no fato de documentos serem salvos como objetos JSON.
Quando você fizer buscas na API de `_search` do elastic, vai encontrar documentos com essa estrutura.

Os dados salvos pelo usuário estão dentro de `_source`, os outros campos são metadados adicionados pelo banco de dados.

```json
{
	"_index": "produtos",
	"_type": "_doc",
	"_id": "1234",
	"_score": 2.0,
	"_source": {
        "nome": "Notebook",
        "descricao": "bla bla bla", // Essa descrição pode ser facilmente buscável no elastic, e eu eu sei que JSON não aceita comentário
		"preco": "4000"
	}
}
```
#### Índice
É basicamente uma coleção de documentos do mesmo tipo. Ou seja, é como uma tabela de um banco de dados relacional.

#### Node
Um `node` é uma instância do Elasticsearch que salva dados. Para salvar terabytes de dados no elastic, você pode ter vários nodes, cada um guardando um pedaço dos dados.
Assim você pode escalar em várias máquinas diferentes, mesmo que cada uma tenha só alguns gigabytes de armazenamento, cada uma rodando os `nodes` que pode.
Teoricamente falando, é como se cada `node` fosse um arquivo.

#### Cluster
Se `nodes` são arquivos, `clusters` são pastas. Eles basicamente agrupam os `nodes` e te permitem buscar em todos esses nodes em busca dos dados que você quer.
Geralmente um `cluster` serve a um propósito. Então é possível ter um cluster para ter a busca de produtos no seu e-commerce, e outro `cluster` guardando os seus logs, por exemplo.

#### Outros termos do Elastic
- `Field` é como se fosse uma coluna da tabela do banco relacional. No Json do documento, "nome" é um field e "preco" é outro.
- `Mapping` é como o schema de um banco de dados relacional. Por exemplo, é o mapping que guarda o tipo de cada Field
- `Shard` é um pedaço do seu índice. O elastic por padrão quebra seu índice em vários pedaços e distribui entre os nodes do cluster, daí vem a computação distribuída que mencionei anteriormente.

## Arquitetura "básica" da API Restful do elastic
Enquanto outros bancos de dados usam conexão TCP para se comunicar com o cliente, e exigem um driver específico para cada linguagem, o Elastic usa _simplesmente_ uma API RESTful HTTP. Isso significa que qualquer linguagem com uma lib de requisições HTTP consegue se comunicar facilmente com o elastic (embora existam clients específicos para facilitar esse trabalho também).

As estruturas básicas de um endpoint do elastic são as seguintes:
```
{url_cluster}/<seu_indice>/<_operação>
// ou
{url_cluster}/<_ação_no_cluster_inteiro>/<_operação>
```
Geralmente, as operações começam com underscore (`_`), tipo `_search`, `_update`, `_delete`, `_bulk`, etc. E na maioria dos casos, se você colocar a url do cluster e em seguida um nome sem underscore, como no primeiro exemplo, ele vai inferir que é um índice seu.

Como a API é RESTful, cada operação tem um verbo HTTP associado.
Para buscar dados, seja buscar documentos ou dados sobre o cluster, você usa o verbo `GET`, para criar um novo documento você usa o verbo `POST` e assim por diante.

Alguns exemplos de requisições para essa API são:
```bash
# Buscar dados de saúde do cluster
curl -X GET "localhost:9200/_cluster/health"

# Atualizar o mapping de um índice
curl -X PUT "localhost:9200/meu_indice/_mapping" -H 'Content-Type: application/json' -d'
{
  "properties": {
    "nome_produto": {
      "type": "text"
    }
  }
}
```
## Como o Elastic consegue buscar texto de forma tão rápida?
Como dito anteriormente, o elastic é baseado no Apache Lucene, e o Lucene usa uma técnica chamada **[inverted index](https://j.blaszyk.me/tech-blog/exploring-apache-lucene-index/#inverted-index)** para buscar texto de forma eficiente. Um índice invertido é basicamente uma lista de palavras que apontam para os documentos que contém essa palavra. Por exemplo, se você tem um documento com o texto "O rato roeu a roupa do rei de Roma", e outro índice como "O gato comeu a roupa do rato", o índice invertido seria algo como:
```json
{
    "rato": [1, 2],
    "roeu": [1],
    "roupa": [1, 2],
    "rei": [1],
    "roma": [1],
    "gato": [2],
    "comeu": [2]
}
```

E quando você busca por "roupa", o elastic vai buscar no índice invertido a palavra "roupa" e vai te retornar os documentos 1 e 2.

Então, buscando a frase "O rato comeu queijo", ele vai perceber que o documento 2 tem duas palavras da busca ("rato", "comeu"), e vai te retornar o documento 2 com um score maior que o documento 1, pois o documento 1 só tem uma palavra da busca ("rato").

Por isso o nome é índice **invertido**, pois ele inverte a lógica de busca. Em vez de buscar palavras em documentos, ele busca documentos em palavras. Isso é simples, mas extremamente eficaz.

Claro, existem outras otimizações, como usar um [Vector Space Model para calcular similiridade entre documentos e a busca](https://www.elastic.co/pt/blog/found-similarity-in-elasticsearch), mas a ideia básica é essa.
## Conclusão
Bom, esse foi um resumo básico do que é o Elasticsearch, porque ele foi criado e como ele consegue buscar texto de forma tão eficiente, além de ser um overview de como funciona a API RESTful.
Nos próximos artigos aprenderemos a fazer queries e usaremos o docker para subir um cluster do elastic e uma instância do Kibana, uma ferramenta muito útil para visualizar e interagir com clusters do elastic. Até lá!
