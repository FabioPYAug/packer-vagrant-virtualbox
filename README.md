# Packer-Vagrant: Estrutura

Este repositório possui toda a estrutura para fazer o provisionamento do projeto. Será feita uma arquitetura completa com entrega contínua e ArgoCD. Para fazer a aplicação do projeto você deverá realizar os seguintes passos:

## Índice
- [Passo a Passo](https://github.com/FabioPYAug/packer-vagrant-virtualbox/new/main?filename=README.md#passo-a-passo)
  - [Clonar Repositório](https://github.com/FabioPYAug/packer-vagrant-virtualbox/new/main?filename=README.md#clonar-reposit%C3%B3rio)
  - [Arquivos binários](https://github.com/FabioPYAug/packer-vagrant-virtualbox/new/main?filename=README.md#arquivos-bin%C3%A1rios)
  - [Conexão com o SSH](https://github.com/FabioPYAug/packer-vagrant-virtualbox/new/main?filename=README.md#conex%C3%A3o-com-o-ssh)
  - [Ansible](https://github.com/FabioPYAug/packer-vagrant-virtualbox/new/main?filename=README.md#ansible)
  - [ArgoCD](https://github.com/FabioPYAug/packer-vagrant-virtualbox/new/main?filename=README.md#argocd)
- [Documentação](https://github.com/FabioPYAug/packer-vagrant-virtualbox/new/main?filename=README.md#documenta%C3%A7%C3%A3o)
- [Frontend](https://github.com/victtows/Front-Projetodevops.git)
- [Backend](https://github.com/FabioPYAug/AnonyQuest.git)
- [Banco de Dados](https://github.com/Otaviopax/MySQL-Projetodevops.git)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Passo a Passo

### Clonar Repositório
Clone o repositório utilizando o comando abaixo:
~~~~
git clone https://github.com/FabioPYAug/packer-vagrant-virtualbox.git
~~~~

### Arquivos binários

Para executar os comandos do vagrant e packer será necessário baixar dois arquivos binários. Você deverá entrar nesses dois links abaixo.
- Vagrant: https://developer.hashicorp.com/vagrant/install
- Packer: https://developer.hashicorp.com/packer/install

Após baixar os arquivos, você deverá extrair e colocar eles dentro de sua pasta clonada (pasta desse projeto). Agora, você deverá executar os seguintes comandos na (pasta do projeto dentro do VSCode) em sequência:
~~~
packer init .

packer plugin install github.com/hashicorp/virtualbox

packer plugin install github.com/hashicorp/vagrant

packer build debian.json

vagrant box add debian12 debian12.box

vagrant up
~~~

Caso algum desses comando NÃO funcione, tente colocar "./" antes do comando, como no exemplo abaixo:
~~~
./packer init .
~~~

Durante o comando "packer build debian.json" o próprio código irá abrir o seu VM Oracle e quando isso acontecer você NÃO deve selecionar nenhuma opção na VM, o código fará isso SOZINHO e só terminará quando no seu terminal não aparecer mais nenhuma linha de comando rodando.

### Conexão com o SSH
Assim que a VM estiver ligada, você deverá abrir ela com o usuário "vagrant" e senha "vagrant" e pegar o IP da máquina. Com o IP dela, você deverá substituir o IP que estará dentro do arquivo hosts na pasta ansbile. Substitua pelo IP da sua máquina.

Depois de ter feito isso, você irá utilizar os seguintes comandos na sua máquina hospedeira:
~~~
ssh-keygen
~~~

Esse comando irá perguntar sobre algumas configurações que não é necessário responder e no final irá dar um caminho, algo parecido com isso "/home/fabio.augusto/.ssh/id_rsa". Esse caminho será usado nesse próximo comando que também será usado na máquina hospedeira:
~~~
ssh-copy-id -i <caminho> vagrant@<ip da maquina virtual>
~~~

Além disso, você irá copiar aquele mesmo caminho e colar dentro da pasta hosts novamente. Irá subsituir o caminho dessa variável: ***ansible_ssh_private_key_file=/home/fabio.augusto/.ssh/id_rsa***

### Ansible
Agora, iremos utilizar o Ansible para baixar e configurar nossa máquina. Você deverá entrar dentro da pasta "ansible" do projeto. Lá, você utilizará os seguintes comandos:
~~~
ansible-playbook -i hosts install_nginx.yml

ansible-playbook -i hosts install_docker.yml

ansible-playbook -i hosts install_kind.yml

ansible-playbook -i hosts install_kubectl.yml

ansible-playbook -i hosts raise_nodes.yml

ansible-playbook -i hosts install_argocd.yml
~~~

Com isso, o ArgoCD já estará rodando e caso você desligue a máquina e queria rodar novamente o ArgoCD, você poderá o comando abaixo sem precisar baixar ele novamente:
~~~
ansible-playbook -i hosts start_argocd.yml
~~~

### ArgoCD
Agora, ao utilizar o comando start_argocd.yml, o ArgoCD começará a ser rodado e você deverá utilizar "https://<ip da máquina>:8080/" em uma página web. Lá, você irá criar uma nova aplicação (no total são três) e nelas irá preencher os valores com o link do github do projeto (front, back e banco). Após isso irá fazer o sync e caso tudo esteja verde deu tudo certo!
<img width="1170" height="660" alt="image" src="https://github.com/user-attachments/assets/fa1cdb5f-4099-4ce9-8f6a-3481eca98abf" />
<img width="1223" height="743" alt="image" src="https://github.com/user-attachments/assets/30cd4cd0-a062-44bc-a410-7a27373c2d7f" />

### Hospedar IP
Nessa parte você irá abrir o terminal com o ssh utilizando o comando:
~~~
ssh vagrant@<seu ip>
~~~
Agora, você irá rodar o comando para ver os seus serviços:
~~~
kubectl get svc
~~~
Esse comando irá retornar as portas e nomes de cada serviço e com eles, você irá dividir o terminal para mais três e rodar esse comando três vezes para hospedar o IP:
~~~
kubectl port-forward svc/<nome do serviço> --address 0.0.0.0 <porta>:<porta>
~~~
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Documentação
[Documentação](https://docs.google.com/document/d/1yj_hls5wxS2QRv2Vp7o4Nbvax-kYS5KR8sc2TPcUDpw/edit?usp=sharing)
### Usados?
- Packer;
- Vagrant;
- Ansible;
- Kubectl e Kind;
- Nginx;
- ArgoCD;
- Docker.

### Packer
O Packer foi escolhido para o projeto pois ele cria uma imagem base, padronizando os ambientes criados.

### Vagrant
O Vagrant foi escolhido pois é uma ferramenta prática para o gerenciamento das VMs criadas utilizando a própria imagem criada pelo Packer.

### Ansible
Provisiona as instalações para a VM, preparando o ambiente para o que for necessário no projeto.

### Kubectl e Kind
São usados para gerenciar os recursos kubernetes, interagindo com os Clusters, podendo até criar alguns para testes rápidos.

### Nginx
Proviciona o IP para serviços web (usado para o Frontend)

### ArgoCD
Gerenciar os deployments no Cluster com base no repositório no Github.

### Docker
Criar containers para a aplicação. Além de ser necessário para rodar serviços de forma isolada.
