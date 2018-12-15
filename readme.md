<p align="center">
	</a>
	<a href="https://git.io/col">
		<img src="https://img.shields.io/badge/%E2%9C%93-collaborative_etiquette-brightgreen.svg" alt="Collaborative Etiquette">
	</a>
	<a href="https://discord.gg/bBpQHym">
		<img src="https://img.shields.io/discord/447118387118735380.svg?logo=discord" alt="chat on Discord">
	</a>
	<a href="https://twitter.com/intent/follow?screen_name=eoscostarica">
		<img src="https://img.shields.io/twitter/follow/eoscostarica.svg?style=social&logo=twitter" alt="follow on Twitter">
	</a>
	<a href="#">
		<img src="https://img.shields.io/dub/l/vibe-d.svg" alt="MIT">
	</a>
</p>

<p align="center">
	<a href="https://eoscostarica.io">
		<img src="https://cdn.rawgit.com/eoscostarica/assets/574d20a6/logos/eoscolors-transparent.png" width="300">
	</a>
</p>

# EOS Local 

A docker compose based development environment for EOS DApp Development.   

This is easiest way maintain a local environment for development that is guaranteed to work out-of-the-box across the different host operating systems: Mac OS, Windows and Linux.  

A common reusable docker based microservices architecture boilerplate and development environment that will allow to deploy EOS appliactions faster.

It's inspired on MonsterEOS' EOSIO DreamStack architecture.

EOS Local is a community-driven project led by EOS Costa Rica. We welcome contributions of all sorts. There are many ways to help, from reporting issues, proposing features, improving documentation, contributing code, design/ux proposals.

**Important Disclaimer: This is a Work in Progress** 

## Advantages

- Get started with EOS DApp development in less than 5 minutes with a single command.
- Focus on your biz logic, not on configurations or integrating commonly used third party services.
- Scalable architecture. 
- Deploy your dApp dedicated services easily to any infrastructure provider with containers.  
- Ability to run multiple versions of EOS with different configuration with no conflicts.
- This project follows EOS DApp development best practices.

## Technical Specs

- Fully virtualized EOS blockchain development environment.
- Inteligent automated blockchain replay. ( no more replay flag shenanigans )
- Microservices architecture.
- Out-of-box services: 
  - Postgres database.
  - Mongodb database.
  - Demux service.
  - Eos-dev node for contract dev and compilation.
  - Eos fullnode with history.
  - Graphql endpoint.
  - Reactjs client with:
    - Scatter integration.
    - Lynx integration.
    - EOS Account profile page.
    - Material UI.
    - GraphQL Apollo client.
