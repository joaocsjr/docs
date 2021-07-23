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

# verifica info dos pods em formato yaml
oc get pods [YOUR_APP_PODNAME] -o yaml

# filtrando saida do comando get pods
oc get pods --field-selector status.phase=Running

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
## Service

```yaml
apiVersion: v1
kind: Service
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
    kind: Service
    name: httpd-deployment
```

# Deployments

## Estrategias de rollout:
- **Rolling:** *default no openshift, prove continuos update mantendo a disponibilidade da app*
- **Recreate:**  *esta estrategia termina todos os pods que estão rodando antes de criar uma nova versão do deply*
- **Canary ou Blue-green:**  *nessa estrategia vc deve criar um novo deploy, com a nova versão da app tb deve criar novos services, quando a app estiver pronta apenas reaponte o router para o novo service com a nova versão*
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
- Como todos os containers no pod podem ler e gravar no  emptyDir, ele  é útil para implantações chamadas de "sidecar" em que, por exemplo, o contêiner principal executa um serviço e registra localmente no disco, que neste caso é emptyDir.O contêiner secundário pode então pegar esse arquivo de log e transmiti-lo para um serviço de registro.


## Environment variables

- Variáveis ​​de ambiente são amplamente compreendidas e usadas e facilmente consumidas programaticamente, tornando-as uma técnica popular para injetar variáveis ​​de configuração e outros objetos.

- O OpenShift fornece várias maneiras de definir variáveis ​​de ambiente por meio de segredos, ConfigMaps e a API Downward. As variáveis ​​de ambiente são visíveis no nível do pod.

- Você pode definir, cancelar ou listar variáveis ​​de ambiente em pods ou templates  de pod. Seu escopo esta no nivel de deployment e replication controller 


## Secrets

- Secrets fornecem uma maneira de separar as informações confidenciais dos pods que as consomem. Os secrets  são armazenados em Base64. Portanto, embora estejam protegidos, isso não fornece criptografia.

- São montadas nos pods  via  volume plug-in.

-  precisam ser definidos primeiro e depois referenciados. Quando montados como volumes, eles são montados como tmpfs e nunca permanecem no storage node.
-  São compartilhados dentro de um namespace.

- Podem ser montados em pods e consumidos como arquivos. A montagem de uma secret em um objeto é feita por meio do comando "oc set volume" com a flag  "--secret-name".

## ConfigMaps

- Os ConfigMaps fornecem um mecanismo conveniente para injetar dados de configuração em pods. Isso pode incluir arquivos de configuração, blobs JSON e outros objetos de configuração.

- Os dados são mantidos em pares de chave / valor que podem ser consumidos pelo pod.

- Casos de uso típicos para ConfigMaps incluem preencher os valores das variáveis ​​de ambiente, definir argumentos de linha de comando e preencher arquivos de configuração como um volume.

- Há restrições a serem observadas ao usar ConfigMaps. Eles devem ser criados antes de serem consumidos. Eles devem residir em um projeto e não podem ser compartilhados entre projetos ou namespaces.

- Ao atualizar um ConfigMap com novos valores, essas atualizações não são propagadas para os pods existentes. O ConfigMap deve ser redeployed do explicitamente.


## Downward API

- O OpenShift expõe uma API Downward que permite que os pods acessem informações sobre o ambiente de execução, incluindo pod, namespace e valores de recursos, como memória e CPU.







# Openshift Configuration 


- O Red Hat OpenShift Container Platform é normalmente implantado como um ambiente multiusuário.Os usuários incluem desenvolvedores de aplicativos, operações, segurança e administradores de plataforma.

- Todos esses usuários precisam se autenticar na plataforma, seja para acessar o console da web ou a API - por exemplo, com a interface de linha de comando.

- A autenticação precisa ser configurada para que cada usuário - independentemente de sua função - se autentique com suas próprias credenciais para que todas as ações possam ser auditadas para determinar quem fez o quê.

- O Processo de  autenticação com OAuth começa com o console da web ou a API redirecionando o usuário para o componente do servidor OAuth.

- Se o usuário for autenticado com sucesso, ele receberá um token OAuth

- O cliente inclui o token OAuth em cabeçalhos HTTP em solicitações de API subsequentes.

- O token OAuth expira após um intervalo de tempo configurável - o padrão é 24 horas - e o usuário deve se autenticar novamente.



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




















# Ubuntu
**EM DESENVOLVIMENTO**

## Para Mais Informações
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#latest-release-via-dnf-or-yum)
- [Ansible AWX](https://github.com/ansible/awx/blob/devel/INSTALL.md#installing-awx)
- [Docker](https://docs.docker.com/install/linux/docker-ce/centos/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- [Docker SDK](https://pypi.org/project/docker/)