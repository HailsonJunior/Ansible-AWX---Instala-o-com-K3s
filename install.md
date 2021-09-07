# Instalação Ansible AWX

Abaixo os passos para realizar a instalação do Ansible AWX:

Requisitos:

- Memória RAM >= 6GB
- CPU >= 3 Cores
- Armazenamento >= 20GB
- “Distribuições” Kubernetes
- Kubernetes (K8s)
- Minikube
- K3s

Preparando a máquina
------------

Desabilite o firewalld 

`systemctl disable firewalld`

Desabilite a memória swap (remova também do fstab)

`swapoff -a`

Faça o update

Debian: `sudo apt update && sudo apt upgrade -y`

CentOS: `sudo yum upgrade -y`

K3s
------------

Para evitar problemas com o comando kubectl no CentOS, verifique se o diretório “/usr/local/bin” está na variável PATH. Se não estiver, adicione com:

`export PATH="/usr/local/bin/:$PATH"`

Instalação k3s

    `curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=v1.21.1+k3s1 sh -s - --no-deploy=traefik`

    export KUBECONFIG=/etc/rancher/k3s/k3s.yaml

    systemctl status k3s.service

Antes de continuar com o a instalação, caso seja nexessário realizar ajustes de rede, altere o arquivo “/run/flannel/subnet.env”.

Helm
------------

`curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash`

Traefik
------------

    helm repo add traefik https://helm.traefik.io/traefik
    helm repo update
    helm install traefik traefik/traefik
    helm  ls --all-namespaces
    k3s kubectl get nodes
    k3s kubectl get pods
    
AWX Operator
------------

`k3s kubectl apply -f https://raw.githubusercontent.com/ansible/awx-operator/devel/deploy/awx-operator.yaml`

Caso for preciso atualizar o Operator do AWX para uma nova versão no futuro, basta executar este comando novamente. Podemos no lugar de “devel” inserir uma versão específica como, por exemplo, “0.12.0”. Com devel, será instalada a versão mais nova.

Se o container do operator não esteja subindo, verifique os logs com os comandos: 

    sudo k3s kubectl logs -f $(sudo k3s kubectl get pods | awk '/awx-operator/{print $1}')
    k3s kubectl get events -w

Se o erro for de conexão com algum endereço, adicione as seguintes regras no iptables:

    iptables -I INPUT 3 -s 10.42.0.0/16 -j ACCEPT
    iptables -I INPUT 3 -d 10.42.0.0/16 -j ACCEPT

AWX Operator
------------

Crie um arquivo chamado “meu-awx.yml” com o seguinte conteúdo:

    ---
    apiVersion: awx.ansible.com/v1beta1
    kind: AWX
    metadata:
      name: awx
    spec:
      service_type: nodeport
      ingress_type: none
      hostname: awx.ontic.local
    
    ---
    apiVersion: v1
    kind: Secret
    metadata:
      name: awx-admin-password
      namespace: default
    stringData:
      password: <suaSenha>
    
Esse será o instalador do AWX. Altere “<suaSenha>” pela senha desejada para o usuário admin.
Altere no arquivo /etc/hosts de acordo com o hostname do arquivo meu-awx.yml.
  
Execute o seguinte comando para instalar:
  
`k3s kubectl apply -f meu-awx.yml`
  
Acompanhe a instalação:
  
`sudo k3s kubectl logs -f $(sudo k3s kubectl get pods | awk '/awx-operator/{print $1}')`
  
Verifique a porta em que o serviço está rodando:
  
`kubectl get services`
  
Tente acessar a plicação pelo browser.
  
Para deletar todos os pods:

`k3s kubectl delete pods --all`
  

Caso precisar desinstalar o k3s:

`/usr/local/bin/k3s-uninstall.sh`
