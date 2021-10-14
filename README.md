Plano de Estudo
​

Semana 01
	Introdução
	
	Overview sobre container e Kubernetes
	  * Kubernetes Control Plane (master do kubernetes)
		  ****kube Api Server        -> CLI para adm do kubernetes
		  ***Kube Scheduler          -> mecanismos de agendador de tarefas de qualquer objeto
		  ***ETCD                    -> banco proprietário chave/valor
		  ***Kube Controller Manager -> gerencia o processamento de todos controlers do kubernetes 
		                               (replicaset, admission controller, controller customizado etc)
		  
	  * Kubernetes Node
	      ***kubelet           -> responsável por se comunicar com kubernetes control plane utilizando o kube api server e 
		                          monitorar/analisar o estado do nó em execução
		  ***kube proxy        -> responsável pela comunicação entre os nós
		  ***container runtime -> responsável por executar os containers (docker/podman/containerd/rkt/cril"https://cri-o.io/"),
		                          kubernetes usa uma interface onde todos esses containers runtime implementam 
								  
	  ** versão 1.20 do kubernetes o docker foi classificado como (descontinuado) como container runtime compatível com 
	     kubernetes - o docker nunca implementou o container runtime interface, para utilizar o docker existia um adaptador
		 (docker shurins) que fazia o meio de campo, projeto mantido pela equipe do kubernetes
		 https://kubernetes.io/docs/setup/production-environment/container-runtimes/
		 
	  ** padrão para criação de imagem e execução de container (open container initiative https://opencontainers.org/),
	   vários container runtime implementam este OCI, inclusive o docker, em resumo, uma imagem gerada pelo docker,
	   segue o contrato que os demais container runtime executam, então não muda nada com a retirada do docker como
	   container runtime do kubernetes
	  
	  
	  
	O que é a Cloud Native Computing Foundation ?
	 - criadora do docker 
	 
	Arquitetura do Kubernetes
	
	Instalação das Ferramentas do Curso
		Introdução
		Ferramentas exclusivas para Windows	  
			Chocolatey
			-> gerenciado de instalação -> https://chocolatey.org/
		GitBash
		Docker
		Instalação do VSCode
		Kubectl
		   -> responsável pela comunicação com o clueste do kubernetes, por ele que usamos para executar os comandos
		   
		Kubernetes no seu Ambiente Local
		   Chave SSH
		     -> gerando no windows por PuTTYgen: https://adrianorosa.com/blog/seguranca/como-criar-ssh-key-pair-windows.html
			 -> gerado via command: ssh-keygen -t rsa -b 4096

	Introdução
		Instalando o Multipass e MicroK8S
			https://multipass.run/docs/installing-on-windows
			https://github.com/canonical/multipass/releases
		
		Utilizando Multipass e MicroK8S - (COM BASE EM MÁQUINAS VIRTUAIS - VM)
		    *criar uma máquina:
			  multipass launch -n k8s -c 2 -m 2G -d 20G
			  
			*listar máquinas criadas
			  multipass list
			 
			*acessar terminao da máquina
			  multipass shell k8s
			  
			*instalar o microk8s na máquina
			  multipass exec k8s -- sudo snap install microk8s --classic --channel=1.18/stable
			  
			*dando permissão de super usuário para o aplicativo "microk8s" para o usuário "ubuntu" da máquina
			  multipass exec k8s -- sudo usermod -a -G microk8s ubuntu
			  
			*setar senha ao usuário ubuntu
			multipass exec k8s -- sudo passwd ubuntu
			
			*dar permissão ao usuário "ubuntu" para acessar o ".kube" que é a configuração do kubernetes
			  multipass exec k8s -- sudo chown -f -R ubuntu ~/.kube
			  
			*reiniciar a máquina
			  multipass restart k8s
			  
			*fazendo deploy da uma imagem nginx
			  multipass exec k8s -- /snap/bin/microk8s.kubectl create deployment nginx --image=nginx
			  
			*listando pods do kubernetes
			  multipass exec k8s -- /snap/bin/microk8s.kubectl get pods
			
			*imprime na tela as configurações de acesso do kubectl
			  multipass exec k8s -- /snap/bin/microk8s.kubectl config view --raw
			
			* copia o conteúdo do raw e colocar no arquivo config da pasta
			  C:\Users\marci\.kube
			 
			*deletar máquina criada
			  multipass delete k8s
	  
		Instalando o Kind (UTILIZANDO CONTAINERS EM VEZ DE MÁQUINAS VIRTUAIS)
			* url do kind:
				https://kind.sigs.k8s.io/
			
			* instalação usando chocolatey: 
				choco install kind
				https://community.chocolatey.org/packages/docker-desktop#install
				
		    * criando cluster com kind
			    kind create cluster
			
			* alternando de contextos quando existir mais de 1 configurado
			    https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
				
			    - kubectl config --kubeconfig=kind-kind view
			    - kubectl config --kubeconfig=kubernetes-admin@kubernetes view
		
		Utilizando Kind
		    * listas os nodes do cluster
			    kubectl get nodes
			
			* criando primeiro deployment
			    kubectl create deployment nginx --image=nginx
			
			* verificando pods criado pelo deployment
			    kubectl get pods
				
			* executando encaminhamento de porta do host para o pod
			    kubectl port-forward pod/nginx-6799fc88d8-nwpkc 8080:80
				
				acessando a http://localhost:8080/ terá acesso à página do nginx
				
		    * criando um cluster nomeado
			    kind create cluster --name primeiro-cluster-nomeado
				
			* verificando lista de clusters
				kind get clusters
			
			* deletando cluster				   
				kind delete cluster --name primeiro-cluster-nomeado
				
			* criando cluster com kind determinando objetos do kubernets
			    kind create cluster --config ./cluster-one-control-plane.yml --name mult-nodes
				
				exemplo do arquivo yaml:
				####################################
				kind: Cluster
				apiVersion: kind.x-k8s.io/v1alpha4
				nodes:
				- role: control-plane
				- role: worker
				- role: worker
				####################################
			
			* recuperando informações do cluester 
			    kubectl cluster-info --context kind-primeiro-cluster-nomeado
				
			* verificando os serviços rodando
			    kubectl get services
		
		Utilizando Kubernetes as a Service
		    * AKS -> Azure Kubernetes Service (Microsoft)
			
			* Instalando CLI do azure
				* documentação MS -> https://docs.microsoft.com/pt-br/cli/azure/install-azure-cli-windows?tabs=azure-cli
				* CLI corrente    -> https://aka.ms/installazurecliwindows
				
				* recuperando as credenciais do AKS depois de configurado
				--> az aks get-credentials --resource-group kubedev-rc-group --name kubedev-cluster --overwrite-existing
				
				* mudar o contexto novo como principal
				kubectl config --kubeconfig=kubedev-cluster view
				kubectl cluster-info --context kubedev-cluster
			
						
	
	Introdução
		Azure CLI
			* rodar comando para verificar instalação:
			    az                -> irá apresentar as funções disponíveis
				az --version      -> irá apresentar a versão da CLI instalada
				az login          -> executa o login no azure para usar os recursos da Cloud da Azure
				az resource list  -> lista recursos utilizados na cloud  (ativei minha conta gratuíta hoje 28/08/2021)
				      site de gerenciamento de recurso      -> https://portal.azure.com/?quickstart=True#blade/Microsoft_Azure_Resources/QuickstartCenterBlade
					  site para ativação de outros recursos -> https://my.visualstudio.com/Benefits?wt.mc_id=o~msft~profile~devprogram_attach&workflowid=devprogram&mkt=pt-br
		
		Azure Kubernetes Service
			* criar recurso/cluste na azure:
			    https://portal.azure.com/#blade/HubsExtension/DeploymentDetailsBlade/overview/id/%2Fsubscriptions%2Fb7e10cb1-cc93-411a-b944-51a1832663ed%2FresourceGroups%2Frec-kubedev%2Fproviders%2FMicrosoft.Resources%2Fdeployments%2Fmicrosoft.aks-20210828131533/packageId/microsoft.aks/packageIconUri/https%3A%2F%2Fcatalogartifact.azureedge.net%2Fpublicartifacts%2Fmicrosoft.aks-1.2.0%2FMedium.png/primaryResourceId/%2Fsubscriptions%2Fb7e10cb1-cc93-411a-b944-51a1832663ed%2Fresourcegroups%2Frec-kubedev%2Fproviders%2FMicrosoft.ContainerService%2FmanagedClusters%2Fcluster-kubedev/provisioningHash/postCreateId/createBlade/%7B%22bladeName%22%3A%22CreateAksBlade%22%2C%22extension%22%3A%22Microsoft_Azure_ContainerService%22%7D
			
			* depois do recurso criado, recuperar as credenciais do recurso (irá editar o arquivo config da pasta .kube da máquina)
			    az aks get-credentials --resource-group rec-kubedev --name cluster-kubedev --overwrite-existing

			
		Scaleway
		    *endereço: https://console.scaleway.com/organization


		
	Cluster Kubernetes com Kubeadm
		
		Instalação com Kubeadm (MASTER)
		  --> https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
			01) trocar para usu root       -> sudo su -
			02) atualiza a máquina         -> apt-get update -y && apt-get upgrade -y
			03) necessário hab containerd  ->  https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd
			
