---
title: Como criar uma GitHub Action para importar posts do DEV.to em PHP com o Minicli - Vídeo + Tutorial
published: true
description: Esse post reúne os vídeos que produzi mostrando como criar uma GitHub Action em PHP com o Minicli para importar posts do DEV.to para um repositório de sua escolha.
tags: php, github, tutorial, braziliandevs
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kdl8nnrqkba92y9ut89l.png
---

[GitHub Actions](https://docs.github.com/en/actions) é uma funcionalidade oferecida pelo GitHub que permite a execução de processos automatizados que podem ser engatilhados por eventos tais como pull requests, pushs, novos comentários, mas também podem rodar em agendamento similar ao crontab. Actions podem ser combinadas para a criação de workflows automatizados diversos, que podem ser usados para CI/CD e também para outras finalidades. 

Repositórios open source tem acesso ilimitado a esse recurso, fazendo dessa funcionalidade uma ótima ferramenta para execução de tarefas agendadas, pequenos workers e bots.

Esse post reúne os vídeos que produzi mostrando como criar uma GitHub Action em PHP com o [Minicli](https://docs.minicli.dev), um microframework PHP para a linha de comando. O primeiro vídeo traz uma introdução rápida sobre o Minicli, útil para fornecer um contexto antes de iniciarmos o desenvolvimento da aplicação. No segundo vídeo, vemos como criar a aplicação que irá importar os posts do DEV.to e salvá-los como arquivos markdown. No terceiro e último vídeo, vemos como adaptar a aplicação para ser usada como GitHub Action.

Logo abaixo dos vídeos você encontra os comandos e arquivos necessários para reproduzir o tutorial.

### Prerequisitos para seguir o tutorial

Para executar os passos desse tutorial, você precisará de:

- Um ambiente de desenvolvimento PHP-cli 8.0+, com a extensão `php-curl` instalada.
- Composer instalado.

## Introdução ao Minicli (Opcional)

Nesse vídeo curto, mostro uma visão geral sobre o Minicli e como fazer o bootstrap de uma nova aplicação usando esse framework.

{% youtube _pXdTrffG98 %}

## Criando uma aplicação Minicli para importar posts do DEV.to

Nesse vídeo, criamos uma aplicação Minicli do zero e desenvolvemos um comando para importar posts de um usuário ou org do DEV.to, usando a API fornecida por essa plataforma de conteúdo.

{% youtube bMS5-hH-XR8 %}

### Bootstrap da aplicação

Você pode criar uma nova aplicação Minicli com o seguinte comando:

```shell
composer create-project minicli/application importDevTo
```

### Adicionando dependência minicli/curly

Vamos usar o pacote [minicli/curly](https://github.com/minicli/curly) para fazer requisições à API do DEV.to:

```shell
composer require minicli/curly
```

### Criando um novo comando

Para criar um novo comando que será chamado com `minicli import dev`, você precisará seguir a convenção estabelecida pelo Minicli: `app/Command/CommandNamespace/SubcommandController.php`.

```shell
mkdir app/Command/Import
touch app/Command/Import/DevController.php
```

Vamos precisar de alguns valores de configurações que poderão ser usados para customizar essa Action através de variáveis de ambiente. Abaixo, a versão mais atualizada do arquivo, já contendo alterações que serão trazidas no próximo vídeo:

```php
<?php

return [
    'app_path' => __DIR__ . '/app/Command',
    'debug' => true,
    'devto_username' => getenv('DEVTO_USERNAME') ?: 'erikaheidi',
    'data_path' => getenv('APP_DATA_DIR') ?: __DIR__ . '/devto'
];
```

Para usar esse config com a aplicação, você precisará editar o arquivo `minicli` na raiz da aplicação. Substitua a linha onde a aplicação é instanciada pela seguinte linha:

```php
(...)
$app = new App(require __DIR__ .'/config.php');
(...)
```

A versão mais atualizada do arquivo `DevController.php`, que contém o comando chamado por `minicli import dev`, está disponível abaixo. Note que essa versão não irá coincidir com a do vídeo pois contém algumas adaptações incluídas posteriormente para facilitar o uso prático dessa aplicação como GitHub Action.

```php
<?php

namespace App\Command\Import;

use Minicli\Curly\Client;
use Minicli\Command\CommandController;

class DevController extends CommandController
{
    public string $API_URL = 'https://dev.to/api';

    public function handle(): void
    {
        $this->getPrinter()->display('Fetching posts from DEV...');
        $crawler = new Client();

        if (!$this->getApp()->config->devto_username) {
            throw new \Exception('You must set up your devto_username config.');
        }

        $devto_username = $this->getApp()->config->devto_username;

        $articles_response = $crawler->get($this->API_URL . '/articles?username=' . $devto_username);

        if ($articles_response['code'] !== 200) {
            throw new \Exception('Error while contacting the dev.to API.');
        }

        if (!$this->getApp()->config->data_path) {
            throw new \Exception('You must define your data_path config value.');
        }

        $data_path = $this->getApp()->config->data_path;

        if (!is_dir($data_path) && !mkdir($data_path)) {
            throw new \Exception('You must define your data_path config value.');
        }

        $articles = json_decode($articles_response['body'], true);
        foreach($articles as $article) {
            $get_article = $crawler->get($this->API_URL . '/articles/' . $article['id']);

            if ($get_article['code'] !== 200) {
                $this->getPrinter()->error('Error while contacting the dev.to API.');
                continue;
            }

            $article_content = json_decode($get_article['body'], true);
            $date = new \DateTime($article_content['published_at']);
            $filepath = $data_path . '/' . $date->format('Ymd') . '_' . $article_content['slug'] . '.md';
            $file = fopen($filepath, 'w+');
            fwrite($file, $article_content['body_markdown']);
            fclose($file);

            $this->getPrinter()->info("Saved article: " . $article_content['title'] . " to $filepath");
        }

        $this->getPrinter()->info("Finished importing.", true);
    }
}
```

## Convertendo a aplicação demo em GitHub Action

Nesse vídeo, partimos da aplicação desenvolvida no vídeo anterior, fazendo pequenas adaptações e criando os arquivos necessários para usar a aplicação como GitHub Action.

{% youtube bMS5-hH-XR8 %}

### Criando um Dockerfile para a aplicação

O arquivo `Dockerfile` deve ser criado na raiz do repositório da sua GitHub Action. Ele será referenciado pelo arquivo `action.yml` que define o que essa Action irá executar.

```Dockerfile
FROM php:8.1-cli

RUN apt-get update && apt-get install -y \
    git \
    curl \
    libxml2-dev \
    zip \
    unzip

RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Composer and set up application
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
RUN mkdir /application
COPY . /application/
RUN cd /application && composer install

ENTRYPOINT [ "php", "/application/minicli" ]
CMD ["import", "dev"]
```

### Criando um action.yml

O arquivo `action.yml` define os metadados da Action e deve ser comitado na raiz do repositório.

```yaml
name: 'Import DEV.to posts'
description: 'Imports posts from DEV.to as markdown files'
outputs:
  response:
    description: 'Output from command'
runs:
  using: 'docker'
  image: 'Dockerfile'
```

### Montando um Workflow

O workflow deve ser criado no repositório onde você quer salvar os arquivos markdown. Esse arquivo pode ter qualquer nome desde que esteja dentro da pasta `.github/workflows` dentro do repositório.

O workflow abaixo irá criar um pull request sempre que houver uma diff entre a versão atual do repositório e o conteúdo importado pela Action. Ele utiliza três actions:

- `actions/checkout@v2` 
- `erikaheidi/importDevTo@v1.2`
- `eter-evans/create-pull-request@v3`

```yaml
name: Import posts from DEV
on:
  schedule:
    - cron: "0 1 * * *"
  workflow_dispatch:
jobs:
  main:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: erikaheidi/importDevTo@v1.2
        name: "Import posts from DEV"
        env:
          DEVTO_USERNAME: erikaheidi
          APP_DATA_DIR: ${{ github.workspace }}/devto
      - name: Create a PR
        uses: peter-evans/create-pull-request@v3
        with:
          commit-message: Import posts from DEV
          title: "[automated] Import posts from DEV"
          token: ${{ secrets.GITHUB_TOKEN }}
```

### Testando workflow 

Para testar o workflow, acesse o repositório e vá até a aba "Actions", selecione o workflow no sidebar da esquerda, e clique em "Run Workflow".

Após o workflow finalizar sua execução, você deverá ver um pull request aberto no repositório onde o workflow está definido (desde que haja posts que não foram importados já anteriormente).

![pull request criado automaticamente por esse workflow](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3lshy3552idlfch88svq.png)

## Conclusão

GitHub Actions é uma ferramenta poderosa e versátil oferecida pelo GitHub que nos permite executar workflows customizados que vão muito além de CI/CD. Nesse tutorial eu procurei passar um pouco mais de contexto para enriquecer os tutoriais em vídeo que compartilhei recentemente sobre o assunto.

## Links Úteis:

- [Playlist no YouTube](https://www.youtube.com/playlist?list=PLwZiXR0tm05y3qT_yKdL-BAtn0NFykO5I)
- [Repositório com todo o código desenvolvido nos tutoriais](https://github.com/erikaheidi/ImportPosts)
- [Documentação do Minicli](https://docs.minicli.dev)
- [Documentação do GitHub Actions](https://docs.github.com/en/actions)

