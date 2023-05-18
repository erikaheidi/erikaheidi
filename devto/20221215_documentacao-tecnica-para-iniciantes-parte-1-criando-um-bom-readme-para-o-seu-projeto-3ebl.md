---
title: Documentação técnica para iniciantes, parte 1: criando um bom README para o seu projeto
published: true
description: Neste artigo veremos dicas importantas para criar um bom README para o seu projeto de software.
tags: ptbr, iniciantes, documentation
series: documentacao-para-iniciantes
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/r7yz6jty1lrnz978m0yg.png
---

Ter uma boa documentação para o seu projeto, principalmente em se tratando de projetos de código aberto, é um diferenciador importante que pode influenciar positivamente na popularização da sua aplicação ou biblioteca.

Infelizmente, a documentação de um projeto geralmente cresce em um ritmo bem mais lento do que o código em si, já que algumas implementações por mais simples que sejam acabam criando uma série de possibilidades de customização e casos de uso muito variados. Nunca é possível cobrir todos os casos de uso possíveis, e por isso mesmo o primeiro trabalho de um _technical writer_ é definir escopo e prioridades ao trabalhar com qualquer tipo de documentação.

Neste artigo, que é a **parte 1** de uma série sobre **Documentação Técnica para Iniciantes**, vou compartilhar dicas e sugestões para criar um bom README para o seu projeto.

## Sobre o README

O arquivo README é, muitas vezes, o primeiro contato que usuários terão com o seu projeto de software, já que esse conteúdo é o que verão primeiro ao serem direcionados para o repositório que guarda seu código fonte. Por essa razão, é importante priorizar o README como ponto de partida para a sua documentação.

Um resumo do que veremos no artigo de hoje:
- 1. Tamanho do README (menos é mais)
- 2. Dividindo para conquistar
- 3. Informações que não podem faltar
- 4. Usando badges para enriquecer o README

Naturalmente, projetos diferentes terão necessidades diferentes no que diz respeito ao README e o conteúdo que deve estar presente, mas as dicas que vou mostrar aqui são um bom ponto de partida para a maioria dos projetos de software.


## 1. Menos é Mais
Pode ser tentador colocar **tudo** no README, por exemplo: como instalar, como usar, como fazer deploy do projeto, como debugar... Mas o arquivo README deve ser visto mais como um ponto de entrada, onde você compartilha as informações mais importantes e adiciona links para documentos mais completos que descrevam com mais detalhes os tópicos relevantes.

Quando o projeto está começando, é natural que tenhamos apenas um README. Mas a verdade é que um README muito longo não é atrativo para os usuários, fica mais difícil encontrar as informações já que não há um menu para navegação ou tabela de conteúdo (TOC). Um README mais curto com links para outros documentos deixa o seu projeto mais organizado e também evita dar a impressão de que é "muito complicado", já que toda aquela informação como primeiro ponto de contato pode sobrecarregar os usuários.

Na próxima seção veremos como quebrar um README longo em arquivos separados sem precisar criar um site dedicado de documentação para o projeto.

 

## 2. Expandindo a documentação no próprio repositório

Não é necessário criar um site dedicado de documentação para quebrar o seu README em documentos separados. Uma boa alternativa é criar uma pasta chamada `docs` na raiz do seu projeto, e ter um `docs/README.md` que funciona como "index" da sua documentação. 

Nessa pasta você pode manter mais arquivos de markdown para dividir a sua documentação em tópicos. Assim, usuários podem navegar na documentação diretamente pela interface do GitHub, por exemplo. 

Vale lembrar que esses arquivos também estarão presentes no código fonte do projeto e estarão presentes quando o projeto for instalado pelo usuário em sua máquina ou servidor.

Idéias para docs que você pode ter nessa pasta:

- `installation.md` - Nesse documento você deve explicar em detalhes como instalar o projeto. Inclua dependências, pré requisitos, e outras informações relevantes para quem está entrando em contato com o projeto pela primeira vez.
- `usage.md` - Uma visão mais detalhada sobre os comandos e/ou forma de usar o projeto.
- `advanced.md` - Um documento para reunir casos de uso mais avançado e outras dicas que são mais específicas e vão além da utilização básica do projeto.

Essas são apenas algumas idéias que podem funcionar como ponto de partida. 