# Create the .conf file to load the modules at bootup
cat <<EOF | sudo tee /etc/modules-load.d/crio.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# Set up required sysctl params, these persist across reboots.
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

sudo sysctl --system
sudo apt-get install -y containerd

sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo systemctl restart containerd

			04) importar a chave k8s       -> curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
			05) add repo k8s               -> echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list 
			06) atualiza a máquina         -> apt-get update -y && apt-get upgrade  -y
			07) add elementros cluster     -> apt-get install -y kubelet kubeadm kubectl
			
			08) inicializando master       -> kubeadm init --apiserver-cert-extra-sans=52.188.107.172
			    depois da instalação o kubeadm gera os 
				comando abaixo para registrar o config 
				de acesso:
										   -> mkdir -p $HOME/.kube
										   -> sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
										   -> sudo chown $(id -u):$(id -g) $HOME/.kube/config
			
			09) instalar o CNI(gerenc rede)-> kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
			

		Instalação com Kubeadm (WORKERS)					                      
			01) repetir do passo 01 até o 07
			02) rodar no master para gerar token -> kubeadm token create --print-join-command
			    resultado                        -> kubeadm join 10.0.0.4:6443 --token 7p0ysh.wsemudc7tgswozt4 --discovery-token-ca-cert-hash sha256:207e2fc20abd09413ab61077faf661b7a2e76b29f7488c69684ca8e7c77c986a
			03) rodar para testar o deploy       -> kubectl create deployment nginx --image=nginx
			04) listar containers em execução    -> ctr containers ls
		
		
