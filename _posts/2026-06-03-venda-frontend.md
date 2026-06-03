---
layout:     post
title:      "Venda o front-end enquanto você arruma o back-end"
subtitle:   ""
date:       2026-06-03 12:00:00
author:     "Lucas Jandrey"
header-img: "img/post-bg-android.jpg"
excerpt:    "Como blindar uma estrutura dinâmica no back-end com uma interface estática trouxe um novo norte para o produto (e melhorou a sanidade do time)."
tags:
    - tecnologia
---

Em uma das minhas experiências recentes, comecei a trabalhar em um sistema legado que era extremamente dinâmico. Ele tinha a proposta de criar workflows e quadros Kanban, mas quando eu falo dinâmico, estou dizendo um nível no qual o usuário conseguia criar novas tabelas no banco de dados através de um formulário.  

Sim, isso mesmo. Isso fere muitos conceitos de modelagem e, pior, fere a saúde mental de qualquer mantenedor do projeto. Para contextualizar: de manhã o sistema tinha 600 tabelas, de tarde tinha 610. Era definitivamente um inferno em código.

![Imagem de cachorro calmo no meio do incêncio](https://media4.giphy.com/media/v1.Y2lkPTc5MGI3NjExNzl4ZXJic2h3bDRlYTN1cWMwcWU4NzBzNTJkcDdscWxlbzNkbjdxYiZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/VOq7iem25Z94Y/giphy.gif)

Houve algumas tentativas anteriores de conter esse dinamismo com tabelas polimórficas de metadados, mas com um banco de dados fraco e de baixa performance. Era uma opção melhor do que alterar o banco em tempo de execução, mas ainda assim causava problemas sérios para a consulta dos dados. Isso ficou por alguns meses sendo o tópico principal dos nossos almoços; eu e meu colega só tínhamos a esperança de, um dia, fazer um CRUD igual a pessoas normais (hehe).

## A esperança disfarçada de feature

Como o sistema era baseado em Workflows, o time de produto veio com uma nova demanda: um módulo de pedidos de compra. Por sinal, eles já tinham implementado isso de forma genérica nos quadros Kanban, mas dessa vez a proposta exigia uma interface muito mais simples e definida. Começamos a ver uma ponta de esperança de as coisas mudarem ali.

Mas, como tínhamos clientes ativos usando o fluxo na versão Kanban, a decisão que tomei foi: vou usar ainda a estrutura dinâmica no back-end, mas blindá-la com uma interface estática no front-end. 

Dessa forma, o cliente não teria que se preocupar em configurar seu quadro Kanban com cards cheios de informações dinâmicas; eu faria isso por ele por de baixo dos panos e exibiria só a interface limpa que o pessoal de produto criou. Eu queria largar de fato aquela estrutura, mas o sistema já tinha muitas features paralelas atreladas a ela, como geração de documentos e importação de arquivos. 

## O avanço de produto vs. O desespero técnico

Chegou a hora da implementação e tudo ocorreu bem. A tela estava funcionando, o novo fluxo era muito simples de usar e até o pessoal de vendas estava conseguindo vender melhor o produto por conta da simplicidade da interface. Mal sabiam eles como estavam ficando os dados por baixo dos panos: o local de armazenamento continuava o mesmo de antes, ruim e com baixa performance.

Após dois meses em produção e com o número de registros começando a crescer, a preocupação com o armazenamento dos dados só aumentava. A cada novo cliente integrado, batia aquele desespero com pensamentos do tipo: "Uma hora esses dados precisam sair daqui, tá inseguro demais isso".

Foi quando chegou uma nova demanda para mim: colocar permissões de acesso em nível de campo. Nesse momento, vi que não teria mais como manter da forma que estava. Até daria, mas iríamos afundar junto com aquela tela nova.

## O retorno à civilização

Conversei com o PM sobre o tamanho desse débito técnico. Fiz a implementação de tabelas isoladas para aquele tipo de quadro e, então, rodamos a migração da estrutura dinâmica para a nova estrutura de dados.

Finalmente houve paz na busca dos registros e na segurança das relações. Agora tudo estava em seu devido lugar. Algumas das features paralelas precisaram de adaptações, mas o custo operacional foi baixo.

Essa mudança foi significativa para a parte de produto, que largou um pouco do extremo dinâmico para tentar ser mais assertiva em modelar os fluxos dos clientes e para a parte técnica também, já que agora estávamos fazendo CRUD novamente.

![Gato meditando](https://media2.giphy.com/media/v1.Y2lkPTc5MGI3NjExMG9rb284bHc2c3UyOWlzaG5kYzdubThjdHEydGt1Mm15NXd5b3UxdiZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/105D9aefNvprfG/giphy.gif)

Todos que passaram por esse projeto e se depararam com as tabelas `cliente_xyz_batata_2` certamente vão guardar na memória o preço real de manter um sistema dinâmico sem governança.

Por fim, quero deixar uma frase cirúrgica de Edsger W. Dijkstra (o homem do algoritmo):

> "A simplicidade é uma grande virtude, mas exige muito esforço para ser alcançada e conhecimento para ser valorizada. E, para piorar, a complexidade costuma vender mais."
