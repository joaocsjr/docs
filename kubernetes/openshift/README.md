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

```bash 
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



```
Operating System: CentOS Linux 7 (Core)
Kernel: Linux 3.10.0-957.el7.x86_64
Architecture: x86-64
```
## Requisitos do Sistema
- Processador: CPU dual core
- Memória: 4GB de RAM
- Espaço no disco: 20GB de espaço livre

## Requisitos para Instalação
Para aplicação Ansible AWX funcionar corretamente, será necessário:
- Ansible 2.4+
- Recente versão do Docker
- Docker SDK para Python
- Git
- Node versão LTS
- NPM versão LTS

## Criando Usuário
Vamos criar um usuário chamado awx:
```bash
sudo adduser awx
```

Agora escolha uma senha para este usuário:
```bash
sudo passwd awx
# Comfirme a nova senha. É recomendavel usar uma senha forte:
Changing password for user awx.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

Vamos adicionar o usuário no grupo wheel:
```bash
sudo usermod -aG wheel awx
# Por padrão, no CentOS, membros do grupo wheel já são sudo
```

Use o comando `su -` para alternar para a nova conta de usuário:
```bash
sudo su - awx
```

Execute `yum update` para checar se há atulizações, caso tenha estas serão aplicadas:
```bash
sudo yum update -y
```

## Instalando Python 3
Execute o camando abaixo para iniciar a instalção:
```bash
sudo yum install python3 -y
```

Defina Python 3 como padrão:
```bash
sudo alternatives --set python /usr/bin/python3
```

Verifique se ocorreu tudo certo:
```bash
python -V
# Exemplo de saida:
Python 3.6.8
```

## Instalando pip 
Pip é um gerenciador de pacotes que simplifica a instalação e gerenciamento de pacotes escritos em Python.\n Por padrão ele não vem instalado no CentOS.

Adcione repositório EPEL:
```bash
sudo yum install epel-release -y
sudo yum update -y
```

Uma vez que o repositório EPEL foi abillitado, agora podemos instalar pip:
```bash
sudo yum install python-pip -y 
```

Verifique se há alguma atualização. Casa haja, será instalado automaticamente:
```bash
sudo pip install --upgrade pip
```

Verifique se o pip foi instalado:
```bash
pip -V
# Exemplo de saida:
pip 19.3.1 from /usr/lib/python2.7/site-packages/pip (python 2.7)
```

## Instalando Git
Execute o comando abaixo para instalar git:
```bash
sudo yum install git -y
```

Verifique se o git foi instaldo:
```bash
git --version
# Exemplo de saida:
git version 1.8.3.1
```

## Intalando Ansible
Execute o comando abaixo para instalar o Ansible
```bash
sudo yum install ansible -y
```

Teste se foi instalado corretamente:
```bash
ansible localhost -m ping
# Exemplo de saida:
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

## Instalando Docker 
Vamos desinstalar versões anteriores:
```bash
sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine -y
```

Agora vamos instalar repositório:
```bash
sudo yum install yum-utils  device-mapper-persistent-data lvm2 -y
```  

Use o seguinte comando para definir repositório estavel: 
```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum update -y
```

Agora vamos instalar a ultima versão do Docker Engine - Community:
```bash
sudo yum install docker-ce docker-ce-cli containerd.io -y
```

Inicie o Docker:
```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Verifique se a instalação ocorreu corretamente executando a imagem `hello-word`:
```bash
sudo docker run hello-world
```
Esse comando baixara uma imagem docker e a executara em um container. Se tudo funcionar como esperado,
uma menssagem ira aparecer, parecida com esta:
```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

### Docker SDK para Python
Vamos instalar Docker SDK usando pip:
```bash
sudo pip install docker
```
### Docker Compose
Agora vamos baixar a versão estável  do Docker Compose:
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

Aplique permissões de execução ao binario:
```bash
sudo chmod +x /usr/local/bin/docker-compose
```

Crie um link simbolico para pasta /usr/bin:
```bash
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

Instale Docker Compose usando pip:
```bash
sudo pip install docker-compose
```

Verifique se instalou corretamente:
```bash
docker-compose --version
# Exemplo de saida:
docker-compose version 1.24.1, build 4667896b
```

## Instalando Node e NPM
Adicione o repositorio Node:
```bash
sudo curl -sL https://rpm.nodesource.com/setup_12.x | sudo bash -
sudo yum update -y
# Caso queira versão Node 10.x apenas mude o setup_12.x para setup_10.x
```

Execute o comando abaixo para instalar Node.js e NPM:
```bash
sudo yum install nodejs -y
```

Verifique se foi instalado corretamanete:
```bash
node -v && npm -v
# Exemplo de saida:
v12.13.0 
6.12.0
```

## Instalando Ansible AWX
Faça clone do repositório AWX project:
```bash
git clone https://github.com/ansible/awx.git
```

Entre na pasta `awx/installer/` e mude para ultima versão estável do projeto:
```bash
cd awx/installer/
git checkout 9.0.0
```
Agora que estou fazendo, a ultima versão é a 9.0.0, [verifique clicando aqui as versões disponiveis](https://github.com/ansible/awx/releases)

Execute o Ansible playbook para instalar as imagens docker:
```bash
sudo ansible-playbook -i inventory install.yml
```

Verifique se as imagens estão funcionado:
```bash
sudo docker ps
```
Saida esperada:
```
CONTAINER ID        IMAGE                        COMMAND                  CREATED              STATUS              PORTS                                                 NAMES
d192aec116cc        ansible/awx_task:9.0.0       "/tini -- /bin/sh -c…"   About a minute ago   Up About a minute   8052/tcp                                              awx_task
0cb0bd7de7cc        ansible/awx_web:9.0.0        "/tini -- /bin/sh -c…"   About a minute ago   Up About a minute   0.0.0.0:80->8052/tcp                                  awx_web
ac0ad49488d0        ansible/awx_rabbitmq:3.7.4   "docker-entrypoint.s…"   About a minute ago   Up About a minute   4369/tcp, 5671-5672/tcp, 15671-15672/tcp, 25672/tcp   awx_rabbitmq
cb8070214384        postgres:10                  "docker-entrypoint.s…"   About a minute ago   Up About a minute   5432/tcp                                              awx_postgres
59c835f20bd5        memcached:alpine             "docker-entrypoint.s…"   About a minute ago   Up About a minute   11211/tcp                                             awx_memcached
```

No browser, abra a aplicação em `http://seu.ip` ou use o comando `curl`:
```bash
curl http://localhost/
```

Quando você abrir a aplicação, Ansible AWX estará atualizando. Logo após aparecera a tela de login.
```
# Por padrão o usuario e senha são:
Username: admin
Password: password
```
# Ubuntu
**EM DESENVOLVIMENTO**

## Para Mais Informações
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#latest-release-via-dnf-or-yum)
- [Ansible AWX](https://github.com/ansible/awx/blob/devel/INSTALL.md#installing-awx)
- [Docker](https://docs.docker.com/install/linux/docker-ce/centos/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- [Docker SDK](https://pypi.org/project/docker/)