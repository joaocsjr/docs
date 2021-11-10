# KB 
<div align="center">
<h2>Hello, I am Joao Castro :)</h2>


  <img src="https://raw.githubusercontent.com/MicaelliMedeiros/micaellimedeiros/master/image/computer-illustration.png" min-width="400px" max-width="400px" width="400px" align="right" alt="Computador iuriCode">

<p align="left"> 
 <h3>  About Me!</h3>

  🙋 Devops student
  
  💼 Working as a IT specialist 

<h3>⚙️ Stack</h3>

  💻 Linux ● Docker ● Kubernetes ● Ansible ● Terraform ● GitLab
  
  ⭐ VMWare ● Windows ● Openshift ● AWS ● AZURE ● GCP ● Ansible Tower

  
</p>


  

<img alt="AWS" src="https://img.shields.io/badge/AWS%20-%23FF9900.svg?&style=for-the-badge&logo=amazon-aws&logoColor=white"/>
<img alt="GCP" src="https://img.shields.io/badge/Google_Cloud-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white"/>
<img alt="Gitlab" src="https://img.shields.io/badge/GitLab-330F63?style=for-the-badge&logo=gitlab&logoColor=white"/>

<img alt="Jenkins" src="https://img.shields.io/badge/jenkins%20-%232C5263.svg?&style=for-the-badge&logo=jenkins&logoColor=white"/>
<img alt="Ubuntu" src="https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white" />
<img alt="Docker" src="https://img.shields.io/badge/docker%20-%230db7ed.svg?&style=for-the-badge&logo=docker&logoColor=white"/>
<img alt="Kubernetes" src="https://img.shields.io/badge/kubernetes%20-%23326ce5.svg?&style=for-the-badge&logo=kubernetes&logoColor=white"/>
<img alt="Vagrant" src="https://img.shields.io/badge/vagrant%20-%231563FF.svg?&style=for-the-badge&logo=vagrant&logoColor=white"/>
<img alt="Ansible" src="https://img.shields.io/badge/ansible%20-%231A1918.svg?&style=for-the-badge&logo=ansible&logoColor=white"/>
<img alt="Terraform" src="https://img.shields.io/badge/terraform%20-%235835CC.svg?&style=for-the-badge&logo=terraform&logoColor=white"/>



 <p>

<br>
  <a href="https://www.linkedin.com/in/joaocsjr/">
    <img src="https://img.shields.io/badge/linkedin-%230077B5.svg?&style=for-the-badge&logo=linkedin&logoColor=white" />
  </a>&nbsp;&nbsp;
</br>
</p>


[![Joao castro GitHub Stats](https://github-readme-stats.vercel.app/api?username=joaocsjr&show_icons=true)](https://github.com/joaocsjr)

<p align='center'>
  📫 How to reach me: <a href='mailto:jcastro@jcastro.net'>jcastro@jcastro.net</a>
</p>





# Procedimento de instalação e configuração do ansible 

## Maquina controller

```bash
yum install ansible
useradd devops
echo 'r3dh4t1!' | passwd --stdin devops

### criando chaves ssh para acesso sem senha 
ssh-keygen -N '' -f ~/.ssh/id_rsa

### pegando valor da chave 
cat homedouser/.ssh/id_rsa.pub

### Criando user para usar a chave criada
ansible all -m user -a "name=devops" -k 


### copiar como root
ansible all -m authorized_key -a "user=devops state=present key='ssh-rsa '" -k

### configurando o sudoers
ansible all -m lineinfile -a "dest=/etc/sudoers state=present line='devops ALL=(ALL) NOPASSWD: ALL'" -k 

```
**Como alternativa pode ser usado o comando:**


```bash 
ssh-copy-id -i <file> devops@<server>
```


**testar a conexão com o host remoto**
``` bash
ssh <servername>
````

**validando acesso via ansible**
```bash 
ansible all -m ping
```



