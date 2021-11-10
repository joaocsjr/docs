# Notas do treinamento Openshift




## Anatomia de um arquivo de manifesto

*Cada arquivo de manifesto tem esses 4 componentes*

- **apiVersion:** _versão da api que sera usada de acordo com as features necessarias para app_

- **kind:**  _especifica qual o tipo de resource que esta sendo declarado_

- **metadata:**  _especifica o nome dos componentes, labels, namespace_

- **spec:** _especifica a config da app, quais são os containers as images que serao usadas_


## YAML Basico
- Use 2 espaços, não use TAB
- Use algum editor de texto que suporte YAML
- Para setar o VIM como editor padrão, Use o comando:
``` bash
EDITOR=/usr/bin/vim 
```

## Comandos basicos

```zsh 
# pega status do projeto
oc status 

# verifica info dos **pods** em formato yaml
oc get **pods** [YOUR_APP_PODNAME] -o yaml

# filtrando saida do comando get **pods**
oc get **pods** --field-selector status.phase=Running

# verificando infos do replicationcontroller
oc get rc [RC-NAME] -o yaml

# verifica infos do deploymenetcontroller
oc get dc cakephp-mysql-example -o yaml

```


# Exemplos

## Deployment 

```yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-deployment
spec:
  selector:
    matchLabels:
      app: httpd
  replicas: 2
  template:
    metadata:
      labels:
        app: httpd
    spec:
      containers:
      - name: httpd
        image: image-registry.openshift-image-registry.svc:5000/openshift/httpd
        ports:
        - containerPort: 8080
```
## **service**

```yaml
apiVersion: v1
kind: **service**
metadata:
  name: httpd-deployment
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    app: httpd
  type: ClusterIP
```

## Route
```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: httpd-deployment
spec:
  port:
    targetPort: 8080
  to:
    kind: **service**
    name: httpd-deployment
```







--------------



# Conceitos Basicos


- O código do aplicativo é executado em contêineres. Os **pods** são compostos por um ou mais contêineres. Normalmente, você tem um contêiner por pod. Você pode ter mais de um contêiner em um Pod se quiser que eles compartilhem a rede e o espaço de armazenamento - por exemplo, para ter acesso ao mesmo volume. Cada pod possui um endereço IP. Normalmente, os contêineres não possuem endereços IP individuais.

- Os **pods** não são persistentes. Eles podem iniciar, parar, travar e reiniciar. Cada vez que um pod é reiniciado, um novo endereço IP é atribuído a ele. Por esse motivo, você não pode confiar que o endereço IP do pod permaneça o mesmo.

- Um pod pode ter labels, e esses labels são aplicados a todos os **pods** durante esta implantação específica. Os labels podem ser usados ​​para identificar e selecionar **pods**. Os labels são pares de valores-chave. Exemplos de labels incluem app: web, class: frontend e zone: east.

- Os **pods** podem falhar. Quando eles travam, geralmente você deseja que sejam reiniciados automaticamente. O objeto **ReplicaSet** (ou ReplicationController) é responsável por fazer isso. Ele pode assistir a um único pod ou a um conjunto de **pods**. Ele os seleciona por seletores e labels nos **pods**. Sua única tarefa é garantir que o número solicitado de **pods** esteja ativo e em execução. Por exemplo, você pode ter um **ReplicaSet** chamado rs-web que especifica app: web como um seletor, e você tem **pods** com um label correspondente. Nesse caso, o **ReplicaSet** gerencia o número de réplicas desses **pods** específicos e garante que o número especificado de **pods** exista.

- Um **ReplicaSet** (ou ReplicationController) fornece apenas uma função muito básica. E se você quiser atualizar o código do aplicativo? Ou iniciar um novo **ReplicaSet** e alternar para ele? Ou migrar gradualmente os **pods** de uma versão antiga para uma nova? Ou reverter para a versão antiga se a nova não estiver funcionando corretamente? Deployments e DeploymentConfigs podem fazer isso por você. Além disso, eles podem fazer isso automaticamente quando detectam alterações na configuração do aplicativo.

- Os endereços IP dos **pods** não são estáveis. Cada vez que um pod é reiniciado, um novo endereço IP é atribuído a ele. Para acessar um pod, você pode querer que o pod tenha um endereço IP estável. Ou você pode executar vários **pods** idênticos e balancear a carga entre eles; nesse caso, você deve ter um endereço IP estável para o balanceador de carga. Em ambos os casos, você usa um **service**.