================================================================================================================================
		
	Introdução ao Docker
	   -> docker client   (client)     (docker build | docker pull | docker run)
	   -> docker daemon   (docker_host)(container | imagens)
	   -> docker register (registry)   (redis | nginx | etc)
		
	Introdução ao Docker e comandos para containers
	
	Criando imagens Docker com Docker commit
	
	Criando imagens Docker com Dockerfile
	   01) verifica as camadas da imagem    -> docker image history NOME_DA_IMAGEM
	   
	   02) para gerar outra versão de image -> docker tag marcioalmeidarosa/image:1.0 marcioalmeidarosa/image:latest
	
	Conhecendo Docker Registry e utilizando o Docker Hub
	
	Dicas de otimização
	
	MultistageBuild
	
	Azure Container Registry
	   01) https://kubedev.club.hotmart.com/lesson/M7G2Mo5Pew/azure-container-registry
	   
	   Rodando um compose com arquivo de environment -> usar ${TAG} -> para usar a variárvel no arquivo de componse
	   docker-compose --env-file ./.env up -d
	   
	Exercícios
		* Questão 01 - Execute os comandos para criar os 4 bancos de dados listados com containers, e use como se tivesse instalado eles localmente na sua máquina (Não esquece de garantir que não vai perder os dados caso o container seja excluido).

			- MongoDB
			  - imagem : https://hub.docker.com/_/mongo
			  - comando: docker run --name db-mongo -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=Senha@123! -v mongo-db-volume:/data/db -p 27017:27017 --network=nw-mongo-db -d mongo:5.0.1
			  
			- MariaDB
			  - imagem : https://hub.docker.com/_/mariadb
			  - comando: docker run --name db-mariadb -e MARIADB_ROOT_PASSWORD=Senha@123! -v maria-db-volume:/etc/mysql/conf.d -p 3306:3306 --network=nw-maria-db -d mariadb:10.6
			  
			- PostgreSQL
			  - imagem : https://hub.docker.com/_/postgres
			  - comando: docker run --name db-postgres -e POSTGRES_USER=root -e POSTGRES_PASSWORD=Senha@123! -e PGDATA=/var/lib/postgresql/data/pgdata -v postgre-volume:/var/lib/postgresql/data --network=nw-postgres -p 5432:5432 -d postgres:13.4-buster
			  
			- Redis
			  - imagem : https://hub.docker.com/_/redis  |  https://redis.io/topics/config  |  https://redis.io/topics/lru-cache
			  - comando: docker run --name cache-redis -v cache-redis-volume:/data -v cache-redis-cfg-volume:/usr/local/etc/redis --network=nw-redis-cache -p 6379:6379 -d redis:6.2.5-alpine redis-server --requirepass "Senha@123!"
			  
			  
		* Questão 02 - Certo, você conseguiu criar 4 bancos na sua máquina utilizando containers. Mas tem uma coisa, não adianta só conectar a aplicação no banco quando se está desenvolvendo, é preciso também acessar o banco, executar comandos e consultar a base. Então vamos fazer isso da forma KubeDev de ser, com containers !!! Cada banco de dados tem uma ferramenta administrativa com interface web que você pode usar.
			- MongoDB
			  - cliente: https://hub.docker.com/_/mongo-express  |  https://github.com/mongo-express/mongo-express
			  - comando: docker run --network=nw-mongo-db -e ME_CONFIG_MONGODB_SERVER=db-mongo -e ME_CONFIG_MONGODB_ADMINUSERNAME=root -e ME_CONFIG_MONGODB_ADMINPASSWORD=Senha%40123! -p 8081:8081 -d mongo-express:1.0.0-alpha
			  
			- MariaDB
			  - cliente: https://hub.docker.com/_/phpmyadmin  |  https://www.phpmyadmin.net/  |  https://github.com/phpmyadmin/docker
			  - comando: docker run --name myadmin -e MYSQL_ROOT_PASSWORD=Senha@123! -e PMA_HOST=db-mariadb -v phpmyadm-volume:/etc/phpmyadmin/config.user.inc.php --network=nw-maria-db -p 8080:80 -d phpmyadmin
			  
			- PostgreSQL
			  - cliente: https://hub.docker.com/r/dpage/pgadmin4/  |  https://www.pgadmin.org/download/pgadmin-4-container/  |  https://www.pgadmin.org/  |  https://www.enterprisedb.com/postgres-tutorials/connecting-postgresql-using-psql-and-pgadmin
			  - comando: docker run --name pgadmin -e PGADMIN_DEFAULT_EMAIL=marcio.almeida.rosa@gmail.com -e PGADMIN_DEFAULT_PASSWORD=Senha@123! -e PGADMIN_CONFIG_CONSOLE_LOG_LEVEL=10 --network=nw-postgres -p 8082:80 -d dpage/pgadmin4:5.6
			  
			- Redis
			  - cliente: https://hub.docker.com/r/rediscommander/redis-commander
			  - comando: docker run --name redis-commander -e HTTP_USER=root -e HTTP_PASSWORD=Senha@123! -e REDIS_HOST=cache-redis -e REDIS_PORT=6379 -e REDIS_PASSWORD=Senha@123! --network=nw-redis-cache -p 8083:8081 -d rediscommander/redis-commander
			  
			  
		* Questão 03 - Um dos fundamentos chave pra se trabalhar com container é a criação de imagens Docker. E criar uma imagem Docker pra cada aplicação sempre muda dependendo de como ela foi desenvolvida. Um cliente entrou em contato e expos o principal problema para a migração pra ambiente baseado em containers. Então agora eu tenho aqui algumas aplicações que precisam ser executadas em containers mas eu só tenho o código fonte delas, chegou a hora de você mostrar seu talento e executar essas aplicações em containers Docker e deixar acessível na sua máquina local.(Pesquise e entenda como cada plataforma é utilizada antes de começar a criar a imagem)

			- App Node
			  - repositório   : https://github.com/KubeDev/conversao-temperatura
			  - fork          : https://github.com/MarcioDeAlmeidaRosa/conversao-temperatura
			  - referências   : https://vsupalov.com/docker-env-vars/  | https://nodejs.dev/learn/how-to-read-environment-variables-from-nodejs
			  - comando build : docker build --build-arg ARG_NODE_PORT=8080 -t marcioalmeidarosa/conversao-temperatura:1.0 .
			  - comando run   : docker run --name=api_conversao-temperatura_node --env-file ./.env -p 8080:8070 -d marcioalmeidarosa/conversao-temperatura:1.0
			  			  
			- App Python
			  - repositório   : https://github.com/KubeDev/conversao-distancia
			  - fork          : https://github.com/MarcioDeAlmeidaRosa/conversao-distancia
			  - referências   : https://www.howtogeek.com/225487/what-is-the-difference-between-127.0.0.1-and-0.0.0.0/#:~:text=0.0%3F-,127.0.,particular%20address'%20place%20holder).
			  - com instal pkg: pip install -r requirements.txt
			  - executar app  : python app.py
			  - comando build : docker build -t marcioalmeidarosa/conversao-distancia:1.0 .
			  - comando run   : docker run --name=api_conversao-distancia_python -p 8099:5000 -d marcioalmeidarosa/conversao-distancia:1.0
			  
			- App C#
			  - repositório   : https://github.com/KubeDev/conversao-peso
			  - fork          : https://github.com/MarcioDeAlmeidaRosa/conversao-peso
			  - sdk           : https://hub.docker.com/_/microsoft-dotnet-sdk
			  - comando build : docker build -t marcioalmeidarosa/conversao-peso:1.0 .
			  - comando run   : docker run -it marcioalmeidarosa/conversao-peso:1.0 /bin/bash
			  
			  
		* Questão 04 - Agora que você já afiou o seu conhecimento sobre criação de imagens Docker, tá na hora de fazer o deploy de uma aplicação 100% em containers Docker. A aplicação está no GitHub do KubeDev e um detalhe MUITO importante, a aplicação precisa ser toda criada com apenas uma linha de comando.
			  - repositório   : https://github.com/KubeDev/rotten-potatoes
			  - fork          : https://github.com/MarcioDeAlmeidaRosa/rotten-potatoes
			  - com instal pkg: pip install -r requirements.txt
			  - executar app  : python app.py
			  - comando build : docker build -t marcioalmeidarosa/rotten-potatoes:1.0 .
			  - comando run   : docker run -it -p 8050:5000 -d marcioalmeidarosa/rotten-potatoes:1.0
			  - compose up    : docker-compose -f .\src\docker-compose.yaml up -d

			  
		* Questão 05 - Chegou um cliente pra você que possui todas as suas aplicações em data centers e a gestão dessas aplicações está cada vez mais complexa então pra iniciar um plano de gestão unificada e migração pra um ambiente cloud, as aplicações serão migradas pra containers. E hoje você precisa iniciar esse processo com um projeto piloto, o portal de conteúdos da empresa construido em Wordpress. Então hoje sua missão é criar esse ambiente wordpress pronto para a equipe de publicidade começar a popular.
			  - repositório   : https://github.com/MarcioDeAlmeidaRosa/portal-empresa		


		* Questão 06 - Agora vamos aumentar mais a complexidade das coisas, chegou a hora de executar uma aplicação baseada em arquitetura de microsserviços. Essa aplicação é formada por 3 repositórios:
			- Aplicação Web
              - repositório   : https://github.com/KubeDev/rotten-potatoes-ms
			  - fork          : https://github.com/MarcioDeAlmeidaRosa/rotten-potatoes-ms
			  
			- Microsservico de Filmes
              - repositório   : https://github.com/KubeDev/movie
			  - fork          : https://github.com/MarcioDeAlmeidaRosa/movie
			  
			- Microsserviço de Avaliação
              - repositório   : https://github.com/KubeDev/review
			  - fork          : https://github.com/MarcioDeAlmeidaRosa/review
			  
			  - repositório   : https://github.com/MarcioDeAlmeidaRosa/projeto-microservice-docker
		
		* Questão 07 - Agora que você concluiu, tão importante quanto executar, é documentar. Então crie anotações sobre cada questão, detalhe as tomadas de decisão e processo de construção. E pra ficar melhor ainda, crie posts no Linkedin sobre o processo e não se esquece de me marcar e colocar a nossa #rumoaelite !!!!
			  
	Seu container na nuvem sem Kubernetes
	  https://teste-chamada.azurewebsites.net/?teste=90
	
	Azure App Services
	  	  * Objetos mínimos para ter uma aplicação rodando em Kubernetes
		  * Pod
		   -- Labels
		   -- Selectors
		  * ReplicaSet
		   -- Labels
		   -- Selectors
		  * Deployment
		   -- Labels
		   -- Selectors
		  * Service
		   -- Labels
		   -- Selectors



