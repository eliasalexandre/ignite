# RENTALX

- repository

  Camada/class encarregado de fazer toda manipulação da aplicação.

  - EX: fazer manipulação do banco de dados (CRUD),

- patterns

  - singleton
    - criar instacias que vão ser globais na aplicação

- Stream

  - permite ler determinado arquivo por _partes_: `chunck`

  - `pipe()`
    - para cada **pedaço/chunk** lido ele vai receber esse pedaço

- Mapper: transforma o Objeto e devolve os dados desejados.

- Usos

| func                 | desc                          |
| -------------------- | ----------------------------- |
| `fs.promises.stat`   | verifica se um arquivo existe |
| `fs.promises.unlink` | remove um arquivo             |

## Docker

- Dockerfile
  - passo a passo de como irémos rodar a aplicação

| passo                     | descrição                                              |
| ------------------------- | ------------------------------------------------------ |
| `FROM`                    | imagem que vai ser rodada, é sempre o primeiro comando |
| `WORKDIR`                 | o diretório onde as informações vão ficar              |
| `COPY *file* *dir*`       | copia um arquivo para dentro do diretório              |
| `RUN command`             | vai executar o comando                                 |
| `EXPOSE [portnumber]`     | porta onde esse servoço vai estar rodando              |
| `CMD ['npm','run','dev']` | vai rodar comandos como se fosse no terminal           |

---

- commands

| comando                                   | descrição                                                                   |
| ----------------------------------------- | --------------------------------------------------------------------------- |
| `docker ps`                               | lista os containers ativos                                                  |
| `docker build -t rentx .`                 | vai construir a imagem, e dar o nome de **rentx** no diretório atual: **.** |
| `docker run -p 3333:4444 rentx`           | executa a imagem **rentx**                                                  |
| `docker exec -it [imagem_nome] /bin/bash` | vai entrar na raiz da imagem                                                |

---

- `-p`: faz um mapeamento das portas.
  - sempre que for acessado na máquina _local_ o endereço **3333**, vai jogar no enderço do _docker_ na porta **4444**

## docker-compose

- compositar/administar uma imagem docker. _ex: definir var ENV_

- docker-compose.yml

| func                                         | descrição                                          |
| -------------------------------------------- | -------------------------------------------------- |
| `VERSION: "3.7"`                             | define a versao do compose                         |
| `services: {}`                               | configurações dos servicos a ser orquestrado       |
| `s: app: {}`                                 | define o nome do serviço                           |
| `s: app: build .`                            | vai executar o comando `build .` no serviço        |
| `s: app: ports: { - 3333:4444 }`             | mapear porta local para o docker                   |
| `s: app: volumes: { - .:/usr/app }`          | de `.` principal dir para o `/usr/app`             |
| `docker-compose up`                          | executa o arquivo do compose ouvindo as alterações |
| `docker-compose up -d`                       | vai ficar executando em background                 |
| `docker-compose logs [IMAGE-NAME: rentx] -f` | vai mostra os logs                                 |

- docker comandos

  - `docker ps -a`: lista todos os containers, mesmo os offlines
  - `docker rm [ID ou NAME]`: vai remover a imagem docker
  - `docker start NAME`: vai iniciar a imagem docker
  - `docker stop ID`: vai para de executar a imagem docker
  - `docker exec -it NAME /bin/bash`: acessa a raiz/DIR da imagem
  - `docker logs NAME -f`: exibe os logs da imagem

- docker compose

  - `docker-compose up id`: subir o container em BG
  - `docker-compose start`: inicia a execução do compose
  - `docker-compose stop`: para de executar o compose
  - `docker-compose down`: remove tudo que foi criado
  - `docker-compose up --force-recreate`: recria com novas configurações
  - `docker exec ID cat /etc/hosts`: exeibe infomações sobre o host. ex: IP
  - `docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' ID`: exeibe o IP do ID

- Ao criar serviçoes eles rodam sobre diferentes images, cada imagem tem o seu própio IP.
  - pode ser resolvido configurando para ambos rodarem na mesma rede.
    ex: `network_mode:host`: **configura o modo de rede**

## Testes

> Não são feitos em banco de dados de produção

- Testes unitários

  - teste de regras de negócio
  - testes em pedaços/unidades da aplicação
  - ex: teste de uma feature

- testes de integração

  - Quando é testado o fluxo da aplicação inteira

- TDD: test driven development.

  - metodologia usada para fazer testes.
  - quando é feito primeiro o teste e em seguida é feito a função.

- SuperTest
  - permite criar servidor HTTP ao testar a aplicação

## Jest

biblioteca usada para programar os testes em _JS/TS_

- instalando

  - `yarn add jest; yarn add @types/jest`
  - `yarn add ts-jest -D`: vai rodar os testes em **TS**