- **service**, como **ReplicaSet**s, usam seletores para encontrar os **pods** associados a eles. Os seletores selecionam **pods** com labels específicos. Por exemplo, um **service** denominado **service**-web fornece um endereço IP estável (denominado ClusterIP) para os **pods** com o label app: web.

- Um ClusterIP fornecido por um **service** é interno ao cluster. Para acessar o aplicativo de fora do cluster, é necessária uma rota. O objeto Route obtém informações sobre **pods** associados do **service** de back-end e atua como um balanceador de carga externo para esses **pods**. As rotas geralmente são implementadas usando HAProxy. As rotas fornecem endereços de nomes de domínio, que podem ser usados ​​para acessar o aplicativo.






# Openshift Configuration 


- O Red Hat OpenShift Container Platform é normalmente implantado como um ambiente multiusuário.Os usuários incluem desenvolvedores de aplicativos, operações, segurança e administradores de plataforma.

- Todos esses usuários precisam se autenticar na plataforma, seja para acessar o console da web ou a API - por exemplo, com a interface de linha de comando.

- A autenticação precisa ser configurada para que cada usuário - independentemente de sua função - se autentique com suas próprias credenciais para que todas as ações possam ser auditadas para determinar quem fez o quê.

- O Processo de  autenticação com OAuth começa com o console da web ou a API redirecionando o usuário para o componente do servidor OAuth.

- Se o usuário for autenticado com sucesso, ele receberá um token OAuth

- O cliente inclui o token OAuth em cabeçalhos HTTP em solicitações de API subsequentes.

- O token OAuth expira após um intervalo de tempo configurável - o padrão é 24 horas - e o usuário deve se autenticar novamente.

- Você pode recuperar a lista de tokens OAuth ativos e identificar quais usuários estão conectados usando ```oc get oauthaccesstokens```.

- Uma vez conectado ao cluster, você pode consultar a sessão atual via ```  oc whoami```.

- O LDAP é um componente comum da infraestrutura de gerenciamento de identidade e acesso.

- LDAP TLS, é o padrão recomendado para autenticação, para isso ocorrer é necessário a criaçao de um configmap no namespace openshift-config, com a chave do certificado ca.crt ``` 
oc create configmap ldap-tls-ca -n openshift-config \
    --from-file=ca.crt=PATH_TO_LDAP_CA_FILE ```


- **RBAC** - controlas os objetos e determina o que pode ser feito com eles, quais verbos podem ser usados para gerenciar o objeto]

- ***Roles e ClusterRoles*** definem ações permitidas para tipos de recursos. As funções são definidas em um namespace de projeto e só podem ser usadas nesse namespace. ClusterRoles pode ser aplicado a todo o cluster ou a um namespace específico.


- ***RoleBindings*** concedem acesso mapeando um Role ou ClusterRole para usuários ou grupos para acesso a recursos dentro de um namespace de projeto.

- ***ClusterRoleBindings*** concedem acesso a tipos de recursos com escopo de cluster ou tipos de recursos em qualquer namespace.


- ***ClusterRoleBindings*** concedem acesso a tipos de recursos com escopo de cluster ou tipos de recursos em qualquer namespace.

- ***RoleBindings ou ClusterRoleBindings*** são um tipo de recurso no OpenShift. O acesso para criar esses recursos permite que um usuário conceda acesso a outros usuários ou grupos, mas o OpenShift impede que um usuário conceda acesso que o usuário não possui.

- ****service** account** Quando os aplicativos precisam de acesso à API de cluster OpenShift, eles podem usar uma conta de **service** para autenticação e autorização.

- Cada pod rodando no Red Hat OpenShift Container Platform tem uma conta de **service** associada, e aplicativos externos podem ser integrados ao cluster usando credenciais de conta de **service**

- As contas de **service** OpenShift são tratadas como um tipo de usuário cujo acesso pode ser gerenciado com o controle de acesso baseado em função OpenShift

-  Por exemplo, **DeploymentConfigs** são implementados usando um pod de implantador que é executado com uma conta de **service** de implantador. Os aplicativos em contêineres de pod podem fazer chamadas de API para fins de descoberta. Os servidores Jenkins usam a API de cluster para criar agentes em execução em contêineres. E os operadores usam a API de cluster para monitorar recursos personalizados e a API para gerenciar ConfigMaps, implantações e outros.


- **Security context constraints**  Ao contrário das políticas de autorização, que controlam o que um usuário pode fazer, restrições de contexto de segurança ou SCCs, controlam as ações que um pod pode realizar e o que pode acessar. SCCs são objetos que definem um conjunto de condições com as quais um pod deve ser executado para ser aceito no sistema.


