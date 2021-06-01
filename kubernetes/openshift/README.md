# Notas do treinamento Openshift
 **É recomendavel usar em ambientes de Teste/Desenvolvimento**.
chama


IPvTmxRcGBbp

# Index
- [Openshift](#Openshift-4.x)
    - [Anatomia de um arquivo de manifesto](#anatomia-de-um-arquivo-de-manifesto)
    - [YAML Basico](#YAML-Basico)
    - [Comandos basicos](#Comandos-basicos)
    - [Exemplos](#Exemplos)
        - [Deployment](#Deployment)
        - [Service](#Service)
        - [Route](#Route)
    - [Intalando Ansible](#intalando-ansible)
    - [Instalando Docker ](#instalando-docker)
        - [Docker SDK para Python](#docker-sdk-para-python)
        - [Docker Compose](#docker-compose)
    - [Instalando Node e NPM](#instalando-node-e-npm)
    - [Instalando Ansible AWX](#instalando-ansible-awx)
- [Ubuntu](#ubuntu)
- [Para mais Informações](#para-mais-informações)

# Openshift 4.x

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











# Ubuntu
**EM DESENVOLVIMENTO**

## Para Mais Informações
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#latest-release-via-dnf-or-yum)
- [Ansible AWX](https://github.com/ansible/awx/blob/devel/INSTALL.md#installing-awx)
- [Docker](https://docs.docker.com/install/linux/docker-ce/centos/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- [Docker SDK](https://pypi.org/project/docker/)