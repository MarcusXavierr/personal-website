---
title: "Clean Code: Nomes Significativos"
summary: "Bons nomes são demasiadamente importantes no desenvolvimento de software e este artigo visa cobrir as melhores dicas do livro Clean Code sobre a escolha de bons nomes"
date: 2022-08-06T10:10:58-03:00
draft: true
---
## Introdução
Clean Code é um livro extremamente famoso entre programadores. E essa série de posts visa cobrir os principais capítulos desse livro. O artigo de hoje é sobre o segundo capítulo: nomes significativos.

A ideia é não só mostrar as dicas apresentadas no livro clean code, mas também apresentar vários exemplos de código para que a gente entenda bem como aplicar essas dicas de código limpo em vários cenários.

É sempre bom deixar claro que o livro Clean Code não é nenhum magnum opus da computação e nem tem pretensão de ser. O próprio autor do livro deixa bem claro que não se deve levar a ferro e fogo 100% do que é dito no livro. Então não vamos desapontar o uncle bob, ok 😉?

### Use nomes que revelam a intenção do código
Um nome excelente deveria responder o por quê de tal determinado trecho de código existir.
* Por que a classe "X" existe?
* Qual é a finalidade da função "Y"?
* O teste "A" testa o que exatamente?
* Eu consigo imaginar com um pouco de precisão a aparência do componente "Z" só com base no nome dele?

Segundo a minha visão, excelentes nomes são capazes de responder essas pergunta.

A vantagem em ter excelentes nomes é que eles te dão um certo contexto geral do que está acontecendo no código e isso pode poupar bastante tempo de leitura.

Bom, na minha experiencia seguir essa regra a risca é bem difícil. Ter uma certa padronização no seu código ajuda bastante, por exemplo, usar o controller somente para ser o "meio de campo" entre o endpoint e a regra de negócio.

Logo, se eu criar uma class WithdrawRequestController, fica claro que essa classe irá somente chamar outras classes que vão cuidar da regra de negócio. Esse nome também deixa claro que a classe cuida de requisições de saque. Então, dado o contexto da nossa codebase, o nome WithdrawRequestController nos diz que a classe **existe para cuidar do meio de campo entre o endpoint e a nossa regra de negócio, que irá processar requisições de saque dos nossos usuários**.
