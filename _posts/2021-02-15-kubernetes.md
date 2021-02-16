---
layout: post
title:  "Kubernetes simplificado: o que você precisa saber para começar"
date:   2021-02-15 17:11:41 -0300
categories: devops kubernetes
---

O Kubernetes, também conhecido como k8s por possuir 8 letras entre o k e o s, surgiu nos últimos anos e tomou de assalto o mercado. O principal propósito do k8s é a orquestração de contêineres. A tecnologia trouxe diversos benefícios para empresas grandes e pequenas. Empresas como IBM, Pinterest, Booking.com, Spotify e BlaBlaCar o utilizam. Entretanto, para desenvolvedores se tornou obscuro e incompreensível. Em muitos momentos se parece algo mágico e distante de tecnologias anteriores de infra-estrutura.

Nesse artigo vamos explorar alguns conceitos básicos para entender o Kubernetes, eles são:

* **Docker e contêineres**

* **Gerenciamento de contêineres**

* **Arquitetura do Kubernetes e infraestrutura como código**

* **Elementos básicos do K8s**

* **Motivações e aplicações**

* **Por onde começar**

## Docker e contêineres

Para ter mais confiabilidade nas aplicações desenvolvidas deve haver ambientes isolados, em que possa ter estabilidade. O conceito de contêiner é criar um ambiente isolado dentro do computador para que a aplicação possa ficar independente, tendo bibliotecas nativas, chamadas do sistema operacional e variáveis estáveis. Para cria-lo é necessária uma ferramenta que nos permita montar esse ambiente. Essa ferramenta é o motor de contêiner. No nosso caso vamos utilizar o [Docker](https://www.docker.com/resources/what-container) como referência, sendo este o mais usado.

![Estrutura de uma máquina executando um motor de contêineres. Fonte: [https://rancher.com/playing-catch-docker-containers](https://rancher.com/playing-catch-docker-containers)](https://cdn-images-1.medium.com/max/2000/1*zpOcHOb83mbILCHH3-j9pg.png)*Estrutura de uma máquina executando um motor de contêineres. Fonte: [https://rancher.com/playing-catch-docker-containers](https://rancher.com/playing-catch-docker-containers)*

Contêineres são criados a partir de arquivos que declaram o que deve ser instalado nesse ambiente. Utilizando o Docker como motor de contêineres temos os arquivos de Dockerfile. Abaixo temos um exemplo simples utilizando o sistema operacional Ubuntu e instalando-se uma aplicação desenvolvida em Python:

    FROM ubuntu:18.04
    COPY . /app
    RUN make /app
    CMD python /app/app.py

A partir desse arquivo o motor de contêineres irá gerar o ambiente em que os comandos foram executados. Este será armazenado como uma imagem, que é um molde para se criar esse contexto. Esse ambiente pode ser reproduzido múltiplas vezes a partir da imagem. Podemos também enviar essa imagem para um armazenamento externo. Criado o ambiente e copiado o código para dentro, a aplicação pode ser executada em isolamento. Abaixo podemos ver a arquitetura do Docker, com a diferenciação entre contêineres e imagens.

![Arquitetura Docker separando contêineres de imagens. Fonte: [https://docs.docker.com/get-started/overview/](https://docs.docker.com/get-started/overview/)](https://cdn-images-1.medium.com/max/6304/1*9zj9m4XSk9Ls3UuhGkBRVA.png)*Arquitetura Docker separando contêineres de imagens. Fonte: [https://docs.docker.com/get-started/overview/](https://docs.docker.com/get-started/overview/)*

Ao utilizar contêineres podem também ocorrer problemas, como um erro na aplicação causando o encerramento da execução. Nesse caso deveríamos recriar o ambiente e novamente executar a aplicação. Isso não é realizado pelo motor de contêineres. Outra atividade que pode ser importante é manter uma quantidade pré-definida de contêineres rodando.

Quando se tem uma quantidade significativa de contêineres, esse trabalho manual pode crescer e onerar as equipes. É possível realizar uma automação em cima do motor para que tarefas recorrentes sejam abstraídas. Essa necessidade de gerenciamento de contêineres motivou a criação do Kubernetes pelo Google.

## Gerenciamento de contêineres e o Kubernetes

Para um gerenciamento efetivo de contêineres, diversas tarefas são necessárias, entre elas: checagem de saúde, deploy de uma nova versão sem queda e auto-recuperação em caso de quedas. Essas atividades não estão previstas e não são responsabilidades do motor de contêineres, então criaram-se soluções para isso, como o Kubernetes.

Kubernetes é um software distribuído responsável por manter e gerenciar contêineres. Em seu site oficial o software se declara como:
> Kubernetes (K8s) é um produto Open Source utilizado para automatizar a implantação, o dimensionamento e o gerenciamento de aplicativos em contêiner.

O software pode gerenciar múltiplos contêineres e organizá-los para executarem de forma distribuída.

## Arquitetura do Kubernetes

![Arquitetura de um cluster de Kubernetes.](https://cdn-images-1.medium.com/max/2468/1*EusH89TG3hFy4_TqZsP9Xg.png)*Arquitetura de um cluster de Kubernetes.*

Na figura acima podemos enxergar a arquitetura simplificada de uma implementação do Kubernetes. O software roda em diversas máquinas, também chamados de nós, e sua interação com o usuário é feita através de sua API e cliente de terminal. Por possuir diversas máquinas é que nos referimos normalmente de cluster, o conjunto de instâncias utilizadas. Possuímos 2 categorias de nós: master e worker. O master é responsável por receber as requisições em sua API e enviar comandos para os nós worker executarem mudanças.

A comunicação entre os usuários e o K8s é feita através do cliente de terminal processando arquivos de configuração do tipo [YAML](https://pt.wikipedia.org/wiki/YAML), formato de arquivo declarativo, e enviando-os para a API do cluster. É utilizado o conceito de Gerenciamento de Estado Desejado (“Desired State Management”) e de infraestrutura como código. O usuário descreve o estado final que quer que os contêineres estejam e o software é responsável por criar e manter aquele estado. Caso haja uma falha, o K8s vai se encarregar de recuperar o estado desejado declarado pelo usuário. Além disso, o K8s distribui a carga entre os nós que existem no cluster.

No arquivo de definição do estado possuímos informações vitais como: onde está localizada a imagem de contêiner a ser utilizada, quantas réplicas deverão ser mantidas e identificadores para agrupar contêineres. A imagem do contêiner a ser utilizada deve estar em uma fonte externa como, por exemplo, o serviço ECR da AWS, onde se registram imagens do Docker.

## Elementos básicos do K8s

O Kubernetes realiza o gerenciamento de contêineres através de abstrações, definidas nos arquivos de YAML. No próprio site há uma passagem que diz:
> Ele agrupa contêineres que compõem uma aplicação em unidades lógicas para facilitar o gerenciamento e a descoberta de serviço.

![Fonte:[https://subscription.packtpub.com/book/networking_and_servers/9781788997027/12/ch12lvl1sec116/kubernetes-deployment](https://subscription.packtpub.com/book/networking_and_servers/9781788997027/12/ch12lvl1sec116/kubernetes-deployment)](https://cdn-images-1.medium.com/max/2000/1*7lH67ONS-oRIEFHPvKV0yw.png)*Fonte:[https://subscription.packtpub.com/book/networking_and_servers/9781788997027/12/ch12lvl1sec116/kubernetes-deployment](https://subscription.packtpub.com/book/networking_and_servers/9781788997027/12/ch12lvl1sec116/kubernetes-deployment)*

São separadas as responsabilidades, entre os elementos lógicos do Kubernetes, de acordo com o [princípio de responsabilidade única](http://www.butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod). Acima há uma imagem ilustrando a hierarquia de componentes. Abaixo iremos entender cada elemento e sua responsabilidade.

## Pod

O Pod é o componente mais básico e onde fica declarado o contêiner. O Pod traz algumas garantias como tudo que é declarado dentro é executado na mesma máquina. É possível ter mais de um contêiner no mesmo Pod. A definição do contêiner possui um endereço onde o Pod pode baixar a imagem do contêiner, como o DockerHub ou repositórios privados como ECR da AWS.

## ReplicaSet

Esse elemento é responsável por manter a quantidade de réplicas de um contêiner no estado desejável, utilizando-se de um processo de auto-recuperação.

## Deployment

A responsabilidade desse componente é realizar atualizações, caso haja uma mudança. Se o contêiner for alterado, ele será responsável por criar um ReplicaSet com o contêiner diferente e esperar até que o estado desejado seja alcançado, para desligar o antigo ReplicaSet. Caso o novo ReplicaSet não esteja saudável, deverá ser feito o rollback, ou seja, o retorno para o estado anterior.

## Exemplo completo de YAML

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment
      labels:
        app: nginx
    spec: # ReplicaSet
      replicas: 3
      selector:
        matchLabels:
          app: my-app
      template: # Pod
        metadata:
          labels:
            app: my-app
        spec:
          containers:
          - name: my-app
            image: nginx:1.14.2
            ports:
            - containerPort: 80

## Comunicação externa através de Service e Ingress

Com os elementos acima é possível executar uma aplicação no Kubernetes, mas, além disso, muitas vezes queremos que essas aplicações possam ser encontradas e contactadas por requisições externas. Existem diversas formas de alcançar essa comunicação, vamos exemplificar através do uso de Service e Ingress.

![Fonte: [https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0](https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0)](https://cdn-images-1.medium.com/max/2800/1*It5QyZizAmpO_rvglbR1jg.png)*Fonte: [https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0](https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0)*

O elemento Service é uma abstração responsável por agrupar uma série de Pods, que estão no mesmo Deployment, por exemplo. Este provê uma interface única para que outros elementos, como Pods, possam se comunicar com um conjunto de Pods. Ele vai declarar um endereço único que pode ser usado para comunicação dentro do cluster.

Utilizamos Services para identificar Pods devido à natureza finita de um Pod, que pode surgir e ser deletado de acordo com a necessidade. Quando se utiliza um ReplicaSet, por exemplo, novos Pods podem ser criados para atingir um contingenciamento. O Service vai ser capaz de identificar novos Pods e encaminhar qualquer comunicação para eles, de acordo com a regra para identificação. Enquanto Pods podem ser criados e deletados a todo momento, a interface do Service permanece a mesma.

Já o Ingress é um elemento responsável por receber requisições externas ao cluster e encaminhar para serviços, de acordo com regras pré-estabelecidas. As regras podem ter vários critérios como: hostname utilizado, path da requisição e outros.

Exemplo de arquivos de definição de Service e Ingress:

    #### SERVICE

    apiVersion: v1
    kind: Service
    metadata:
      name: my-service
    spec:
      selector:
        app: my-app # Repare nesse seletor que mostra a tag app sendo utilizada
      ports:
        - protocol: TCP
          port: 80
          targetPort: 9376

    #### INGRESS

    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: test-ingress
      annotations:
        nginx.ingress.kubernetes.io/rewrite-target: /
    spec:
      rules:
      - http:
          paths:
          - path: /testpath
            pathType: Prefix
            backend:
              serviceName: my-service # Encaminhamento para o serviço
              servicePort: 80

## Motivação e aplicações

O Kubernetes exige coordenação e configuração complexa. É uma aplicação distribuída que exige um esforço inicial e apresenta uma complexidade de abstração para o uso. Dito isso, podemos analisar que para tirar proveito de tal software precisamos ter um uso vasto. É necessário ter em um horizonte a criação de múltiplos serviços para que o esforço inicial seja diluído.

Havendo tal necessidade para múltiplos serviços de alta disponibilidade, o uso do K8s traz muitos benefícios como: auto-recuperação, escalonamento automático, isolamento dos ambientes entre aplicações rodando na mesma máquina, etc.

O uso dessa tecnologia pode acelerar o processo de deploy, dando confiança para a equipe realizar entrega contínua.

Resumindo, considere o uso de Kubernetes quando:

* Há uma demanda crescente de novos serviços.

* Deseja-se otimizar o uso de recursos, muitas vezes compartilhando-os entre serviços, sem perda do isolamento.

* É necessário ter uma aceleração no processo de entrega com confiabilidade, tirando proveito de atualizações canary e auto-recuperação.

É claro que pode-se alcançar os mesmos benefícios do K8s com outras tecnologias. Já existem diversas ferramentas nos ambientes de nuvem que podem alcançar isso. Entretanto, o K8s oferece uma solução empacotada que já traz muitas configurações direto de fábrica. Diferencia-se também dessas tecnologias por ser open-source e agnóstico de plataforma de nuvem.

## Por onde começar

Entendido o modelo do K8s e sua aplicação, podemos partir para a prática. Existem diversos simuladores que são capazes de rodar o K8s em apenas uma máquina, simulando um ambiente real para aprendizado. O Minikube ([https://kubernetes.io/docs/setup/learning-environment/minikube/](https://kubernetes.io/docs/setup/learning-environment/minikube/)) é uma dessas alternativas.

Sugestão de passos para entender o K8s, na prática:

* Instalar o simulador Minikube

* Criar um Deployment a partir de uma imagem pronta definida no DockerHub

* Interagir com esse deployment realizado updates

* Construir e hospedar sua própria imagem, utilizando-a no deployment

* Criar um Service para comunicação entre pods

* Criar um Ingress para acesso externo

Essas são algumas ideias para começar, mas a explicação prática sai do escopo desse artigo. Agradeço a paciência da leitura e se tiver alguma dúvida ou sugestão, por favor não hesite em enviar nos comentários.
