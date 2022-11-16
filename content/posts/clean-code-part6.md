---
title: "Clean Code: Classes"
date: 2022-11-15T15:33:55-03:00
draft: false
---

## TL;DR
Classes deveriam ser pequenas, deveriam fazer somente uma coisa e deveriam ter somente um motivo para mudar

## Classes deveriam ser pequenas
Existe um milhão de motivos para odiar classes grandes: o código fica com aspecto de bagunçado, existem literalmente dezenas de métodos que podem fuçar em todos os atributos da classe, o que aumenta a chance de algum erro passar despercebido, e também é uma porcaria se "mover" na classe, você pula pra um método privado da classe, e vai pra 1000 linhas abaixo de onde você estava.

Já as "classes pequenas", por outro lado, são mais simples de manter, pois elas não fazem muitas coisas então a preocupação de causar um efeito colateral ao mudando algo é menor. Em geral, quando você bate o olho no nome de um "Classe pequena" já é possível ter uma ideia do que ela faz (se o nome for bom, obviamente).

"Classes pequenas", também são mais fáceis de testar, pois elas costumam a ser menos acopladas com coisas externas, isso significa menos mocks no teste, o que é sempre bom 😀.

## Mas o que raios é uma "Classe Pequena"?
Acho que isso foi o suficiente pra mostrar que classes gigantes são uma merda geralmente. E agora vem a próxima pergunta: como eu defino o que é uma classe pequena?
Muitas pessoas vão lembrar do capitulo sobre funções e pensar que também é possível definir se a classe é pequena ou não, se baseando na sua quantidade de linhas (seu tamanho), mas isso não poderia estar mais incorreto.

Usar a quantidade de linhas para classificar uma classe seria muito vago, e por isso o uncle bob nos recomenda pensar no bom e velho S do SOLID (Principio da Responsabilidade Única). A ideia aqui é, ao invés de contar a quantidade de linhas para ver se a classe é pequena, nós iremos contar a quantidade de "responsabilidades" que a classe possui. Se tiver mais do que uma responsabilidade, geralmente é hora de dividir essa classe em classes menores.

## Ok, e como eu sei se a minha classe tem mais de uma responsabilidade?
Bom, o uncle bob também apresenta algumas formas bem interessantes de verificar isso.

A primeira forma é, na hora de criar o nome da classe, tentar dar um nome descritivo para ela. Se você tentar e não conseguir criar um nome descritivo, e ao mesmo conciso, **pode** ser que a classe tenha mais de uma responsabilidade.

Outra forma de ver isso é tentar escrever uma pequena descrição de uma 25 palavras explicando por cima o que a classe faz. Se você usar palavras como _"mas"_, _"se"_, _"e"_, _"ou"_, também **pode** ser que a sua classe tenha mais de uma responsabilidade. Aqui vai um exemplo do livro:

>_"A `SuperDashBoard` provê acesso ao último componente utilizado **e** também nos permite acompanhas [metadados como] o número da versão e build."_

E por fim você pode pensar "motivos para mudar", ao invés de "responsabilidades". Pense, _"em quais situações, ou por quais motivos, eu teria de mudar o código dessa classe?"_. Se existir mais de um motivo para mudar, está bem claro que a sua classe tem mais de uma "responsabilidade" ou "razão para existir".

## O que você prefere?
Eu já vi pessoas reclamando de que dividir seu sistema classes pequenas acaba gerando PRs maiores do que simplesmente fazer do modo "quick and dirty", tudo dentro de uma classe. Outras pessoas também podem argumentar que ter uma sistema repleto de arquivos de classes é mais complexo de gerenciar. Mas o autor do livro argumenta que a complexidade do sistema não tende a mudar muito por causa disso, e deixa a seguinte reflexão.

>_"Você prefere as suas ferramentas organizadas em armários repletos de pequenas gavetas, cada uma contendo componentes bem definidos e bem nomeados? Ou prefere umas poucas gavetas onde você joga tudo lá?"_
