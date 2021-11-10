 # Kubernetes CKA Udemy


![https://blog.geekhunter.com.br/wp-content/uploads/2019/04/logo-kubernetes-arquitetura-de-cluster-3.png](https://blog.geekhunter.com.br/wp-content/uploads/2019/04/logo-kubernetes-arquitetura-de-cluster-3.png)




> # Arquitetura 


![https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)


<br> 

> ETCD
 - ### Caracteristicas
    -   Banco chave valor 
    -   Roda na porta 2379
    -   etcdctl command line 
     
``` bash

# o pod do etcd roda no namespace do kube-system
kubectl get pods -n kube-system

##executadando comando dentro do pod do etcd

kubectl exec etcd-master –n kube-system etcdctl get / --prefix –keys-only

```
<br>

> ### Kube-api server
  - ### Função
    -   todas as transaçoes que são feitas com o etcd 
    - controla o fluxo de autenticação:
  
  <br>

  >**FLUXO de consulta**
   - Quando executamos qualquer comando, as interações são feitas com a api-server 

      1. Autentica o user
      1. verifica a se a requisicão e valida
      2. Em caso de consulta o dado solicitado no etcd, em caso de novo item 4
      3. atualiza o etcd
      4. O scheduler verifica o apiserver, percebe que um novo pod foi criado, apos isso ele determina qual node vai executar aquele pod e informa o kubelet 
      5. kubelet interage com o container runtime e executa o pod conforme especificado
 

<br>




> ### Kube Controller Manager 


- Existem alguns tipos de controllers

- [x] Node Controller
  - Verifica o status dos nodes
  - Node monitor period = 5s
  - Node monitor grace period = 40s
  - Pod Eviction timeout = 5m 

- [x] Replication controller 
  - Gerencia dos replica sets


<br>


> ### Kube Scheduler
  - ### Função
    -  verificar em qual node o pod deve ser executado
    - Utiliza metricas de capacity para realizar o ranking e determinar qual node tem recursos suficientes para executar aquele pod 


<br>

> ### Kubelet 
  - ### Função : 
    -  realizar as config para inclusao do node no cluster 
    -  executar ou matar um container em um node usando as instrucoes do scheduler
    -  enviar informacoes para o master sobre o status de um pod 
    - Realiza a interacao com o container engine para baixar a imagem do registry no worker node e executar o pod 
    - Monitora  o api server para saber sobre as alterações
    - Responsavel pelo lifecycle do pod
    - Reporta informações sobre os nodes e pods 
    - Pod probes
    - ***Kubeadm nao instala o kubelet, precisa ser instalado manualmente***
- 
    
<br>

> ### Kube-proxy
  - ### Função:
    - Comunicação entre os pods 
    - Gerenciamento dos services 
    - iptables 
    - routing e traffic para os pods 
    - Load Balance
    - 


<br>

> ### Container Runtime
  - ### Função:
    - Download das imagens
    - Execução dos containers
    - Container runtime interface
  - 
