# Ansible AWX

Este guia o ajudará a realizar a instalação do Ansible AWX em seu ambiente utilizando o K3s. 

Abaixo alguns conceitos importantes sobre as ferramentas utilizadas:

AWX
------------

O AWX é um a interface WEB REST API (OpenSource) baseada no Ansible Tower (ferramenta enterprise da RedHat). Essa interface atua como um Frontend para a Engine Ansible.

Features:

- Interface gráfica
- Regras baseadas em “Access Control”
- Usuários e Grupos (teams)
- Agendamento de tarefas
- Inventário dinâmicos
- Multi Playbook
- Acompanhamento de Jobs em tempo real
- Notificações através de SMTP, Slack, Webhook
- Integração com diferente mecanismo de autenticação (console)
  - Ldap
  - Azure
  - Github
- Diferentes mecanismos de autenticação (targets)
	- Usuários/Senha (SSH/WinRM
  - Private Key (SSH)
  - AWS
  - Git
  - Azure

Licença: Apache 2.0

Operator
------------

Operator Framework é um conjunto de ferramentas desenvolvido pela comunidade Open Source da Red Hat e Kubernetes lançado em 2018. Possui como objetivo realizar o gerenciamento de aplicações nativamente desenvolvidas para Kubernetes, chamadas de operações e efetivamente, automação/escalabilidade.

- Método de empacotamento, deploy e gerenciamento de aplicações Kubernetes.
- Inicialmente utilizado para deploy de aplicações, consequentemente, aplicando gerenciamento mais complexos.
- “Elimina” conhecimentos avançados em API Kubernetes (kubectl).

Operator SDK: Permite o desenvolvimento, empacotamento e testes de Operators baseando-se em sua experiência, sem o requerimento de conhecimentos sobre API Kubernetes.
Operator Lifecycle Management: Ajuda no processo de instalação, updates e gerenciamento do cluster K8s.
Operator Metering: Habilita relatórios de utilização de seus Operators e recursos Kubernetes

AWX Operator
------------

AWX Operator é atualmente o caminho recomendado para realizar a instalação do AWX.
Desenvolvido aplicando os requisitos/compliances do Framework Operator, conseguimos um processo de instalação “simplificado” e automatizado de nossa aplicação AWX.

Instalação AWX
------------

Para instalar o AWX, acesse: [Install AWX](https://github.com/HailsonJunior/ansible-awx-com-k3s-install/blob/main/install.md)