- iniciando
  - `yarn jest --init`: vai fazer as alterações de acordo com as respostas

configurando **jest.config.ts**

| config         | desc                                               |
| -------------- | -------------------------------------------------- |
| preset         | `: "ts-jest"`                                      |
| testMatch      | pastas onde vão ser feito os testes                |
| `**/*.spec.ts` | vai ser realizando em todos os arquivos _.spec.ts_ |
| `bail: true`   | para de rodar apos algum erro                      |

- **funções**
  - `describe(groupTestName, () => {})`: serve para agrupar os testes.

## Infra

separação das implementações de regras de negócio da aplicação

- typeorm query

```ts
// andWhere : concatena outros wheres

const carsQuery = this.repository
  .createQueryBuilder("c")
  .where("available = :available", { available: true });

if (brand) {
  carsQuery.andWhere("c.brand = :brand", { brand });
}
```

## foreignKeys

faz junções/relacionamento de tabelas.

| func                               | desc                                                                 |
| ---------------------------------- | -------------------------------------------------------------------- |
| `name: 'FKCategoryCar'`            | nome para descrever a foreignKey                                     |
| `referenceTableName: 'categories'` | tabela de origin, tabela PAI                                         |
| `referencedColumnNames: ['id']`    | faz referencia dos campos que estão sendo relacionados na tabela PAI |
| `columnNames: ["category_id"],`    | faz referencia da coluna na tabela atual                             |
| `onDelete: "SET NULL"`             | quando a tabela PAI sofrer alteração/delete qual ação tomar          |
| `onDelete: "CASCADE"`              | a coluna atual vai ser removida com a do PAI                         |

## Tokens

- refresh token: O sistema vai refazer/atualizar o token antes de expirar

## Storages

> Storage S3: armazenamento da Amazon

- armazenamentos específicos dentro da Nuvem, onde podemos fazer upload de arquivos para dentro deles.

- bucket
  pasta/arquivo, containers para armazenamento de dados

- criar usuário

> An IAM user is an identity with long-term credentials that is used to interact with AWS in an account.

1 - ir para **"iam"**
2 - na aba usuários vai em add novo

- send mail
  aws-SES - simple email service

## Email providers

- dedicated IPS: garante um único IP para o envio dos nossos emails, sem compartilhar (IP) com outras aplicações garantindo a reputação dos emails que vamos enviar.

- [x] SES-Amazon (Simple Email Service)
- [] SendGrid
- [] mailGun
- [] mailchimp

## EC2

- máquina/server para hospedar o código

- criando usuário para gerenciar o Server

  ```sh
  sudo adduser app
  sudo usermod -aG sudo app

  sudo su - app # entra no user app
  ```

- adiciona forma de authenticação: `ssh {user}@[IP]`:`ssh app@12.12.12`

  | VI command | descr                      |
  | ---------- | -------------------------- |
  | `i`        | abilita a edição/inserção  |
  | `ctrl+c`   | finaliza a edição/inserção |
  | `:wq!`     | salva o arquivo            |

  ```sh
  mkdir .shh
  chmod 700 .ssh/ # apenas o usuário criando terá total acesso!

  cd .ssh/
  touch authorized_keys

  vi authorized_keys # abre a arquivo criado, adicionar a chave publica *ssh-keygen*

  logout # desligá o usuário atual: app
  exit # desligá o usuário atual: app

  sudo service ssh restart # reinicia o service de SSH
  ```

  - [x] adicionar o [Node.js](https://github.com/nodesource/distributions/blob/master/README.md)
  - [x] adicionar [Docker](https://docs.docker.com/engine/install/ubuntu/) na máquina

## babel to build

- JavaScript compiler

- libs: `yarn add @babel/cli @babel/core @babel/node @babel/plugin-proposal-class-properties @babel/plugin-proposal-decorators babel-plugin-module-resolver babel-plugin-transform-typescript-metadata @babel/preset-typescript @babel/preset-env -D`

## Connect to Github

- gera ssh key no user: APP
  
  ```sh
    ssh-keygen
    
    cat cat ~/.ssh/id_rsa.pub # pega a chave gerada
  ```
  
## installing

- basic dev dependencies

  `@babel/cli @babel/core @babel/node @babel/plugin-proposal-class-properties @babel/plugin-proposal-decorators @babel/preset-env @babel/preset-typescript @types/jest  @types/jsonwebtoken @types/uuid babel-plugin-module-resolver babel-plugin-transform-typescript-metadata cross-env eslint-config-prettier eslint-import-resolver-  typescript eslint-plugin-import-helpers eslint-plugin-prettier ts-jest ts-node-dev tsc
   onfig-paths`