Semana 02
	Os elementos básicos de um deploy no Kubernetes
	
		* Introdução
			** Pod
			  -> Responsável por gerenciar os containers
			  -> menor objeto do objeto do cluste kubernetes, 
			  -> dentro do pod são rodados os containers, 
			  -> é permitido mais de um no Pod, porém não é recomandado
			  -> Quando escala a aplicação, o Pod é um elemento que é afetado, se todos seus 
			     container estão dentro do Pod, eles estão compartilhando a mesma rede e 
				 filesystem e todos eles são escalados, não conseguindo escalar somente o necessário.
			  -> quando você vem vários container dentro do mesmo pod é o pattern chamado 
			     SideCar ( seu container de negócio e outros container com rotinas não funcionais 
				 tais como coleta de log, redirecionamento de rota, coleta de métricas, qualquer 
				 container que não é de negócio da aplicação - usados em ferramentas de Service Mesh)
			  -> quando pod é criado recebe IP e filesystem como se "fosse uma máquina"
			  -> nunca usar somente o pod para fazer o deploy puro da aplicação chamado de 
			     "naked pod", por que o pod sozinho não garante escalabilidade e nem resiliência.
			  -> caso o pod seja deletado ou morra por algum motivo, um deploy feito somente com o 
			     objeto pod não tem a caracteristica de subir um novo pod para continuar a execução
			  
			
			** Labels e Selectors
			  -> Labels     -> elemento chave/valor que são armazenados no metadata
			                -> utilizado para organização dos objetos
		      -> Selectors  -> usado como recurso para seleção dos objetos
			                -> usado em várias partes do kubernetes
			
			** ReplicaSet
			  -> Responsável por gerenciar os Pods
			  -> Garante a replicação dos pods caso algum problema ocorra com o pod
			  -> Garante a quantidade de replica e estado do pod
			  -> veio para substituir o replicationControler nas versões antigas
			  -> ReplicaSet não gerencia a mudança de versão, atualização de imagem ou especificação no manifesto para replicaset, para fazer isso, teria que deletar o pod manualmente.
			  -> Responsável por garantir a escalabilidade e resiliência dos serviços
			
			** Deployment
			  -> Responsável por gerenciar o ReplicaSet
			  -> No manifesto não há declaração do replicaset, este objeto é criado intrincicamente ao fazer o apply do objeto deployment
			
	Entendendo Services
		* Introdução
		  -> Responsável por fazer o service discovery dos serviços
		  -> Responsável por fazer o balanceamento dos pods
		
		* ClusterIP
		  -> Responsável por gerar conexão entre pods dentro os clusters
		
		* NodePort
		  -> Permite o acesso externo ao cluster kubernetes por porta entre as portas 30000 até 32767
		
		* LoadBalancer
		  -> Permite o acesso externo ao cluster kubernetes pelo ip que é associado ao external-ip do service
		
		* ExternalName
	
	EndPoint	
		* O que são os Endpoints?
		  -> Toda vez que é criado um service utilizando os selectors para vincular aos pods, é 
		  criado um objeto endopoints, onde os pods se registram nesta coleção para permitirem
		  receber requisições no momento do processamento do workload.
		  -> Coloção com todos os pods vinculados ao service que originou o endpoint.
		
	Namespace
	    -> permete que você crie vários clustes kubernetes lógicos, dentro de um cluster físico
		   kubernetes. (Vários miniclustes kubernetes dentro de um cluster)
		-> permite dar acesso ao usuário por cluster
		-> permite limitar consumo de recurso por cluster por namespaces
		
		
		* Utilizando Namespace
		
		* Comunicação entre Namespaces
		  -> usando diretamente o ip do node (este não roda separado de namespace) ou criar 
		     serviços do tipo ExternalName apontando para o dns completo do serviço no namespace 
			 onde ele é executado
		
		* O que é separado por namespaces e o que não é ?
		  -> nem todo objeto do kubernetes é separado por namespace, conseguimos saber pelo filtro:
		  kubectl api-resources --namespaced=true
		  kubectl api-resources --namespaced=false
	
	
	Deploy de uma aplicação: Do GitHub ao Kubernetes
		* Subindo a aplicação
		  Aplicando mais configurações nos manifestos de deployment e service

		* Exercícios
		  -> https://github.com/MarcioDeAlmeidaRosa/aprendendo-kubernetes/tree/master/comunicacao-diferentes-namespace

	Gerenciamento de Configuração
		* ConfigMap e Secret
		  -> Elementos para auxiliar a separação de variáveis e seus valores dos manifestos para criação de um deployment
		  -> Reaproveitamento dos arquivos de manifestos em diferentes ambientes sem ter que ficar editando as particularidades conforme ambiente
		    -> ConfigMap: Controle por chave/valor com seu conteúdo público, qualquer pessoa consegue executar um describe e saber seus valores.
			-> Secret: Controle por chave/valor, porém seu valor é armazenado em base64, garantidno assim um pouco de segurança, porém caso seja recuperado o conteúdo, nada impede de converter para string novamente para ser revelado seu valor.

		* ConfigMap e Secrets com linha de comando
		  -> Bastante utilizando, pois é possível criar ConfigMap/Secrets com base em arquivos 

			-> ConfigMap
			  * kubectl create configmap literal-configmap --from-literal=Mongo__Host=mongo-service
			  * kubectl create configmap file-configmap --from-file=./configmap-secret-linha-de-comando/prometheus.yaml

			-> Secrets
			  * kubectl create secret generic literal-secret --from-literal=MONGO_PWD=mongopassword
			  * kubectl create secret generic file-secret --from-file=./configmap-secret-linha-de-comando/password.txt

		* Exercícios


