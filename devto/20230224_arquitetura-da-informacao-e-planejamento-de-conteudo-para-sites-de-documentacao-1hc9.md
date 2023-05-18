---
title: Arquitetura da Informação e Planejamento de Conteúdo para Sites de Documentação
published: true
description: Nesse artigo vamos aprender um pouco sobre arquitetura de informação no contexto de sites de documentação, e também como planejar o seu conteúdo.
series: documentacao-para-iniciantes
tags: ptbr, braziliandevs, ux, docs 
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fca8nbacizt0emk9ukir.png
# Use a ratio of 100:42 for best results.
# published_at: 2023-01-17 17:51 +0000
---


Num artigo anterior desta série, vimos como configurar um site dedicado de documentação com o gerador de sites estáticos Hugo, e como hospedá-lo gratuitamente na Netlify. Agora você deve estar se perguntando qual é a melhor maneira de organizar a sua documentação de uma forma que os usuários possam aproveitá-la da melhor maneira possível e encontrar o conteúdo que precisam com facilidade.

Apesar de não haver fórmula mágica para escrever uma boa documentação, algumas coisas serão decisivas para criar uma boa experiência para o usuário final. Organização, consistência na estrutura de artigos, e uma boa navegação são recursos importantes em qualquer site de documentação.

Nesse post vamos aprender um pouco sobre arquitetura da informação no contexto de sites de documentação, e também vamos ver algumas dicas para planejar e manter o seu conteúdo organizado.

## 1. Introdução à Arquitetura da Informação
O termo "arquitetura da informação" (information architecture ou IA) é usado em diversas áreas de estudo para se referir à prática de organizar e categorizar o seu conteúdo de forma a otimizar a experiência do usuário final. Neste contexto, a arquitetura da informação tem como objetivo diminuir a carga cognitiva necessária para que usuários encontrem o conteúdo que precisam no seu site.

Os estudos de arquitetura de informação levam em consideração três elementos que compõem a "ecologia da informação" de qualquer corpo de conteúdo: **usuário**, **conteúdo**, e **contexto**. Estes elementos precisam estar alinhados para criar uma boa experiência para os usuários finais, seja numa biblioteca física ou em um website de documentação.

