---
layout:     post
title:      "O dia em que o OpenTelemetry quase me fez chorar"
subtitle:   ""
date:       2026-02-08 12:00:00
author:     "Lucas Jandrey"
header-img: "img/post-bg-android.jpg"
excerpt:    "Quebrando a aplicação no primeiro mês de empresa ou quase isso."
tags:
    - tecnologia
---

Tem um momento canônico na vida de todo desenvolvedor que é começar a trabalhar com aplicações de alta escala. É nessa hora que você percebe que seus algoritmos não performam como você gostaria. Surgem situações com as quais você precisa tomar muito cuidado.

Eu estava iniciando em uma empresa que tinha um volume de acessos consideravelmente alto. Era meu primeiro mês e você sabe como é, né? Aquela vontade enorme de mostrar serviço, nada me parava. A aplicação tinha diversos serviços separados e eu queria ler todos para entender o fluxo total dos dados. (descobri rápido que era impossível)

Chegou, então, uma tarefa em que eu precisava mexer no serviço de autenticação para resolver o meu problema. Mostrei a alteração para o meu Tech Lead, ele entendeu a lógica e eu segui em frente. Nesse ponto, vale citar que a aplicação era em Go, o último deploy de código tinha sido feito há 3 meses e eu tinha pouca experiência com a linguagem em produção, até fazia um script ou outro, mas nada em um servidor web de fato. 

Mas eu estava confiante.

![Confiança](https://media0.giphy.com/media/v1.Y2lkPTc5MGI3NjExd3AxeDQ3YmRhZ2d0a3JrYWlvbXYybGs0eDBvdmgxdHFsbGxkeW51MSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3ohhwF34cGDoFFhRfy/giphy.gif)

## O dia do deploy

Após passar pelos reviews do time e testar nos ambientes de dev e staging, parecia não ter como dar errado. Às 9 horas começou o deploy. Tudo certo. Eu já estava até pensando em atualizar minha headline do LinkedIn com a linguagem Go (hehe). Eis que, por volta das 10:00, começam alguns comentários no canal da empresa:

> "Pessoal, não está dando para fazer login."

Aí é aquela hora em que parece que o coração muda até o compasso. Era meu primeiro mês na empresa e comecei a achar que eu tinha quebrado tudo. O pessoal, de primeira, busca quem fez as últimas alterações para entender o contexto, e lá estava o meu nome. De repente, a equipe de tech inteira estava falando de mim e eu entrei em total desespero.

Assim como o C, mas de forma mais segura, o Go usa bastante ponteiros. Foi aí que pensei que estava a minha falha: imaginei que em algum momento estava copiando dados de forma desnecessária e isso estaria derrubando a aplicação por estouro de memória (OOM). Acho que foi nesse dia que aprendi, da forma mais rápida possível, como debugar vazamentos de memória em Go. Além do profiling, criei scripts na minha máquina para simular requisições em massa e ver se o consumo subia.

No meio desse desespero, eu acompanhava os logs e os gráficos. A aplicação não mostrava uma curva de acúmulo de memória; a RAM permanecia estável até a queda. O serviço rodava por um tempo, morria, as máquinas faziam o recycle e o ciclo se repetia. Isso me deixou ainda mais confuso, porque memória era a única coisa que eu conseguia imaginar, já que eu não tinha mexido em nenhuma query complexa ou algo do tipo.

Até que o pessoal fez o revert da minha PR próximo das 11 horas. Mas, como eu disse, a aplicação não caía na hora, demorava um tempo. Nesse momento, algumas pessoas muito firmeza (não esquecerei de vocês) me falavam:

> "Calma, isso acontece com todo mundo. E esse serviço aqui é horrível mesmo."

Por mais que estivessem tentando me acalmar, eu ainda estava em choque. Só que, mesmo após o revert, a aplicação caiu novamente depois de um tempo. Foi aí que o time de DevOps foi muito ligeiro e identificou que uma atualização automática do OpenTelemetry estava causando o crash.

Para a equipe tech, o cenário ainda era ruim e precisava de correção. Mas para mim, preciso confessar: deu um alívio gigantesco saber que não tinha sido eu. Logo no primeiro mês!

Ficamos ali até as 12:30 até normalizar tudo e finalmente fomos almoçar. Esse dia ficou marcado na minha história como o dia em que fiquei mais nervoso na carreira.

Olhando para trás, o que mais me marca nessa história é o pessoal que estava ao meu lado me ajudando, em especial meu Tech Lead, o PM e a equipe de DevOps. Eles viram o meu desespero e me acolheram da forma mais humana possível. Foi muito bom trabalhar com essa galera; no pior dos cenários, ninguém soltou a mão de ninguém.

![Parceiros](https://media4.giphy.com/media/v1.Y2lkPTc5MGI3NjExYXY1cXdkdnZ1bWcwMGluMTZudDIweHI4YnFtdGpteGF0NDc5enFjZCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/l3q2zbskZp2j8wniE/giphy.gif)

## Resumo do caos

O problema não estava em si no OpenTelemetry, obviamente. Foi a combinação da atualização dele com outras características da imagem que causaram o problema. No fim das contas, não cheguei a saber em detalhes o que houve.

Não coloquei Golang na minha headline do LinkedIn (hehe), mas usei o susto como combustível para estudar mais sobre a linguagem e ter mais segurança no meu código.

~ Observação: Não havia IA da forma que temos hoje na época para me salvar. ~