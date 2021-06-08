



### retorna o nome do programa em execução
```shell
  $echo $0
 ```

### retorna o shell em uso 
```shell
$ echo $SHELL
````

## Variaveis 

- Variáveis locais Presentes na sessão atual do shell e disponíveis apenas para ele mesmo. 
- Variáveis de ambiente Disponíveis para todos os processos executados no shell.
- Variáveis do shell As variáveis especiais que o próprio shell define e que podem ser tanto locais quanto de ambiente.
- Variaveis readonly não podem ser alteradas 
``` shell
$ fruta="banana"
$ readonly fruta
$ fruta="laranja"
bash: fruta: a variável permite somente leitura
```
- Para remover uma variavel

```bash
$ fruta="banana"
$ echo $fruta
banana
$ unset fruta
$ echo $fruta
````

- Atribundo a saida de um comando a uma variavel

```bash 
$ usuario=$(whoami)
$ echo $usuario
arthur

#ou 

$ maquina=`hostname`
$ echo $maquina
excalibur

````

- Acessando valores de uma variavel
``` bash
$ read -p "Digite seu nome: " nome
Digite seu nome: Renata
$ mensagem="Olá, $nome!"
$ echo $mensagem
Olá, Renata!

#variavel com indice

$ nomes=("João" "Renata" "José")
$ echo $nomes
João

# ${nome_da_variável[índice]} 
# ${nome_da_variável[*]} 
$ echo ${nomes[1]}
Renata
```
- a variavel $? armazena a saida do ultimo comendo digitado, se a saida for 0 significa sucesso na execução

```bash
 $ echo $? 
  
# Aqui nós executamos o comando "help"...
# encaminhando a saida para /dev/null 
help ls &> /dev/null

# E aqui nós testamos a saída com o comando "test"...

[[ $? -eq 0 ]] && echo "ls é interno!" || echo "ls não é interno!"

# o comando test tb pode ser executado da seguinte forma

test $? -eq 0




exit 0

 ````

- Passando Parametros 
``` bash
#!/usr/bin/env bash
# Mensagem de erro...
msg="É preciso informar um comando válido!"
# Teste de quantidade de argumentos...
[[ $# -ne 1 ]] && echo $msg && exit 1
# Aqui nós executamos o comando "help"...
help $1 &> /dev/null
# E aqui nós testamos a saída com o comando "test"...
[[ $? -eq 0 ]] && echo "$1 é interno!" || echo "$1 não é interno!"
exit 0

```

- **$?** Armazena o status de saída do último comando executado.
- **$$** Armazena o PID da sessão corrente do shell.
- **$0** Armazena o nome do arquivo do programa ou do script sendo executado.

- **$n**
      (a partir de 1) Armazena os valores (parâmetros) passados para o script, onde “n” é um número inteiro, positivo, correspondente à posição dos parâmetros na linha de comando (daí “posicionais”).
- **$#** Armazena o número de parâmetros passados para o script.


## Uso de Aspas


```bash 
~$ cor="verde"
# Sem aspas...
:~$ echo $cor
verde
# Com aspas duplas...
:~$ echo "$cor"
verde
# Com aspas simples...
:~$ echo '$cor'
$cor
# Com aspas duplas e caractere de escape...
:~$ echo "\$cor"
$cor
```