Caso você ache necessário organizar os seus docs usando uma estrutura de diretórios mais complexa, com vários níveis e subpastas, talvez essa não seja a melhor solução para a sua documentação. Nesse caso eu recomendo a criação de um site dedicado para a sua documentação, o que você pode fazer gratuitamente em serviços como [readthedocs.org](https://readthedocs.org) ou com GitHub Pages. Vamos cobrir esse tópico em um próximo artigo dessa série.

## 3. Informações que não podem faltar no seu README

Cada projeto tem suas necessidades, mas algumas informações  precisam estar presentes no seu README. Tentei reunir o que considero essencial aqui:

- uma visão geral sobre o projeto, incluindo a linguagem em que foi escrito, o que faz, e por que/como ele pode ser útil ao usuário;
- os requerimentos para instalar / executar o projeto;
- link para um documento que mostra como instalar o projeto;
- visão geral ou exemplo de utilização, para dar aos usuários uma noção de como eles vão rodar o projeto;
- dicas rápidas de debug ou resolução de problemas (também pode ser um link para um doc separado)
- links para docs, artigos, blogs, demos, e  outros recursos que podem ajudar o usuário a fazer o melhor uso do projeto.

O ideal é que esses ítens sejam instruções curtas com links para documentos mais detalhados sempre que necessário.

Dependendo do tamanho do seu projeto e suas metas, também é interessante ter os seguintes arquivos de markdown na raiz do seu projeto, e incluir links para esses arquivos no seu README:

- `CONTRIBUTING.md` - esse arquivo deve descrever o processo para contribuir com o seu projeto, incluindo orientações a desenvolvedores que queiram submeter um pull request.
- `CODE_OF_CONDUCT.md` - o código de conduta informa os usuários de expectativas para sua conduta enquanto participante da comunidade formada ao redor do projeto. Uma boa referência é o [Contributor Covenant](https://www.contributor-covenant.org/).
- `LICENSE` - O arquivo LICENSE contém detalhes sobre a licença do seu projeto, e como ele pode ser utilizado. [Essa página](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository) da documentação do GitHub contém detalhes sobre várias licenças para ajudar você a escolher a mais adequada. Uma opção bastante popular é a MIT, que permite uso irrestrito.

Apesar de não serem especificamente relacionados à utilização do projeto, esses arquivos também fazem parte do corpo de documentação e são relevantes no contexto de open source / código aberto.

Se o seu projeto estiver no GitHub, links para esses arquivos serão exibidos no sidebar da direita, abaixo da seção "about" do projeto, como na imagem abaixo:

![secao about do projeto mostra links para contributing, readme, license, e code_of_conduct](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kgjwthm3urr466yygrj6.png)

### Exemplo de Estrutura

Nesse exemplo vocês podem ver uma estrutura básica em markdown que pode ser usada como base para construir o seu README:

```markdown
# Nome do Projeto

Um parágrafo contendo uma visão geral do projeto, principais features, e como esse projeto pode servir o usuário.

## Requirements

Aqui você deve dar uma idéia geral sobre o que o usuário vai precisar instalar ou configurar para poder usar o seu projeto. 

- Pre requisito 1
- Outro pre requisito

Aqui você inclui [um link](https://url) para a documentação sobre como instalar o projeto.

## Usage

Aqui você deve incluir alguns exemplos de comandos e o que eles fazem, depois linkar para um outro doc com mais detalhes.

For more details, check our [getting started guide](https://url).

## Resources

Inclua aqui outros links relevantes.

- [Link 1](https://url)
- [Link 2](https://url)
- [Link 3](https://url)
```

## 4. Usando Badges

Para enriquecer o seu README com informações de rápido acesso e que são geradas automaticamente, como por exemplo status da última execução dos testes ou a versão estável mais recente, você pode usar **badges**. Existem serviços diferentes que oferecem badges para projetos de código aberto. 

### Badges de GitHub Actions
Você pode obter badges diretamente de suas GitHub Actions - para isso, acesse a página do workflow no seu repositório, clique no botão com três pontos que aparece no topo à direita, e clique em "Create status badge" no menu que vai aparecer:

![Screenshot showing where to find the menu to create a status badge from github actions](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bpb6g6xofhwm8vctwcem.png)

Isso vai mostrar uma janela com markdown que você pode copiar diretamente para o seu arquivo README.

### Shields.io
Outra excelente opção é o site [Shields.io](https://shields.io/), que oferece um grande número de badges que você pode usar gratuitamente com o seu projeto de código aberto.

Por exemplo, a imagem gerada por essa URL mostra a release mais recente do meu projeto [yamldocs](https://github.com/erikaheidi/yamldocs):

```
https://img.shields.io/github/v/release/erikaheidi/yamldocs?sort=semver&style=for-the-badge
```

![latest stable release of yamldocs](https://img.shields.io/github/v/release/erikaheidi/yamldocs?sort=semver&style=for-the-badge)

Aqui um exemplo de README usando vários badges:

![Shows badges from yamldocs project readme](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5frlxoex4fyke8ra1avf.png)

## Conclusão

Documentação é uma parte essencial de qualquer projeto de software, e deve ser levada a sério desde o início. A melhor forma de iniciar é criando um bom arquivo README que mostra informações essenciais sem sobrecarregar os usuários com conteúdo que eles não precisam para começar a usar o projeto.

Na próxima parte dessa série, vamos falar sobre outros tipos de documentação, e como você pode hostear os seus docs quando decidir que é chegado o momento em que o seu projeto precisa de um site dedicado para documentação.