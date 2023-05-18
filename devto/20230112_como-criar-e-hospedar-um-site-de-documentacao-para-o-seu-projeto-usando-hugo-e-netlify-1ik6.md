---
title: Como criar e hospedar um site de documentação para o seu projeto usando Hugo e Netlify
published: true
description: Na segunda parte da nossa série, veja como criar um site de documentação com Hugo e hospedá-lo gratuitamente na Netlify 
tags: ptbr, bolhadev, iniciantes, docs
series: documentacao-para-iniciantes
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/thmr7v1zb4j4ct24g6df.png
# Use a ratio of 100:42 for best results.
# published_at: 2023-01-12 17:11 +0000
---

## Introdução
Num post anterior dessa série, nós vimos [dicas e boas práticas para criar um bom readme para o seu projeto](https://dev.to/erikaheidi/documentacao-tecnica-para-iniciantes-parte-1-criando-um-bom-readme-para-o-seu-projeto-3ebl), e como organizar um set inicial de docs para cobrir o básico.

Manter a documentação junto ao códio fonte pode ser uma boa solução em muitos casos, mas dependendo do tamanho do seu projeto e da quantidade de features ou oportunidades de customização, ter um site de documentação dedicado pode ser uma melhor opção de longo termo para manter a documentação organizada, facilitando a busca por conteúdo e também criando um local "canônico" para referenciar e compartilhar.

Neste post, que é a segunda parte da minha série "Documentação 101", veremos como criar um site de documentação usando o gerador de sites estático Hugo e hospedá-lo gratuitamente na Netlify. 

_Nota: Esse tutorial também pode ser usado para criar um blog pessoal, escolhendo um tema mais adequado para um blog. O processo é o mesmo!_

## Por que Hugo e Netlify?
Há várias formas de criar e hospedar uma documentação gratuitamente. Algumas combinações que eu já experimentei:

- GitHub Pages: geralmente os docs ficam em uma branch separada no mesmo repositório de projeto, pode ficar confuso com o tempo. Menos liberdade de customização.
- Readthedocs + mkdocs: essa é uma boa combinação usando o serviço gratuito [Readthedocs](https://readthedocs.org). O seu projeto ganha um subdomínio .readthedocs e você também pode usar um domínio customizado. No geral uma ótima solução, mas de vez em quando tenho problemas com a atualização automática.
- Netlify + Hugo: minha combinação favorita no momento, porque além de usar o Hugo (que é um excelente gerador de sites estáticos com muitas possibilidades de customização), habilita você a usar o recurso de "Deploy Preview" que eles oferecem como uma aplicação do GitHub. Sempre que uma PR é aberta, um preview do deploy é gerado com um endereço randômico e o bot da Netlify deixa um comentário na PR com o link para o preview. Super útil para fazer revisões! O único detalhe é que com a Netlify você vai querer usar um domínio customizado, caso contrário o seu site terá um endereço randômico que não tem nada a ver com o projeto.


## Instalando o Hugo

[Hugo](https://gohugo.io/) (_you-go_) é um gerador de sites estáticos bastante popular escrito em Go. Ele gera o conteúdo baseado em documentos markdown, que é a maneira ideal de manter a sua documentação desacoplada de estilos e formatação. Uma seção de metadados é adicionada no início de cada post, de forma muito similar ao que temos no editor básico de markdown aqui do DEV.

Você pode hospedar gratuitamente o seu site criado com Hugo no Netlify e também em outras plataformas.

### 1. Download e Instalação
Primeiro, faça o download da versão mais recente do Hugo, escolhendo a versão adequada para o seu sistema. Na [página de releases](https://github.com/gohugoio/hugo/releases) você pode encontrar todos os pacotes disponíveis. Evite usar o package manager do seu sistema (por exemplo, apt ou brew) já que as versões nesses repositórios geralmente estarão desatualizadas.


Para usuários Ubuntu, por exemplo, você pode fazer o download do `.deb` e instalar usando `dpkg`:

```shell
sudo dpkg -i ~/Downloads/hugo_0.107.0_linux-amd64.deb
```

Após completar a instalação, execute o comando `hugo version` para se certificar de que ele foi instalado com sucesso no seu sistema:

```shell
hugo version
```
Você deve obter um output similar a este:

```shell
hugo v0.107.0-2221b5b30a285d01220a26a82305906ad3291880 linux/amd64 BuildDate=2022-11-24T13:59:45Z VendorInfo=gohugoio
```

### 2. Criando um novo projeto Hugo

Agora você pode criar um novo site Hugo usando o comando seguinte. Para os exemplos nesse tutorial, eu usarei "mydocs" como nome do site.

```shell
hugo new site mydocs
```
Você obterá output similar ao seguinte:

```
Congratulations! Your new Hugo site is created in /home/erika/Projects/mydocs.

Just a few more steps and you're ready to go:

1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/ or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation.

```

Com a instalação básica feita, você agora pode começar a customizar o seu site.


### 3. Instalando um Tema

O [site oficial do Hugo]() tem uma grande coleção de temas prontos para serem instalados e usados. Você pode usar as categorias no menu da direita para filtrar os temas pela categoria "docs", assim encontrará os temas que são mais adequados para documentação.

Para esse tutorial, vamos usar o tema [Geekdoc](https://themes.gohugo.io/themes/hugo-geekdoc/), o mesmo que utilizei para a documentação do [yamldocs](https://yamldocs.dev).

De forma geral, você deve seguir as instruções de cada tema, já que alguns requerem dependências bem específicas. No caso do Geekdocs, o processo de instalação é bem simples e requer apenas que você faça o download e descompacte o arquivo na pasta `themes` do Hugo. Você pode fazer isso com os comandos seguintes:

```shell
cd mydocs/
mkdir -p themes/hugo-geekdoc/
curl -L https://github.com/thegeeklab/hugo-geekdoc/releases/latest/download/hugo-geekdoc.tar.gz | tar -xz -C themes/hugo-geekdoc/ --strip-components=1
```

O próximo passo é editar o seu arquivo `config.toml`, que no momento está super básico. Vamos adicionar algumas opções extras que são específicas desse tema e irão habilitar recursos adicionais.


Copie o seguinte conteúdo para o seu `config.toml`:

```toml
baseURL = "http://localhost"
title = "My Documentation Site"
theme = "hugo-geekdoc"

pluralizeListTitles = false

# Geekdoc required configuration
pygmentsUseClasses = true
pygmentsCodeFences = true
disablePathToLower = true

# Required if you want to render robots.txt template
enableRobotsTXT = true

# Needed for mermaid shortcodes
[markup]
  [markup.goldmark.renderer]
    # Needed for mermaid shortcode
    unsafe = true
  [markup.tableOfContents]
    startLevel = 1
    endLevel = 9

[taxonomies]
   tag = "tags"
```

Ao finalizar, não esqueça de salvar o arquivo.

Agora você pode rodar o servidor de desenvolvimento do Hugo para visualizar o seu site:

```shell
hugo server -D
```

Você deverá obter uma página como esta:

![Hugo website default](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/o5pren4q09oaqkvi37sy.png)

Naturalmente, está um pouco vazia, já que não há nenhum documento indexado ainda. Primeiro, vamos dar uma olhada na estrutura de arquivos da sua instalação, para ter uma idéia melhor de onde encontrar cada coisa:

```
.
├── archetypes
│   └── default.md
├── assets
├── content
├── data
├── layouts
├── public
│   ├── categories
│   ├── tags
│   ├── index.xml
│   └── sitemap.xml
├── resources
│   └── _gen
├── static
├── themes
│   └── hugo-geekdoc
└── config.toml

```

Aqui estão alguns diretórios importantes para manter no seu radar:

- **archetypes**: esse diretório contém templates para arquivos markdown que são gerados através do comando `hugo new`. Você pode ter archetypes diferentes para cada tipo de conteúdo que você publica.
- **content**: os arquivos de markdown com o conteúdo da sua documentação ou blog viverão aqui. 
- **layouts**: aqui você vai encontrar os arquivos HTML que formam a base do seu site. Esse diretório estará vazio por padrão, mas você pode checar o diretório `themes/hugo-geekdoc/layouts` e você encontrará os layouts que estão sendo usados pelo seu site no momento. Você vai notar que o tema replica a estrutura de diretórios da raiz, e isso é importante pois é o mecanismo usado para sobrescrever layouts a outros arquivos do tema. Para sobrescrever um layout do seu tema, você só precisa criar um arquivo com o mesmo nome na pasta `/layouts`, e esses terão uma precedência maior do que os layouts incluídos no tema.
- **static**: qualquer arquivo estático, como logos / diagramas e ouras imagens usadas no site podem ser colocadas nesse diretório, e ficarão disponíveis publicamente.
- **public**: nesta pasta é onde são gerados os arquivos HTML do seu site, é a pasta que deve ser configurada como "document root" ao hospedar o seu site.


A [documentação oficial](https://gohugo.io/getting-started/directory-structure/) (inglês) possui informações mais detalhadas sobre a estrutura de diretórios usada pelo Hugo.

### 4. Fazendo o "Build" dos seus docs

Agora que o site está "up and running", você pode começar a criar as suas páginas de documentação. Aqui é onde o trabalho realmente acontece, então não tenha pressa. Você pode começar com uma página básica do tipo "Getting Started" e ir melhorando sua documentação aos poucos. Em uma próxima parte dessa série, vamos falar sobre planejamento e organização de conteúdo.

Para criar uma nova página de documentação, você pode usar o comando "hugo new". Esse comando irá assumir o tipo de conteúdo que você está criando (archetype) baseado no caminho que você definir como output. Por exemplo:

```shell
hugo new docs/getting-started.md
```
```
Content "/home/erika/Projects/mydocs/content/docs/getting-started.md" created
```

Abra o arquivo criado e adicione algum conteúdo para testes. Depois, faça um reloas da sua página e você deve obter algo assim:

![Hugo website with initial getting started page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/528xhsnvmpd5pjru12yp.png)

Você vai notar que o markdown gerado tem um conteúdo pré definido, baseado no archetype do tipo "docs" que está definido pelo tema em `themes/hugo-geekdoc/archetypes/docs.md. Você pode sobrescrever esse conteúdo pré definido criando um arquivo com o mesmo nome no diretório "archetypes" na raiz do projeto.

Para aprender mais sobre archetypes, visite a [documentação odicial](https://gohugo.io/content-management/archetypes/).

## Publicando seu site Hugo na Netlify

Como mencionado anteriormente, há várias maneiras diferentes de hospedar um site Hugo gratuitamente. A maior vantagem da Netlify na minha opinião é a funcionalidade de *deploy preview* que você tem acesso através da interface do GitHub. Sempre que uma PR é aberta, o bot da Netlify deixa um comentário na PR com um link para prever as mudanças, assim facilitando o review.

Vamos agora ver como configurar tudo isso. Antes de avançar, assegure-se de comitar (e fazer o push) seu site Hugo para o GitHub ou GitLab, em um repositório dedicado para ele. Isso é um pré requisito para importar o seu site na Netlify.

### 1. Crie uma conta na Netlify

O primeiro passo é ter uma conta (gratuita) na Netlify. Você pode fazer o sign up com a sua conta do GitHub para facilitar a importação do seu projeto. 

Depois de fazer sua conta e logar no site, você será direcionado para uma dashboard como essa:

![Netlify Dashboard](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wuzo4gol99h5sqoidwei.png)

### 2. Importe o seu projeto
Clique no botão "Add new site" para criar um novo projeto. Você terá acesso a um menu dropdown. Escolha a primeira opção "Import an existing project" e siga as instruções para importar o seu projeto do GitHub / GitLab.

Você pode precisar autorizar a Netlify para que tenha acesso aos seus repositórios. Depois disso, você poderá escolher qual repositório quer importar. Escolha o repositório com a sua instalação do Hugo e avance para a próxima página.

### 3. Configurando opções de deploy para sites Hugo 
Nesta página você vai encontrar as opções de configuração para o deploy. Para nossa sorte, a Netlify já detecta que se trata se um site Hugo e configura as opções padrão. Graças ao fato de nosso tema ser simples e não usar node, por exemplo, podemos usar os valores de configuração padrão fornecidos pela Netlify.

![Netlify default deploy settings for Hugo](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bou34ef2d8zqyoivk24p.png)

Clique em "Deploy Site" para finalizar o processo.

### 4. Preview do Projeto
Ao voltar para sua dashboard, você vai notar que o seu novo site está no processo de "building", e já tem um subdominio randômico associado a ele. Você poderá adicionar um domínio próprio logo mais.

![Netlify Deploy](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/uanur1elhe9c9lp8kl4e.png)

Agora você pode acessar o site usando a url temporária e checar se ele está idêntico à sua versão local.

### 5. Configurando Deploy Preview
O recurso de "Deploy Preview" deve estar automaticamente habilitado para o seu projeto, mas você precisará [autorizar o app da Netlify no GitHub](https://docs.netlify.com/configure-builds/repo-permissions-linking/#authentication-with-the-netlify-github-app).

Para ter certeza de que essa feature está habilitada, acesse "Deploys" e então "Continuous Deployment" no menu da esquerda. Você deve encontrar uma seção como essa:

![Deploy previews default settings on Netlify](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/r00hjmfos4dtsfo6nvb1.png)

A [documentação oficial](https://docs.netlify.com/site-deploys/deploy-previews/) explica em maiores detalhes como configurar essa feature.

### 6. Configurando um domínio próprio (opcional)
O passo final para ter o seu site publicado seria adicionar um domínio próprio. Apesar de ser opcional, é extremamente recomendado, do contrário o seu site ficará acessível apenas pelo domínio randômico que não tem nada a ver com o projeto. Além disso, com o domínio configurado você terá  um certificado SSL do Let's Encrypt para que seu site fique acessível através de https - isso é gerado automaticamente pelo Netlify.

Para configurar um domínio próprio, acesse o link "Set up a custom domain" que aparece no passo número 2 da seção "Set up your website" na sua dashboard. Você pode usar um domínio ou subdominio que você já possui, e também pode registrar um novo domínio diretamente da interface da Netlify. Siga as instruções e esteja ciente que pode levar algumas horas até que o site fique acessível através do domínio customizado, dependendo da propagação do DNS.


Consulte a [documentação oficial da Netlify](https://docs.netlify.com/domains-https/custom-domains/) para maiores detalhes em como configurar um domínio próprio com o seu site.

## Conclusão 
Criar um site dedicado de documentação para o seu projeto é uma solução de longo termo que pode ajudar a aumentar a adoção e popularidade do projeto, facilitando a organização e indexação da sua documentação. 

Existem opções diferentes de sistemas e serviços de hospedagem onde você pode hospedar o seu site se documentação gratuitamente. Neste artigo, vimos como fazer isso usando o gerador de sites estáticos Hugo, com hospedagem pela Netlify.

No próximo artigo dessa série, veremos dicas práticas e recomendações para planejamento e organização da sua documentação, de forma a mantê-la limpa e fácil de acompanhar.
