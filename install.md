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

```bash
systemctl disable firewalld
```

Desabilite a memória swap (remova também do fstab)

```bash
swapoff -a
```

Faça o update

Debian: 
```bash 
sudo apt update && sudo apt upgrade -y
```

CentOS: 
```bash
sudo yum upgrade -y
```

K3s
------------

Para evitar problemas com o comando kubectl no CentOS, verifique se o diretório “/usr/local/bin” está na variável PATH. Se não estiver, adicione com:

```bash
export PATH="/usr/local/bin/:$PATH"
```

Instalação k3s

    curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=v1.21.1+k3s1 sh -s - --no-deploy=traefik

    export KUBECONFIG=/etc/rancher/k3s/k3s.yaml

    systemctl status k3s.service

Antes de continuar com a instalação, caso seja necessário realizar ajustes de rede, altere o arquivo “/run/flannel/subnet.env”.
Em alguns casos, por exemplo, para comunicação com o Git, será necessário alterar o MTU. O MTU é configurado automaticamente neste arquivo com base no MTU configurado no host.

Helm
------------

```bash
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

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

```bash
k3s kubectl apply -f https://raw.githubusercontent.com/ansible/awx-operator/devel/deploy/awx-operator.yaml
```

Caso for preciso atualizar o Operator do AWX para uma nova versão no futuro, basta executar este comando novamente. Podemos no lugar de “devel” inserir uma versão específica como, por exemplo, “0.12.0”. Com devel, será instalada a versão mais nova.

Se o container do operator não estiver subindo, verifique os logs com os comandos: 

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
      password: suaSenha
    
Esse será o instalador do AWX. Altere "suaSenha" pela senha desejada do usuário admin para acessar a interface após a instalação.
Altere no arquivo "/etc/hosts" de acordo com o hostname do arquivo meu-awx.yml. Por exemplo:

`127.0.0.1 awx.ontic.local`
  
Execute o seguinte comando para instalar:
  
```bash
k3s kubectl apply -f meu-awx.yml
```
  
Acompanhe a instalação:
  
```bash
sudo k3s kubectl logs -f $(sudo k3s kubectl get pods | awk '/awx-operator/{print $1}')
```
  
Verifique a porta em que o serviço está rodando:
  
```bash
kubectl get services
```
  
Tente acessar a plicação pelo browser.
  
Para deletar todos os pods:

```bash
k3s kubectl delete pods --all
```
  

Caso seja preciso desinstalar o k3s:

```bash
/usr/local/bin/k3s-uninstall.sh
```
