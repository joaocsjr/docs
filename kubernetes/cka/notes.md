# Notas sobre treinamento de kubernetes

   visudo -f /etc/sudoers
   cat /etc/group | grep sudo
   usermod -aG sudo  jcastro
   usermod -aG sudo devops
   visudo -f /etc/sudoers
   usermod -aG sudo student
   apt-get update && apt-get upgrade
   apt install vim -y

    modprobe overlay
modprobe br_netfilter
sysctl --system

   export OS=xUbuntu_18.04
   export VERSION=1.20
   echo "deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$VERSION/$OS/ /" > /tc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:$VERSION.list
   apt update
   curl -L https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$VERSION/$OS/Release.key | 
   apt-key add -
   curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/Release.key | apt-key add -
   apt update

   apt-get install cri-o cri-o-runc
   systemctl daemon-reload
   systemctl enable crio
   systemctl start crio
   systemctl status crio
   curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
   apt update -y
   apt-get install -y kubeadm=1.20.1-00 kubelet=1.20.1-00 kubectl=1.20.1-00
   apt-mark hold kubelet kubeadm kubectl


root@cp: ̃# vim /etc/sysctl.d/99-kubernetes-cri.conf

   net.bridge.bridge-nf-call-iptables  = 1
   net.ipv4.ip_forward                 = 1
   net.bridge.bridge-nf-call-ip6tables = 1



   kubeadm init --config=kubeadm-config.yaml --upload-certs | tee kubeadm-init.out