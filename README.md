# Segurança da informação
## Configurando Firewall via Terminal e acessando/bloqueando acesso

<p>Trabalharemos com VM's no Virtual Box</p>

<p>Obs: a distro Debian já está pré configurada pelo prof. (usuario, chave ssh)</p>

<p>1- importe a distro para Virtual Box</p>

![](imagens/importar-distro.jpeg)

![](imagens/importar-distro2.jpeg)

![](imagens/importar-distro3.jpeg)

<p>Agora faça o clone</p>
<p>Obs: o clone servirá para tarefas posteriores</p>

![](imagens/clonando.jpeg)

![](imagens/clonando2.jpeg)

![](imagens/clonando3.jpeg)

<p>2- Agora configura a rede da máquina principal no V. Box</p>

![](imagens/config-rede.jpeg)

![](imagens/config-rede2.jpeg)

![](imagens/config-rede3.jpeg)
<p>Agora selecione a maquina que deseja iniciar, após selecionar, uma das formas de iniciar é clicando na seta verde iniciar</p> 

![](imagens/iniciar-maquina.jpeg)

<p>Após iniciar a máquina informe o login e senha</p>

![](imagens/login.png)
<p>Consulte o IP através do comando ip -4 a</p>

![](imagens/consultar-ip.png)

<p>Agora com o IP da minha rede interna, posso acessar usuario Aluno com servidor de ssh previamente instalado</p>

<p>Para isso utilizarei o prompt de comado (CMD)</p>

![](imagens/acesso-ssh.png)

<p>Agora invoque o super usuário através do comando Su - </p>

![](imagens/invoque-super-user.png)

<p>Agora liste as regras existentes através do cmando iptables -L </p>

![](imagens/listar-as-regras.png)

<p>Mudando a politica padrão do INPUT através do comando iptables -P INPUT DROP </p>

![](imagens/xi-perdi-acesso.png)

<p>Xi perdi acesso! isso porque travei o input por ter infligido regra da política padrão. </p>

<p>Listando a política vigente (iptables -L)</p>

![](imagens/listar-regras2.png)

<p>iptables -F muda as regras para a politica padrão</p>
<p>Agora vou tentar mudar a politica padrão través do comando iptables -F que limpa as regras "Flush" retornando para política padrão e em seguida enviando o comando iptables -L para listar novamente os dados da tabela </p>

![](imagens/tentando-mudar-politica.png)

<p>Notou que nada foi modificado? isso porque a listagem anterior já é a política padrão </p>

<p>Portanto para que possa mudar as regras da política padrão use o comando iptables -P INPUT ACCEPT (aceitando input)</p>

<p>em seguida liste os dados da tabela com o comando:  iptables -L</p>

![](imagens/input-accept.png)

<p>Note na imagem a seguir que agora tenho acesso ssh aluno@... liberado</p>

![](imagens/acesso-liberado.png)

<p>Agora logar como root novamente e efetuar o comando  (iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT) com esse comando o Firewall aceitará todas as conexões validas já estabelecidas </p>

![](imagens/accept-conexion-estab.png)


<p>Agora vou "Dropar a conexão" com o comando iptables -P INPUT DROP e vou listar a tabela com o comando iptables -L com isso mudei minha política para drop e não perdi a conexão</p>

![](imagens/input-drop.png)

<p>Agora que a conexão esta "Dropada" conforme aponta a seta 02, vou tentar acesso! através do Powershell</p


![](imagens/tentativa-acesso-ssh.png)


<p>Note que reportou time out, isso porque havia acabado de bloquear acesso através do comando DROP</p

<p>Agora vou liberar o acesso INPUT para o IP da minha máquina que reponde no IP 192.168.15.4 conforme destacado na figura abaixo</p>

![](imagens/ip-minha-maquina.png)

<p>O acesso será permitido conforme as especificações da seta 03 da imagem abaixo, e para que isso aconteça utilizarei o comando iptables -A INPUT -p tcp -s 192.168.15.4 -i enp03 --drop 22 -j ACCEPT </p>
<p>iptables -L para listar</p>

![](imagens/permita-o-ip.png)


<p>Confirmando a liberação de acesso no Powershell</p>

![](imagens/ip-acesso-liberado.png)

<p>Agora posso novamente deletar a regra que permitia o acesso, através do comando iptables -D INPUT 2</p>

![](imagens/drop-acesso-ip-novamente.png)

<p>Note que reportará time out</p>

<!--[](imagens/time-out.png)-->

![](imagens/correcao-acesso-bloqueado.png)

<p>Agora vamos subir uma nova interface, a interface enp0s8 </p>

<p>Para isso utilizaremos o editor de texto Unix VI, que deverá ser aberto no caminho para interfaces: vi /etc/network/interfaces</p>

<p>Informe os dados da nova interface, conforme destacado</p>

![](imagens/nova-interface2.png)

<p>após isso tecle: "esc" depois ":" depois "wq", para salvar e sair do editor </p>
<p>Agora é só "rebootar" a maquina e verificar se a nova interface foi criada</p>

![](imagens/reboot.png)

<p>Agora consulte as interfaces existentes através do comando ip -4 a</p>
<p>note que agora temos a interface enp0s8</p>

![](imagens/interface-enp0s8.png)

<p>Com privilégio su - adicionarei um novo IP (192.168.0.16/24)</p>

<!--![](imagens/note-o-ip.png) -->

![](imagens/novo-ip2.png)

<p>Poderia apenas ter modificado o IP 10.0.1.10/24, portanto farei a exclusão do mesmo utilizando o comando ip addr del 10.0.1.10/24 dev enp0s8</p>

<p>Note que na nova consulta o IP 10.0.1.10/24 já não existe</p>

![](imagens/exclui-ip.png)


<p>Agora vou configurar para que qualquer máquina ou dispositivo que esteja na minha rede local possa acessar o ssh da máquina virtual</p>

<p>Obs: apenas configurar por enquanto, sendo que o acesso só será possível após configuar outros parametros que farei mais a diante (NAT e tals) </p>

<!-- <p>A rede 192.168.0.0/24 acesse a porta 22 da rede 10.0.1.0/24</p> -->
<p>Configurar para que a rede 192.168.15.4/24 acesse porta 22 da rede 192.168.0.16/24</p>
<p>Para fazer sentido vou "dropar" a cadeia FORWARD com o comando iptables -P FORWARD DROP</p>

![](imagens/drop-p-fazer-sentido4.png)


<p>Agora sim vou liberar acesso para qualquer dispositivo da rede local, vou usar o comando iptables -A FORWARD -p tcp -s 192.168.0.0/24 -d 192.168.0.16/24 --dport 22 -j ACCEPT</p>

![](imagens/liberando-acesso.png)