Quando falamos especificamente de websites, os autores Louis Rosenfeld e Peter Morville identificaram 4 componentes ou sistemas para uma arquitetura de informação bem sucedida no livro [Information Architecture for the World Wide Web](https://books.google.nl/books/about/Information_Architecture_for_the_World_W.html?id=hLdcLklZOFAC&redir_esc=y):

- **Sistemas de Organização (Organization Systems)**: se refere aos grupos ou categorias nas quais o conteúdo será organizado. Sites de documentação geralmente usam um sistema hierárquico com categorias mais abrangentes no topo, afunilando para categorias mais específicas à medida que você navega dentro da árvore taxonômica.
- **Sistemas de Etiquetamento (Labeling Systems)**: "etiquetas" ou labels ajudam usuários a identificarem mais rapidamente seções e categorias de conteúdo.
- **Sistemas de Navegação (Navigation Systems)**: ajudam o usuário a navegar no conteúdo, e pode ser de vários tipos. Um site pode combinar vários estilos de navegação para ajudar a dar visibilidade a certos tipos de conteúdo que se encontram mais escondidos dentro da árvore taxonômica. 
- **Sistemas de Busca (Search Systems)**: essenciais para permitirem ao usuário buscar o conteúdo que precisam dentro do seu site. 

A maioria dos CMSs e geradores de site estáticos já possuem esses componentes implementados como recursos integrados no sistema; cabe a você se preocupar em como utilizar esses recursos da melhor maneira possível para otimizar a experiência dos usuários finais acessando o seu site.

## 2. Estrutura de Conteúdo para Sites Estáticos
A maioria dos geradores de sites estáticos que são usados para sites de documentação (como por exemplo Hugo ou mkdocs) dependem diretamente da hierarquia de diretórios para definir como o conteúdo é agrupado e exibido.  

Se você já estudou ou trabalhou com SEO (search engine optimization) você deve saber que mudar a estrutura de uma URL depois que o site já está indexado pelos mecanismos de busca pode causar um impacto negativo na experiência do usuário, a não ser que você crie redirecionamentos de URL para manter os links ativos. Por isso é importante planejar como o seu conteúdo ficará organizado, levando em consideração áreas que podem ser expandidas depois, e ao mesmo tempo evitando uma estrutura muito aninhada que torne a navegação difícil.

Definir categorias e tipos de conteúdo (content types) pode ser um bom ponto de partida para decidir como você quer organizar os diretórios do topo da hierarquia. Alguns exemplos comuns, do mais abrangente para o mais específico:

- Por região ou idioma, ex: **eng_US**, **pt_BR**...
- Por versão do projeto, ex: **latest**, **2.2.1**...
- Por categoria, ex: **open-source**, **products**...
- Por tópico, ex: **getting-started**, **advanced-usage**...
- Por tipo de conteúdo ou content type, ex: **tutorials**, **videos**...

A decisão de como organizar seus diretórios no topo da hierarquia deve depender do tamanho do seu projeto, e também da sua própria disponibilidade para manutenção e atualizações. Se você mal tem tempo para escrever a documentação básica do projeto, você não deveria se preocupar em fazer uma documentação em múltiplos idiomas ou múltiplas versões. Isso é importante para projetos maiores, já que certamente você terá pessoas usando versões legadas e seria importante dar suporte a estes usuários também.
Para projetos pequenos, organizar os diretórios no topo da hierarquia por tópico é geralmente uma boa estratégia, já que permite organizar documentos similares em pequenas coleções sem complicar muito a estrutura de diretórios da documentação. Caso o seu site tenha conteúdos muito diferentes em termos de propósito ou audiência (como por exemplo tutoriais de projetos open source e documentação de produtos), você pode usar categorias para ajudar a clarificar a diferença entre esses assuntos.

Eu particularmente não recomendaria usar a taxonomia por tipo de conteúdo (vídeo, tutoriais, etc) no topo da sua hierarquia, a não ser que você não tenha variedade de tópicos na sua documentação. Essa taxonomia funciona bem para sub-seções, dentro de uma categoria ou tópico.

## 3. Tipos de Conteúdo
Um tipo de conteúdo (content type ou media type) é uma representação de informação que segue um formato similar, algo que os usuários podem reconhecer no seu conteúdo. Usar tipos variados de conteúdo é uma boa estratégia para prover variedade, permitindo que você possa reutilizar um mesmo conteúdo em formatos diferentes, para uma audiência diferente. Por exemplo, você pode ter um tutorial sobre um tópico, mostrando cada passo em detalhes. Uma versão _quickstart_ do mesmo tutorial poderia ir mais direto ao ponto, cortando explicações e mostrando apenas os comandos que você precisa executar para atingir o mesmo objetivo. Transformar conteúdo em texto para um formato em vídeo também é uma boa estratégia para maximizar o seu output.

Definir tipos de conteúdo (e templates para tal) ajuda bastante na produção de conteúdo porque você não vai precisar pensar muito sobre a estrutura do artigo toda vez que for escrever um novo conteúdo, podendo reutilizar o mesmo formato de artigos já publicados e que são do mesmo "tipo".

## 4. Planejamento de Conteúdo
Algumas das decisões acerca da estrutura de conteúdo e a taxonomia do seu conteúdo só farão sentido a partir do momento que o seu site tenha uma quantidade mínima de artigos que lhe permita navegar através das páginas e seções. Você não precisa escrever todo esse conteúdo antes de publicar o seu site, mas você precisa planejar o que você precisa ter lá como prioridade, o que não pode faltar. 

Pense em uma documentação suficiente para que os usuários sejam bem sucedidos ao testar o seu projeto. Sempre haverá casos específicos e cenários que você não tem como prever ou reproduzir por conta própria, então tenha isso em mente ao definir suas prioridades. Documentação nunca está finalizada, sempre haverá mais conteúdo para adicionar, atualizar, ou melhorar.

Alguns tópicos ou seções que eu particularmente considero essenciais:

- **Getting Started**: Uma seção mostrando em detalhes como instalar o projeto e testar a sua execução.
- **Usage**: Uma seção explorando uma visão mais detalhada de como usar o projeto, incluindo principais comandos e argumentos.
- **Advanced**: Uma seção com conselhos de utilização mais avançados, e casos de uso mais específicos.

Também considere criar uma seção de "Troubleshooting" com dicas para debugar ou solucionar problemas comuns, e um tutorial mais detalhado com passo-a-passo de como usar o seu projeto em um cenário prático.

## Sessão Prática
Agora que você viu alguns conceitos sobre arquitetura da informação e como aplicá-los no contexto de sites de documentação, aqui estão algumas perguntas que vão ajudar você a colocar esse conhecimento em prática para o seu próximo website:

**A)** Quem é a audiência principal do seu projeto? Que nível de experiência você expera que esses usuários tenham para poderem utilizar o seu projeto?

**B)** Qual é o contexto ao redor do seu projeto? Pense sobre as motivações que levaram você a criar o projeto, o quão grande é a comunidade ao seu redor, se há "competidores" ou projetos similares já estabelecidos e como você se posiciona nesse cenário.

**C)** Considere o contexto do seu projeto e a sua audiência. Que tipo de conteúdo seus usuários precisam? Documentação tradicional e referência em texto seria suficiente, ou você precisa preencher algumas brechas com conteúdo teórico explicando elementos que fazem parte do seu ecossistema? Em caso afirmativo, você teria a disponibilidade de criar esse conteúdo de suporte, ou seria mais interessante linkar para uma fonte externa?

**D)** Que tipos de conteúdo servirão melhor os seus usuários? Há espaço para reutilizar conteúdo em múltiplos formatos? Que tipos de mídia você pode oferecer para enriquecer a experiência do usuário? Pense sobre gráficos, diagramas, demos em vídeo, slides…

**E)** Qual conteúdo precisa de qualquer maneira estar ali? Faça uma lista (ou uma planilha) com títulos provisórios para todo o conteúdo que você gostaria de ver no seu site de documentação. Depois, organize esses títulos por prioridade, chegando a uma lista final com 5 páginas de documentação que devem funcionar como o seu ponto de partida.

## Conclusão
Arquitetura de Informação é um campo de estudo que pode ajudar designers e criadores de conteúdo a tomar decisões acerca de como organizar e categorizar conteúdo, de maneira a otimizar a experiência dos usuários. No contexto de sites estáticos, a estrutura de conteúdo depende diretamente da hierarquia de diretórios, por isso é importante planejar com antecedência que tipos de tópicos, categorias, e tipos de conteúdo você quer ter no seu site.