- Services accesible through virtual host names both from host machine and within the docker network.
- Handy scripts for interacting with the local EOS services.
- Gulp as global task manager.
- Automated code linting and testing.
- Automated "seeding" of testing accounts and contract compilation.
- Continuous Integration and Deployment. ( Travis and Netlify )
- Kubernetes support ( coming soon https://github.com/eoscostarica/eos-local/issues/8 )

*Note: at the moment we are not using a docker container for running the React client due to issues related to hot reloading the app efficiently*

## Getting started

Basic knowledge about Docker, Docker Compose, EOS and NodeJS is required.

### Installation

**Global Dependencies**

- Docker https://docs.docker.com/install/.   
At least 7GB RAM (Docker -> Preferences -> Advanced -> Memory -> 7GB or above)
- Install node.js v10 on your machine. We recommend using [nvm](https://github.com/creationix/nvm) and [avn](https://github.com/wbyoung/avn) to manage multiple node.js versions on your computer.
- Yarn https://yarnpkg.com/lang/en/docs/install/.
- Gulp CLI  `yarn global add gulp-cli`.

**NPM packages**

- run `yarn` on the root directoty to install node packages required by `gulp-cli`

## Commands

- `gulp setup` run chain initilization and database migrations.
- `gulp start` starts the docker containers.
- `gulp stop` stops and removes all containers.
- `gulp restart` restarts all services.
- `gulp flush` stop all services and remove all blockchain and database data.
- `gulp logs` displays and folows all services logs.

## Directory Structure

```
.
├── docs/ .............................................. documentation files and media
├── services/ .......................................... microservices
|   ├── demux/ ......................................... demux-js service
|   |   ├── database/ .................................. postgres config and migrations
|   |   ├── src/ ....................................... application biz logic 
|   |   ├── Dockerfile ................................. service image spec 
|   |   ├── pm2.config.js .............................. process specs for pm2
|   |   ├── tsconfig.json .............................. tslint config
|   |   ├── tslint.json ................................ code style rules
|   |   └── package.json ............................... service dependencies manifest
|   |
|   ├── eosiodev/ ...................................... eos-dev node for contact development
|   |   ├── config/ .................................... eos node config
|   |   ├── contracts/ ................................. smart contracts 
|   |   ├── scripts/ ................................... chain and wallet init scripts
|   |   ├── Dockerfile ................................. service image spec 
|   |   └── start.sh ................................... service startup script
|   |
|   ├── eos-fullnode/ .................................. eos fullnode
|   |   ├── config.ini ................................. eos node configuration file
|   |   ├── Dockerfile ................................. service image spec 
|   |   └── start.sh ................................... service startup script
|   |
|   └── frontend/ ...................................... reactjs frontend
|      ├── public/ .................................... static and public files
|      ├── src/ ....................................... reactjs views and components
|      ├── config-overrides.js ........................ configuration overrides for `cra`
|      ├── .env ....................................... environment variables
|      ├── .eslintrc .................................. code style rules
|      └── package.json ............................... service dependencies manifest
|   
├── docker-compose.yaml ................................ docker compose for local dev
├── contributing.md .................................... contributing guidelines
├── license ............................................ project license
├── readme.md .......................................... project documentation
├── netlify.toml ....................................... netlify config file
├── .travis.yml ........................................ travis ci config file
├── .editorconfig ...................................... common text editor configs
└── package.json ....................................... dependencies manifest for gulp-cli
```

## Services

### demux

Demux is a backend infrastructure pattern for sourcing blockchain events to deterministically update queryable datastores and trigger side effects. 

Taking inspiration from the [Flux Architecture](https://facebook.github.io/flux/docs/in-depth-overview.html#content) pattern and [Redux](https://github.com/reduxjs/redux/), Demux was born out of the following qualifications:

1. A separation of concerns between how state exists on the blockchain and how it is queried by the client front-end
1. Client front-end not solely responsible for determining derived, reduced, and/or accumulated state
1. Ability for blockchain events to trigger new transactions, as well as other side effects outside of the blockchain
1. The blockchain as the single source of truth for all application state

Learn more at https://eosio.github.io/demux-js/.

### eosiodev

Due to the fact that the eosio/eos image does not contain the required dependencies for contract development (this is by design, to keep the image size small), you will need to utilize the eosio/eos-dev image. This image contains both the required binaries and dependencies to build contracts using eosiocpp.

https://hub.docker.com/r/eosio/eos-dev/ the base image can be found at https://github.com/EOSIO/eos/blob/master/Docker/dev/Dockerfile.

### fullnode

This is node the provides the RPC API.

See fullnode cofiguration at https://github.com/eoscostarica/eos-local/blob/master/services/eos-fullnode/config.ini

https://hub.docker.com/r/eosio/eos/ the base image source code can be found at https://github.com/EOSIO/eos/blob/master/Docker/Dockerfile.

### postgres

Postgres database instance for the demux service.

### mongodb

MongoDB instance for the fullnode. 

The eosio::mongo_db_plugin provides archiving of blockchain data into a MongoDB. It is recommended that the plugin be added to a non-producing node as it is designed to shut down on any failed insert into the MongoDB and is resource intensive.

https://developers.eos.io/eosio-nodeos/docs/mongo_db_plugin

## EOS Documentation & Resources

- https://github.com/EOSIO/eos/tree/master/Docker  
- https://developers.eos.io  
- https://learn.eoscostarica.io  
- https://github.com/slowmist/eos-smart-contract-security-best-practices  
- https://nadejde.github.io/eos-token-sale  
- https://docs.docker.com/kitematic/userguide/  

## Frequently Asked Questions

### How does this project compares to EOSFactory ?

EOSFactory is Python based framework for building and testing EOS smart contracts. This project is Docker based and serves as boilerplate to start an scalable EOSIO project with microservices architecture following best practices at all levels. It includes many required services for large scale EOSIO based applications and ReactJS client with Scatter and Lynx already integrated. 

EOS Local is somehow language agnostic in the sense you can you can spin up services more services using any programming languague. However the out-of-the-box services are JavaScript based.

## Contributing

We use a Kanban-style board. That's were we prioritize the work. [Go to Project Board](https://github.com/eoscostarica/eos-local/projects/3).

The main communication channels are [github issues](https://github.com/eoscostarica/eos-local/issues) and [EOS Costa Rica's Discord server](https://eoscostarica.io/discord). Feel to join and ask as many questions you may have.

Our weekly sync call is every Monday 1:00 AM UTC. [meet.eoscostarica.io](https:/meet.eoscostarica.io).

Contributing Guidelines https://learn.eoscostarica.io/open-source/.

Please report bugs big and small by [opening an issue](https://github.com/eoscostarica/eos-local/issues)

## Awesome Lists

https://github.com/EOS-Nation/Awesome-EOS
https://github.com/DanailMinchev/awesome-eosio
https://github.com/veggiemonk/awesome-docker
https://github.com/dhamaniasad/awesome-postgres
https://github.com/ramnes/awesome-mongodb
https://github.com/enaqx/awesome-react
https://github.com/jaredpalmer/awesome-react-render-props
https://github.com/chentsulin/awesome-graphql

## About EOS Costa Rica

EOS Blockchain is aiming to become a decentralized operating system which can support large-scale decentralized applications.

EOS Costa Rica supports the EOSIO community by maintaining and contributing to open source initiatives, meetups and workshops.

We challenge ourselves to provide the EOS platform with a strong geographical and political diversity by running the most robust EOS Block Producer possible from Costa Rica; We pledge to leverage our talent, experience, and sustainable internet resources to meet such an important challenge.

[eoscostarica.io](https://eoscostarica.io)

## License

MIT © [EOS Costa Rica](https://eoscostarica.io)  

## Contributors

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore -->
| [<img src="https://avatars0.githubusercontent.com/u/391270?v=4" width="100px;"/><br /><sub><b>Gabo Esquivel</b></sub>](https://gaboesquivel.com)<br />[🤔](#ideas-gaboesquivel "Ideas, Planning, & Feedback") [📖](https://github.com/eoscostarica/eos-dapp-dev-env/commits?author=gaboesquivel "Documentation") [💻](https://github.com/eoscostarica/eos-dapp-dev-env/commits?author=gaboesquivel "Code") [👀](#review-gaboesquivel "Reviewed Pull Requests") | [<img src="https://avatars2.githubusercontent.com/u/349542?v=4" width="100px;"/><br /><sub><b>Daniel Prado</b></sub>](https://github.com/danazkari)<br />[💻](https://github.com/eoscostarica/eos-dapp-dev-env/commits?author=danazkari "Code") [📖](https://github.com/eoscostarica/eos-dapp-dev-env/commits?author=danazkari "Documentation") [🤔](#ideas-danazkari "Ideas, Planning, & Feedback") [👀](#review-danazkari "Reviewed Pull Requests") | [<img src="https://avatars1.githubusercontent.com/u/1179619?v=4" width="100px;"/><br /><sub><b>Jorge Murillo</b></sub>](https://github.com/murillojorge)<br />[🤔](#ideas-murillojorge "Ideas, Planning, & Feedback") [📖](https://github.com/eoscostarica/eos-dapp-dev-env/commits?author=murillojorge "Documentation") [🎨](#design-murillojorge "Design") [💻](https://github.com/eoscostarica/eos-dapp-dev-env/commits?author=murillojorge "Code") [👀](#review-murillojorge "Reviewed Pull Requests") | [<img src="https://avatars0.githubusercontent.com/u/5632966?v=4" width="100px;"/><br /><sub><b>Xavier Fernandez</b></sub>](https://github.com/xavier506)<br />[🤔](#ideas-xavier506 "Ideas, Planning, & Feedback") [📝](#blog-xavier506 "Blogposts") [📢](#talk-xavier506 "Talks") [🚇](#infra-xavier506 "Infrastructure (Hosting, Build-Tools, etc)") | [<img src="https://avatars2.githubusercontent.com/u/13205620?v=4" width="100px;"/><br /><sub><b>Rubén Abarca Navarro</b></sub>](https://github.com/rubenabix)<br />[🤔](#ideas-rubenabix "Ideas, Planning, & Feedback") [👀](#review-rubenabix "Reviewed Pull Requests") |
| :---: | :---: | :---: | :---: | :---: |
<!-- ALL-CONTRIBUTORS-LIST:END -->
Thanks goes to these wonderful people ([emoji key](https://github.com/kentcdodds/all-contributors#emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore -->
<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/kentcdodds/all-contributors) specification. Contributions of any kind welcome!
