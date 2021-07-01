# Notas do treinamento Kubernetes





## Criando cluster

  - multinode com LB:

```bash
k3d cluster create --agents 3 --servers 3 -p "8080:30000@loadbalancer"
```


### Git do app de exemplo

https://github.com/KubeDev/rotten-potatoes


## Criando a imagem docker para a app

  ```bash
  ## clonar o projeto e criar o dockfile 
  git clone https://github.com/KubeDev/rotten-potatoes
  cd rotten-potatoes &&  cd src

  ## build, tag das imagens e envio da imagens para o registry
  docker build -t joaocsjr/app-python:v1 -f DockerFile

  docker image ls

  docker tag joaocsjr/app-python:v1 joaocsjr/app-python:latest

  docker push joaocsjr/app-python:v1

  docker push joaocsjr/app-python:latest

````
