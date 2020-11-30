# Hash - Desafio de Arquitetura

[Clique aqui](https://github.com/hashlab/hiring/blob/master/challenges/pt-br/sre-challenge.md) para acessar o desafio.

## Premissas:

- Abordaremos o canary deployment para realizar o rollout transparente das aplicações, e para isso, utilizamos o Istio como gerenciador da implantação;
- Front-end já está desacoplado do Monolith;
- Outros microserviços que compõe a aplicação completa já são contenerizados e executados no Kubernetes e configurados com Istio;
- Alguns microserviços que dependem do Cart e Checkout serão atualizados no decorrer da migração, mais especificamente, os apontamentos para os services do Kubernetes;
- Utilizamos infraestrutura como código com Terraform em projetos distintos entre infraestrutura e aplicação;
- Utilizamos o conceito de GitOps para a implementação dos nossos projetos;
- Fluxo contínuo de implementação com alguma suposta ferramenta de CI/CD e com pipeline configurada no próprio repositório do código, ativada via branch "main";
- Organização e projeto criados no Google Cloud, e com billing account ativo;
- Cloud Resource Manager API e Container API habilitados;
- Database unica para o servico monolith;
- Quebramos o Cart e Checkout em dois microserviços independentes.

## Tecnologias utilizadas:

- Terraform;
- Google Cloud;
- Google Kubernetes Engine;
- Docker;
- Istio configurado a partir de addons (beta) via Terraform, sem Kiali e Grafana (tínhamos a opção de provisionar via Helm ou manifests do Kubernetes).

## Extraindo os microserviços:

1. Identificamos os módulos e dependências do microserviço que nós quebramos, tal como comunicação entre os demais serviços, e tabelas/estrutura de banco de dados;

2. Selecionamos a primeira função chamada Cart para quebrarmos a partir do  Monolith, com isso, criamos um novo projeto no GitHub e refatoramos a funcionalidade para adaptar-se à nova estrutura. Assim, temos um novo microserviço e um banco de dados desacoplado e pronto para receber requisições dos demais serviços e lembrando que será necessário refatorar os demais serviços que consomem o Cart para o novo endpoint de sua API.

3.

Etapa2 : Identifique os modulos que desejamos romper e identificar as tabelas do banco de dados.
Monolith, que atende o /cart e o /checkout

Etapa 2: Divida as tabelas que correspondem a esses modulos e envolva com um serviço
	identificar quais tabelas são usadas pelo modulo cart e dividi-las em seu próprio serviço. O serviço agora é a única coisa que pode acessar suas tabelas, implemente o microservico que cria uma API para que os outros serviços possam consumir os dados do cart e atualize os serviços foo e bar que chamavam o monolith/cart para V2, chamando a API do cart (novo microservico).

Atualize o código que antes dependia das tabelas do banco de dados diretamente para chamar este novo serviço
Enxague e repita

## Projetos:

- [terraform-google-cloud-network](https://github.com/letsrockthefuture/terraform-google-cloud-network): Responsável por provisionar a infraestrutura de rede no Google Cloud para o nosso Kubernetes Engine cluster.
- [terraform-google-kubernetes-engine](https://github.com/letsrockthefuture/terraform-google-cloud-kubernetes-engine): Responsável por criar nosso Kubernetes Engine cluster e os node pools para nossas aplicações.
- [Monolith](https://github.com/letsrockthefuture/monolith): Aplicação que será dividida em dois microserviços independentes, Cart e Checkout.
- [Cart](https://github.com/letsrockthefuture/cart): Microserviço reponsável pelo carrinho de compras.
- [Checkout](https://github.com/letsrockthefuture/checkout) Microserviço reponsável por finalizar a compra.

### Rollout canary para o microserviço Cart:

![alt text](images/cart_canary.png)

### Rollout canary para o microserviço Checkout:

![alt text](images/cart_canary.png)

## Referências:

- [Migrating a monolithic application to microservices on Google Kubernetes Engine - Google Cloud](https://cloud.google.com/solutions/migrating-a-monolithic-app-to-microservices-gke)
- [Supporting your migration with Istio mesh expansion: Concept - Google Cloud](https://cloud.google.com/solutions/supporting-your-migration-with-istio-mesh-expansion-concept)
- [Migrating a Monolithic Website to Microservices on Google Kubernetes Engine - Qwiklabs](https://www.qwiklabs.com/focuses/11953?parent=catalog)
- [How to break a Monolith into Microservices - Martin Fowler](https://martinfowler.com/articles/break-monolith-into-microservices.html)
- [Low-risk Monolith to Microservice Evolution Part I - Christian Posta](https://blog.christianposta.com/microservices/low-risk-monolith-to-microservice-evolution/)
- [Low-risk Monolith to Microservice Evolution Part II - Christian Posta](https://blog.christianposta.com/microservices/low-risk-monolith-to-microservice-evolution-part-ii/)
- [Low-risk Monolith to Microservice Evolution Part III - Christian Posta](https://blog.christianposta.com/microservices/low-risk-monolith-to-microservice-evolution-part-iii/)