Semana 03
	Trabalhando com repositórios privados
		* Utilizando o Azure Container Registry como repositório privado no Kubernetes
		  -> permite utilizar outros register privados ou não para executar container 

		* Exercícios

	Self Healing em seus Pods
		* Startup, Readiness, Liveness Probes
		  -> Recursos para evitar downtime na aplicação

		  -> Startup Probes: utilizado em container que demoram mais para inicializar, evita do "Liveness" executar no momento de subida da aplicação. Importante ter esta configuração para não gerar o problema onde o kubernetes ao executar "Liveness" e receber que a aplicação não esta OK, reestartar o container, causando assim a situação deste container nunca subir.

		  -> Readiness Probes: verifica se o container esta pronto para receber requisições, não necessariamente o container não está saudável, remove o bind do service "via endpoints" até retornar que esta pronto para chamada.
		  Se api estiver no gargalo dela, aqui que informa que ela não esta pronta, desta forma o conteiner continua ativo processando a carga de trabalho que já esta sendo processada nele, e o kubernetes aguarda a aplicação dizer que esta pronta para novas requisições novamente.

		  -> Liveness Probes: verificar sempre a saúde do container -> endpoint de healthcheck -> reestarta quando não saudável.


	Gerenciamento de Recursos da sua Aplicação e dos Ambientes
		* Resource Request e Resource Limits
		  -> ajuda a proteger nossa aplicação e o cluster kubernetes, pois nos possibilita utilizar 
		     consumo de recurso mínimo (Resource Request) e máximo para a aplicação (Resource Limits)

		* Horizontal Pod Autoscaler
		  -> recurso para escalar um alvo (deployment entre outros) de acordo com o critério informa

		* Kubernetes Quality of Service (QoS)
		  -> determina a prioridade para o kubernetes na hora de eliminar pods em execução para garantir a estabilidade do ambiente.
		  -> temos as seguintes prioridade (ordem de prioridade) e como configura-las:
		    -> Guaranteed: 
			               * de maior prioridade para o kubernetes, em caso de necessidade, pods configurado com este QoS serão os últimos
			                 a ser eliminados.
						   * para configurar um pod neste QoS, basta configurar seu (Resource Request e Resource Limits) com os mesmos valores.
			-> Burstable:
			               * de prioridade intermediária para o kubernetes, em caso de necessidade, pods configurado com este QoS serão de segunda prioridade para ser eliminados
						   * para configurar um pod neste QoS, basta configurar seu (Resource Request e Resource Limits) com a valores de request inferiores aos valores de limite.
			-> Best-Effort
			               * de menor prioridade para o kubernetes, em caso de necessidade, pods configurado com este QoS serão os primeiros a ser eliminados.
						   * para configurar um pod neste QoS, basta não configurar seu (Resource Request e Resource Limits).

			Materia para auxilar entendimento:
			https://medium.com/google-cloud/quality-of-service-class-qos-in-kubernetes-bb76a89eb2c6#:~:text=For%20each%20resource%2C%20Kubernetes%20divide,in%20decreasing%20order%20of%20priority.

		* Resources na nossa Aplicação
		  -> conforme configuramos os recursos em nossos manifestos e aplicamos no ambiente, o recurso configurado é reservado na máquina e quando a somatória de todos os recursos reservados de todos os manifestos aplicado no kubernetes atinge o limite físico da máquina, os containers que não foram iniciados ficam com o status de pendente, pois não há recurso para ser fornecido aos novos containers.

		* LimitRange
		  -> Configuração para validar o manifesto antes de aplicar no cluster Kubernetes, define critérios de aceitação de resource request/limit. Permite também configurar recurso default caso não tenha configuração no manifesto sendo aplicado no cluster.
		  -> Só afeta validações executadas depois da sua aplicação, não valida apply anteriores a aplicação deste recurso.

		* Resource Quota
		  -> permite configuração máxima de recurso por namespace (ambiente), não permitindo assim roubo de recurso de um ambiente do outro

	O Ciclo de Vida de um Pod

		* Introdução

		  Estados de um pod: (https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/)
		    
			-> Pending   -> O pod foi aceito pelo cluster do Kubernetes, mas um ou mais dos contêineres não foram configurados e preparados para execução. Isso inclui o tempo que um pod gasta esperando para ser agendado, bem como o tempo gasto no download de imagens de contêiner pela rede.

		    -> Running   -> O pod foi vinculado a um nó e todos os contêineres foram criados. Pelo menos um contêiner ainda está em execução ou está em processo de inicialização ou reinicialização.

		    -> Succeeded -> Todos os contêineres no pod foram encerrados com sucesso e não serão reiniciados.

		    -> Failed    -> Todos os contêineres no pod foram encerrados e pelo menos um contêiner encerrou com falha. Ou seja, o contêiner saiu com status diferente de zero ou foi encerrado pelo sistema.

		    -> Unknown   -> Por algum motivo, o estado do pod não pôde ser obtido. Essa fase normalmente ocorre devido a um erro na comunicação com o nó em que o pod deve estar em execução.

		  Estado de um container
		    -> Waiting    -> Se um contêiner não estiver no estado Running ou Terminated, ele está Waiting. Um contêiner no estado Waiting ainda está executando as operações necessárias para concluir a inicialização: por exemplo, puxar a imagem do contêiner de um registro de imagem do contêiner ou aplicar Segredo dados. Quando você usa kubectl para consultar um pod com um contêiner ou seja Waiting, também vê um campo Motivo para resumir por que o contêiner está nesse estado.

		    -> Running    -> O status Running indica que um contêiner está executando sem problemas. Se houver um hook postStart configurado, ele já foi executado e finalizado. Ao consultar kubectl um pod com um contêiner, ou seja Running, você também vê informações sobre quando o contêiner entrou no estado Running.

		    -> Terminated -> Um contêiner no estado Terminated começou a ser executado e foi executado até a conclusão ou falhou por algum motivo. Ao consultar kubectl um pod com um contêiner, isto é Terminated, você vê um motivo, um código de saída e o horário de início e término do período de execução desse contêiner.

			Se um contêiner tiver um hook preStop configurado, ele será executado antes de o contêiner entrar no estado Terminated.

		  Todos esses estados é possível ver com o kubectl describe pod


		* Signal SIGTERM e Signal SIGKILL

		  -> Eventos de comandos disparados pelo sistema operacional/orquestrador para encerrar o processo/pod. (https://cloud.google.com/blog/products/containers-kubernetes/kubernetes-best-practices-terminating-with-grace)

		    -> Signal SIGTERM (kill / kill all): avisa o container para que ele se prepare para ser encerrado, o tempo padrão entre Signal SIGTERM e Signal SIGKILL é de 30 segundos, isso pode ser configurado na sua aplicação ("terminationGracePeriodSeconds" configuração no pod).

			-> Signal SIGKILL (kill -9): termina imediatamente a execução de um pod/container em execução

			-> Linguagens de programação possuem eventos para tratar o Signal SIGTERM e Signal SIGKILL

		* Post Start e Pré Stop hooks

		  -> Eventos para melhorar o tratamento do ciclo de vida de um container.

		    -> Post Start hook: disparado em paralelo a inicialização do container, é preciso um código de execução de sucesso para sair do estado Waiting, ou se não, irá encerrar o container por erro.
			 -> Duas formas de disparar este evento:
			   -> comando exec
			   -> http get

			-> Stop hooks hook: disparado no momento antes do (Signal SIGTERM), pode usar ele caso a linguagem não tenta tratamento para o evento (Signal SIGTERM).
			 -> Duas formas de disparar este evento:
			   -> comando exec
			   -> http get

		* Init Container
		    -> Permite adicionar execução de outros container (somente) na inicialização do pod, todos devem ter sua conclusão com suceosso para que o pod seja iniciado com sucesso, usado para fazer um pré-processamento/verificação necessário antes do início do Pod principal.
		      -> Duas formas de disparar este evento:
			   -> comando exec
			   -> http get

		Lembrete: o nome completo de um service -> mongo-service  .default    .svc.cluster.local
		                                           nome serviço | namespace | sulfixo de serviço


	Lidando com volumes no Kubernetes
		* Introdução: (https://www.redhat.com/pt-br/topics/cloud-native-apps/stateful-vs-stateless)
		    -> (Aplicações Stateful) os volumes são usado em situações onde não temos pods efêmeros "Eles são executados com base no contexto das transações anteriores, por isso o stado precisa ser mantido.".
			-> (Aplicações Stateless) não há necessidade de volumes, onde temos pod efêmeros "Se a transação for interrompida ou encerrada sem querer, você precisará começar outra sem afetar o comportamento da aplicação".

			-> Objetos para permitir o uso do volume:
			  -> Pod -> PersistentvolumeClaim -> Persistentvolume -> Volumes (nfs/host path, etc...)

			-> É possível trabalhar com volumes no kubernetes de 2 formas
			  -> Stática: administrador do cluste kubernetes cria o Persistentvolume para um volume específico.
			     -> Pod -> PersistentvolumeClaim -> Persistentvolume -> Volumes (nfs/host path, etc...)
			  -> Dinâmica: algum serviço de alocação dinâmica de storage (StorageClass).
			     -> Pod -> PersistentvolumeClaim -> Storage Class -> Persistentvolume -> Volumes (nfs/host path, etc...)

		* Criando um Volume HostPath

		* Utilizando volume do seu provedor de nuvem

	Outros modos de deploy
	StatefulSet
	DaemonSet
	Job e CronJob


Semana 04
	Gerenciando a distribuição dos Pods
		* Introdução
		* Node Selector
		* Node Affinity
		* Pod Affinity e Pod Antiaffinity
		* Taint e Tolerations

	Falando sobre Segurança
		* Introdução
		* WaveNet no Kind
		* NetworkPolicy
		* ServiceAccount e RBAC
	
	Expondo a sua aplicação com Ingress Controller
		* Introduçao
		* Instalando o Ingress Controller
		* Configurando Host Localmente
		* Ingress
	
	Helm
		* Introdução
		* Instalação
		* Manipulando repositórios
		* Instalando aplicações
		* Primeiro Helm Chart
		* Estrutura If/Else
		* Estrutura de Loop
		* Named Templates
		* Dependência de Charts
		* Notes


Semana 05
	Entendendo alguns padrões
		* Introdução
		* SideCar
		* Adapter
		* Ambassador
		* Controllers
		* Operators
	
	Estratégias de Deploy
		* Introdução
		* Recreate
		* Ramped
		* Blue / Green
		* Canary
	
	Prometheus e Grafana
	Introdução
	Arquitetura
	Instalação com Docker
	Consultas com PromQL
	Introdução ao Grafana
	Instalando o Grafana
	Criando Dashboard
	Importando Dashboards
	Introdução ao AlertManager
	Configuração dos Alerts Rules
	Configurando e Instalando o AlertManager
	Executando a Aplicação


Semana 06
	Traefik
	Introdução
	Instalação
	Criando a Aplicação de Exemplo
	Configurando Rota
	Middleware
	Dashboard
	Cert-Manager e Let's Encrypt
	Introdução
	Instalação
	Iniciando a Aplicação de Exemplo
	Criando os Issuers de Homologação e Produção
	Criando o Certificado e Configurando as Rotas
	Criação de uma pipeline CI/CD
	Introdução
	Instalação do Jenkins
	Instalando os plugins
	Configurando as Credenciais
	Conectando ao Kubernetes
	Preparando o repositório Git
	Criando a Pipeline
	Jenkinsfile

Avalie este conteúdo:

    