# Resource manager

- O gerenciamento de recursos na Red Hat OpenShift Container Platform é um tópico amplo e importante. É o planejamento e exercício do uso controlado dos recursos limitados em um cluster

- Os recursos necessários para as cargas de trabalho não são infinitos, esteja você em um ambiente de nuvem pública ou em um data center local. Controlar o uso desses recursos é uma função que muitas vezes é negligenciada ou recebe atenção mínima.

- No Kubernetes, as solicitações e os limites são, em seu nível mais básico, mínimos e máximos que um contêiner pode consumir de recursos específicos. Eles são aplicados a contêineres, não a vagens.

- Uma request é a quantidade mínima de um recurso que o contêiner requer para ser executado. Esse valor pesa muito no agendador, pois ele decide em qual nó colocar um pod recém-solicitado. Isso Também tem um grande impacto nos cálculos de utilização de recursos que o Kubernetes faz para a capacidade do nó no cluster.

- Um limit é a quantidade máxima de um recurso que o contêiner pode consumir. Dependendo do tipo de recurso que você está controlando, como CPU ou memória, o Kubernetes reage de maneira diferente se o contêiner tentar exceder esse máximo.

- QOS - são atribuídas automaticamente a todos os **pods** pelo Kubernetes. Essas classes de QoS não podem ser definidas especificamente pelo usuário que está solicitando o pod. Eles podem, no entanto, ser influenciados pela especificação do pod.

- **BestEffort** Aplicado quando as requests do contêiner e os limite não estão definidos

- **Burstable** Aplicado quando as requests do contêiner são definidas, mas os limits não são definidos ou são superiores à solicitação

- **Guaranteed** Aplicado quando o contêiner requests e limits definidos com o mesmo valor




# Deployments

## Estrategias de rollout:
- **Rolling:** *default no openshift, prove continuos update mantendo a disponibilidade da app*
- **Recreate:**  *esta estrategia termina todos os **pods** que estão rodando antes de criar uma nova versão do deply*
- **Canary ou Blue-green:**  *nessa estrategia vc deve criar um novo deploy, com a nova versão da app tb deve criar novos **service**s, quando a app estiver pronta apenas reaponte o router para o novo **service** com a nova versão*
-  **A-B Testing:**  *nessa estrategia vc pode criar 2 versoes de um frontend com visual diferente e configurar o trafego de rede para mandar conexoes 50/50 para cada versão, essa tecnica permite usar mais que 2 versões de backend*






# Volumes:

## Persistentes volumes
- **PV** = Persistentes volumes Montando em nivel de host ( NFS, ISCSI, FIBERCHANNEL etc)
      NFS mounts
      
      GlusterFS
      
      Ceph® RBD
      
      AWS Elastic Block Store (EBS)
      
      Google GCE
      
      OpenStack® Cinder
      
      iSCSI
      
      Fibre Channel
- **PVC** = Persistentes volumes Claim - consumido do PC e  montando em nivel de pod para persistir os dados do app
    - Os desenvolvedores podem associar um PersistentVolumeClaim a um objeto, como uma configuração de deployment. o OpenShift monta o volume no pod.

    - Os desenvolvedores podem criar um ou mais PersistentVolumeClaims usando PersistentVolumes para fornecer armazenamento persistente a seus projetos. 
    
   

## Acess modes:

- **ReadWriteOnce:** Volume pode ser montado como read/write por um unico node

- **ReadOnlyMany:** Volume pode ser montado como read-only por varios nodes

- **ReadWriteMany:** Volume pode ser montado como read/write por varios nodes

## empyDir
- diretorio temporario existe apenas enquanto o pod esta no ar 
- iniciado vazio
- criado quando o pod é criado 
- é comportilhado entre os containers do mesmo pod 
- Como todos os containers no pod podem ler e gravar no  emptyDir, ele  é útil para implantações chamadas de "sidecar" em que, por exemplo, o contêiner principal executa um **service** e registra localmente no disco, que neste caso é emptyDir.O contêiner secundário pode então pegar esse arquivo de log e transmiti-lo para um **service** de registro.


## Environment variables

- Variáveis ​​de ambiente são amplamente compreendidas e usadas e facilmente consumidas programaticamente, tornando-as uma técnica popular para injetar variáveis ​​de configuração e outros objetos.

- O OpenShift fornece várias maneiras de definir variáveis ​​de ambiente por meio de segredos, ConfigMaps e a API Downward. As variáveis ​​de ambiente são visíveis no nível do pod.

