# DNS SLAVE (BIND / NAMED)
O Slave é um serviço que "herda" as configurações do Master.

## Instalação
Toda a instalação é feita como **root**

### Debian
Instale o bind server com o comando:

`apt-get install bind9`

Criar um diretório onde vão ficar as suas zonas, digite o comando:

`mkdir /etc/bind/zones`

Edite o arquivo **/etc/bind/named.conf.local**, inclua uma linha em branco e após esta inclua as zonas, veja um exemplo em **Configurações** item **Zonas de DNS**.

Na configuração do DNS master altere o arquivo **/etc/bind/named.conf.options** e logo abaixo do encaminhamento de DNS coloque a linha abaixo:

`allow-transfer { localhost; IP_DO_MASTER; IP_DO_SLAVE; };`

Onde o **IP_DO_SLAVE** é o IP deste servidor.

Reinicie o serviço de DNS no master:

`service bind9 restart`

Se tudo der certo será criado o arquivo de host na pasta zones recém criada.

### CentOS
Instale o bind server e as ferramentas do bind com o comando:

` yum -y install bind bind-utils`

Dê a permissão para o named gravar no diretório **/var/named**, digite o comando:

` chown named.named /var/named`

Edite o arquivo **/etc/bind/named.conf.local**, inclua uma linha em branco e após esta inclua as zonas, veja um exemplo em **Configurações** item **Zonas de DNS**.

Habilite e inicie o serviço com os comandos:

```
 systemctl enable named
 systemctl start named
```

## Configurações
### Zonas de DNS
```
zone "home.lan" IN {
       type slave;
       file "/etc/bind/zones/home.lan.host";
       masters { IP_DO_MASTER; };       
};
```
Onde o **IP_DO_MASTER** é o IP do servidor onde está o DNS master.

## Testes
Testando o DNS, digite o comando:

`nslookup (NOME_DO_SERVER).home.lan IP_DO_MASTER`

Que dever retornar algo como:

```
Server:  IP_DO_SERVER
Address: IP_DO_SERVER#53

Name:   (NOME_DO_SERVER).home.lan
Address: IP_DO_MASTER
```

Onde: **NOME_DO_SERVER** é o nome do servidor e **IP_DO_MASTER** é o IP do servidor onde está o DNS master.

Edite o arquivo **/etc/resolv.conf** e coloque na primeira linha conforme exemplo abaixo:

```
nameserver IP_DO_MASTER
nameserver 8.8.8.8
```
Após editar o arquivo, salve-o e feche-o.

Onde: **IP_DO_MASTER** é o IP do servidor o DNS master

Obs: Se este arquivo é modificado a cada inicialização proteja-o dando o comando:

`chattr +i /etc/resolv.conf`

Obs2: Para tirar a proteção altere de **+i** para **-i**
Obs3: Se for feito isto na consulta não precisa colocar o IP do servidor o DNS master.