- Você pode definir, cancelar ou listar variáveis ​​de ambiente em **pods** ou templates  de pod. Seu escopo esta no nivel de deployment e replication controller 


## Secrets

- Secrets fornecem uma maneira de separar as informações confidenciais dos **pods** que as consomem. Os secrets  são armazenados em Base64. Portanto, embora estejam protegidos, isso não fornece criptografia.

- São montadas nos **pods**  via  volume plug-in.

-  precisam ser definidos primeiro e depois referenciados. Quando montados como volumes, eles são montados como tmpfs e nunca permanecem no storage node.
-  São compartilhados dentro de um namespace.

- Podem ser montados em **pods** e consumidos como arquivos. A montagem de uma secret em um objeto é feita por meio do comando "oc set volume" com a flag  "--secret-name".

## ConfigMaps

- Os ConfigMaps fornecem um mecanismo conveniente para injetar dados de configuração em **pods**. Isso pode incluir arquivos de configuração, blobs JSON e outros objetos de configuração.

- Os dados são mantidos em pares de chave / valor que podem ser consumidos pelo pod.

- Casos de uso típicos para ConfigMaps incluem preencher os valores das variáveis ​​de ambiente, definir argumentos de linha de comando e preencher arquivos de configuração como um volume.

- Há restrições a serem observadas ao usar ConfigMaps. Eles devem ser criados antes de serem consumidos. Eles devem residir em um projeto e não podem ser compartilhados entre projetos ou namespaces.

- Ao atualizar um ConfigMap com novos valores, essas atualizações não são propagadas para os **pods** existentes. O ConfigMap deve ser redeployed do explicitamente.


## Downward API

- O OpenShift expõe uma API Downward que permite que os **pods** acessem informações sobre o ambiente de execução, incluindo pod, namespace e valores de recursos, como memória e CPU.




# Continuous Integration

- Verifica a integridade do build, verificando se o código-fonte pode ser retirado do repositório e construído para fins de implantação. O processo de construção pode incluir compilar, empacotar e configurar o software.

- Validar os resultados do teste executando todos os testes criados pelos desenvolvedores em um ambiente semelhante ao de produção para verificar se o código-fonte não foi quebrado como um efeito colateral do commit.

- Verificar a integração entre vários sistemas usando testes de integração para validar a integração de todos os sistemas usados ​​pelo software.

- Ele identifica quaisquer problemas e alerta todas as equipes afetadas para corrigir os problemas imediatamente.

- Você pode realizar a CI manualmente, mas as operações manuais carecem de rastreabilidade, portanto, o processo de CI geralmente é gerenciado por uma ferramenta. Em uma cultura DevOps,CI é obrigatório e executado por uma ferramenta que executa scripts de automação criados por desenvolvedores para evitar intervenção humana durante o processo de CI.

## Beneficios 

- **Feedback rápido:** quando uma build é executada, os membros da equipe são imediatamente notificados sobre o status da build. Isso reduz o tempo necessário para descobrir e corrigir quaisquer novos defeitos

- **Risco reduzido:** Ao integrar várias vezes ao dia, você pode reduzir os riscos em seu projeto. Bugs são detectados e corrigidos mais cedo. A integridade do software é mensurável por meio de testes unitarops e relatórios de inspeção de código.

- **Team Ownership:** Com a CI, não existe mais uma situação de "nós" versus "eles". Todos os membros da equipe recebem relatórios regulares sobre o status da build. Isso permite maior visibilidade do projeto, onde todos podem perceber tendências e tomar decisões eficazes. Isso também aumenta a confiança para adicionar novos recursos ao projeto, porque todos estão atualizados sobre o funcionamento atual do projeto.

- **Build de software implantável:** O processo de build deve gerar software que pode ser implantado. Todos estão se esforçando para criar um software que possa ser implantado a qualquer momento. Isso não significa que você deve implantar o software, mas que é um bom release candidate se quiser implantar. Hoje, muitas equipes de desenvolvimento lutam com esse cenário.

- **Processo automatizado:** automatizar a build economiza tempo, custos e esforço. Além disso, o processo é executado sempre da mesma forma. Liberar os desenvolvedores da execução de processos repetitivos permite que eles se concentrem em trabalhos de maior valor.


- O uso de um repositório de código é um requisito fundamental para qualquer projeto de software profissional. Para construir software de forma eficaz, todos os ativos de software devem ser armazenados em um repositório de código.Além disso, manter um repositório de código ajuda a evitar o problema em que o código funciona em uma máquina, mas não em outra.

- O Build deve ser totalmente automatizada, incluindo testes. Os testes automatizados devem ser aprovados para que sua construção seja aprovada. Eles são tão importantes quanto a compilação de código.

- Todos entendem que código que não compila não funciona. O mesmo vale para os testes: o código que não passa nos testes não funciona. Use uma estrutura de teste de unidade, como JUnit.


# Continuous Delivery

- CD - é uma extensão do CI ele descreve como criar um processo automatizado para construir e configurar seu software. A entrega contínua, ou CD, é simplesmente uma extensão da CI. O CD adiciona suporte para ambientes adicionais, incluindo teste, controle de qualidade e assim por diante

- CD é importante observar que o software não é implantado automaticamente na produção. O CD cria um artefato que você pode implantar na produção, mas não há nenhum requisito para fazer isso, que pode depender de uma aprovaçção ou validação antes de ser implementado 


# Templates 

- Um template descreve um conjunto de recursos que podem ser personalizados e processados ​​para produzir uma configuração.

- Cada template é uma lista parametrizada que o OpenShift usa para criar uma lista de recursos, incluindo **service**s, **pods**, rotas e configurações de compilação. Um template também define um conjunto de label para aplicar a todos os recursos que ele cria.

- Labels são usados ​​para gerenciar recursos gerados, como **pods**. Os labels especificados no template são aplicados a todos os recursos gerados a partir do template

- As labels são usadas para agrupar objetos e indicar para eles quais são os respectivos **pods** relacionados aquele replication controller ou **service**


 # Openshift application deployment 

- Outro recurso do DeploymentConfigs é que, se a implantação de uma nova versão resultar em um erro, o DeploymentConfig reverterá automaticamente para a versão de trabalho anterior. Ele espera 10 minutos por padrão e, se o aplicativo ainda não estiver pronto, a revisão com falha é excluída e a revisão DeploymentConfig anterior é lançada.

- Existem outras diferenças entre Deployments e DeploymentConfigs que são descritas na seção Comparing Deployments and DeploymentConfigs da documentação da OpenShift Container Platform.

- A documentação descreve a diferença da seguinte maneira:

  - Uma diferença importante entre Deployments e DeploymentConfigs são as propriedades do teorema CAP que cada projeto escolheu para o processo de distribuição. DeploymentConfigs preferem consistência, enquanto as implementações têm preferência por disponibilidade em vez de consistência.

  - Para DeploymentConfigs, se um nó que executa um pod do implantador ficar inativo, ele não será substituído. O processo espera até que o nó volte a ficar online ou seja excluído manualmente. Excluir manualmente o nó também exclui o pod correspondente. Isso significa que você não pode excluir o pod para desfazer a implementação, pois o kubelet é responsável por excluir o pod associado.

  - No entanto, as implementações de implantação são conduzidas por um gerenciador de controlador. O gerenciador de controlador é executado no modo de alta disponibilidade em mestres e usa algoritmos de eleição de líder para valorizar a disponibilidade em vez da consistência. Durante uma falha, é possível que outros mestres atuem na mesma implantação ao mesmo tempo, mas esse problema é resolvido logo após a ocorrência da falha.

- Implementações e ReplicaSets são considerados a forma preferencial de implementação de aplicativos. Por outro lado, quando você usa o comando oc new-app, ele cria DeploymentConfigs e ReplicationControllers. É uma boa ideia estar familiarizado com os dois métodos. Este laboratório fornece uma visão geral rápida de suas semelhanças e diferenças



#  Machine API 

- É a combinacao de recursos primarios baseados no projeto de desenvoltvimento da API do cluster updatream e o Openshift container plataform resources.

- Existem 2 recursos primarios

    - **machines** - principal unidade, descreve o host para um node 

    - **machineSet** - Grupo de machines, machineset estao para as machines como o replicaset esta para o pod, a quantidade de replicas e controlada atraves do machineset,  Machine Controllers provisionam e desprovisionam machines e nodes 

- Recursos criados no namespace de Machine API ***openshift-machine-api*** nao fazem parte do escopo do cluster  


-  **O Machine API operator** gerencia o ciclo de vida, dos objetos para esse proposito extendendo a capacidade da AP do kubernetes, isso permite controlar o estado desejado  das maquinas dentro do cluster 






## Para Mais Informações
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#latest-release-via-dnf-or-yum)
- [Ansible AWX](https://github.com/ansible/awx/blob/devel/INSTALL.md#installing-awx)
- [Docker](https://docs.docker.com/install/linux/docker-ce/centos/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- [Docker SDK](https://pypi.org/project/docker/)