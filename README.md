<div class="doacoes">
    <h3>Doações | Donations</h3>
    <p>
	<b>
	    Chave PIX (E-mail): contato@edvaldojr.com.br<br>
	    QR CODE:<br>
	    <a href="https://nubank.com.br/pagar/gz7q/CVYBGtgikv" target="_blank">
                <img alt="" src="https://raw.githubusercontent.com/theedvaldojr/edvaldojr.com.br/main/assets/images/QRCode.png" height="173" width="172">
            </a>
	</b>
    </p>
</div>

<div class="redes-sociais">
    <h3>Redes sociais | Social media:</h3>
    <a href="https://www.instagram.com/edvaldojr.com.br" target="_blank">
        <img alt="" src="https://raw.githubusercontent.com/theedvaldojr/edvaldojr.com.br/main/assets/images/Instagram.png" height="40" width="40">
    </a>
    <a href="https://www.linkedin.com/in/edvaldojnr/" target="_blank">
        <img alt="" src="https://raw.githubusercontent.com/theedvaldojr/edvaldojr.com.br/main/assets/images/Linkedin.png" height="40" width="40">
    </a>
    <a href="https://github.com/theedvaldojr" target="_blank">
        <img alt="" src="https://raw.githubusercontent.com/theedvaldojr/edvaldojr.com.br/main/assets/images/GitHub.png" height="40" width="40">
    </a>
</div>

# Tutoriais | Tutorials

<details>

<summary>Ver todos</summary>

<a href="https://edvaldojr.com.br/#configurar-servidor-dns-bind9--configure-dns-server-bind9"><p>Configurar servidor DNS (Bind9) | Configure DNS server (Bind9)</p></a>
<a href="https://edvaldojr.com.br/#configurar-bloqueio-por-dns-rpz--configure-dns-block-rpz"><p>Configurar bloqueio por DNS (RPZ) | Configure DNS block (RPZ)</p></a>
<a href="https://edvaldojr.com.br/#redimensionar-disco-no-linux--resize-disk-in-linux"><p>Redimensionar disco no Linux | Resize disk in Linux</p></a>
<a href="https://edvaldojr.com.br/#instalar-zabbix--install-zabbix"><p>Instalar Zabbix | Install Zabbix</p></a>

</details>

## Configurar servidor DNS (Bind9) | Configure DNS server (Bind9)

Exemplo do meu laboratório:  
Example of my laboratory:

Minha rede **LAN** é **192.168.25.0/24**  
My network **LAN** is **192.168.25.0/24**

O endereço IP do **servidor DNS** ( bindserver's) é **192.168.25.3**  
The address IP of the **server DNS** (bindserver’s) is **192.168.25.3**

O endereço IP do **Zabbix** é **192.168.25.4**  
The address IP of **Zabbix** is **192.168.25.4**

Meu domínio é **edvaldojr.com.br**  
My domain is **edvaldojr.com.br**

-------

Acessar modo superuser:  
Acess mode superuser:  
```sudo su```

Realizar o update:  
Realize the update:  
```apt update```

Realizar o upgrade:  
Realize the upgrade:  
```apt upgrade```

Instalar o bind9 e suas dependencias:  
Install bind9 and your dependencies:  
```apt install bind9 bind9utils bind9-doc```

Verificar o status do bind9:  
Verify status of bind9:  
```systemctl status bind9```

Resultado esperado: **"Active: active (running)"**  
Expected result: **“Active: active (running)”**

-------

Configurações de rede:  
Network settings:  
```vi /etc/bind/named.conf.options```

```
acl LAN {
        192.168.25.0/24;
};
options {
    directory "/var/cache/bind";
    auth-nxdomain no;
    listen-on { any; };
    listen-on-v6 { any; };
    minimal-responses yes;
    allow-recursion { LAN; };
    allow-query-cache { LAN; };
    allow-query { any; };
    allow-transfer { none; };
    masterfile-format text;
};
```

Verificar se as configurações acima estão corretas:  
Verify if the above settings are correct:  
```named-checkconf /etc/bind/named.conf.options```

-------

Configurações de zonas:  
Zones settings:  
```vi /etc/bind/named.conf.local```

```
zone "edvaldojr.com.br" IN {
        type master;
        file "/etc/bind/zones/edvaldojr.com.br";
};
zone "25.168.192.in-addr.arpa" IN {
        type master;
        file "/etc/bind/zones/edvaldojr.com.br.rev";
};
```

Verificar se as configurações acima estão corretas:  
Verify if the above settings are correct:  
```named-checkconf /etc/bind/named.conf.local```

-------

Criar pasta de zones:  
Create zones folder:  
```mkdir /etc/bind/zones```

-------

Copiar o arquivo de zona:  
Copy zone archive:  
```cp /etc/bind/db.local /etc/bind/zones/edvaldojr.com.br```

Configurar a zona:  
Zone settings:  
```vi /etc/bind/zones/edvaldojr.com.br```

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     edvaldojr.com.br. root.edvaldojr.com.br. (
                               3        ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.edvaldojr.com.br.
;
ns1     IN      A       192.168.25.3
;
zabbix  IN      A       192.168.25.4
;
www     IN      A       200.189.123.64
@       IN      A       200.189.123.64
```

> Obs: O IP 200.189.123.64 foi retirado do site PIÁ (Paraná Inteligência Artificial) somente para testes, utilize o IP do seu site.  
> Obs: The IP 200.189.123.64 was removed of PIÁ (Paraná Inteligência Artificial) site only for tests, use the IP to your site.

> Obs 2: O número do serial deve ser aumentado em +1 quando salvo, no exemplo acima está em 3, assim que for salvo novamente, deve ser alterado para 4. A orientação serve para outros arquivos utilizados no tutorial.  
> Obs 2: The serial number should be increased by +1 when saved, in the example above is in 3, as soon as it is saved again, it must be changed for 4. The oriention serves for others files used in tutorial.

Verificar se as configurações acima estão corretas:  
Verify if the above settings are correct:  
```named-checkzone edvaldojr.com.br /etc/bind/zones/edvaldojr.com.br```

-------

Copiar o arquivo de zona reversa:  
Copy reverse zone archive:  
```cp /etc/bind/db.127 /etc/bind/zones/edvaldojr.com.br.rev```

Configurar a zona reversa:  
Reverse zone settings:  
```vi /etc/bind/zones/edvaldojr.com.br.rev```

```
;
; BIND reverse data file for local loopback interface
;
$TTL    604800
@       IN      SOA     edvaldojr.com.br. root.edvaldojr.com.br. (
                               2        ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.edvaldojr.com.br.
;
ns1     IN      A       192.168.25.3
;
3       IN      PTR     ns1.edvaldojr.com.br.
4       IN      PTR     zabbix.edvaldojr.com.br.
;
@       IN      PTR     www.edvaldojr.com.br.
@       IN      PTR     edvaldojr.com.br.
```

Verificar se as configurações acima estão corretas:  
Verify if the above settings are correct:  
```named-checkzone edvaldojr.com.br /etc/bind/zones/edvaldojr.com.br.rev```

-------

Reiniciar o serviço de bind9 para aplicar as configurações:  
Restart service bind9 for apply settings:  
```systemctl restart bind9```

-------

Teste nslookup:  
Test nslookup:  
```nslookup ns1.edvaldojr.com.br```

Resultado esperado:  
Expected result:  
```
edvaldojr@desktop:~$ nslookup ns1.edvaldojr.com.br  
Server:		127.0.0.53  
Address:	127.0.0.53#53  

Non-authoritative answer:  
Name:	ns1.edvaldojr.com.br  
Address: 192.168.25.3  
```

```nslookup zabbix.edvaldojr.com.br```  
Resultado esperado:  
Expected result:  
```
edvaldojr@desktop:~$ nslookup zabbix.edvaldojr.com.br  
Server:		127.0.0.53  
Address:	127.0.0.53#53  

Non-authoritative answer:  
Name:	zabbix.edvaldojr.com.br  
Address: 192.168.25.4  
```

```nslookup edvaldojr.com.br```  
Resultado esperado:  
Expected result:  
```
edvaldojr@desktop:~$ nslookup edvaldojr.com.br  
Server:		127.0.0.53  
Address:	127.0.0.53#53  

Non-authoritative answer:  
Name:	edvaldojr.com.br  
Address: 200.189.123.64  
```

-------

## Configurar bloqueio por DNS (RPZ) | Configure DNS block (RPZ)

Acessar modo superusuario:  
Access mode superuser:  
```sudo su```

Configuração de zonas:  
Zones settings:  
```vi /etc/bind/named.conf.local```

```
zone "rpz.zone" {
    type master;
    file "/var/cache/bind/rpz/db.rpz.zone";
    allow-query {none;};
};
```

Verificar se as configurações acima estão corretas:  
Verify if the above settings are correct:  
```named-checkconf /etc/bind/named.conf.local```

-------

Configurações de redes:  
Network settings:  
```vi /etc/bind/named.conf.options```

```
options {
    …
    response-policy {
	zone "rpz.zone";
    };
    …
};
```

Criar pasta de rpz:  
Create rpz folder:  
```mkdir /var/cache/bind/rpz/```

Crie um link simbólico:  
Create symbolic link:  
```ln -s /var/cache/bind/rpz/ /etc/bind/```

Editar as configuração de zonas:  
Edit zones settings:  
```vi /var/cache/bind/rpz/db.rpz.zone```

```
$TTL 1H
@       IN      SOA LOCALHOST. ns1.edvaldojr.com.br. (
                2               ; Serial  
                1h              ; Refresh
                15m             ; Retry
                30d             ; Expire 
                2h              ; Negative Cache TTL
        )
        NS  ns1.edvaldojr.com.br.


facebook.com    IN CNAME .
*.facebook.com  IN CNAME .
instagram.com   IN CNAME .
*.instagram.com IN CNAME .
netflix.com     IN CNAME .
*.netflix.com   IN CNAME .
```

> Obs: O número do serial deve ser aumentado em +1 quando salvo, no exemplo acima está em 2, assim que for salvo novamente, deve ser alterado para 3.  
> Obs: The serial number should be increased by +1 when saved, in the example above is in 2, as soon as it is saved again, it must be changed for 3.

-------

Reiniciar o serviço de bind9 para aplicar as configurações:  
Restart service bind9 for apply settings:  
```systemctl restart bind9```

-------

```nslookup facebook.com```  
Resultado esperado:  
Expected result:  
```
edvaldojr@desktop:~$ nslookup facebook.com
Server:		127.0.0.53
Address:	127.0.0.53#53

** server can't find facebook.com: NXDOMAIN 
```

```nslookup instagram.com```  
Resultado esperado:  
Expected result:  
```
edvaldojr@desktop:~$ nslookup instagram.com
Server:		127.0.0.53
Address:	127.0.0.53#53

** server can't find instagram.com: NXDOMAIN
```

```nslookup netflix.com```  
Resultado esperado:  
Expected result:  
```
edvaldojr@desktop:~$ nslookup netflix.com
Server:		127.0.0.53
Address:	127.0.0.53#53

** server can't find netflix.com: NXDOMAIN
```

> Obs 2: Para o perfeito funcionamento, deve se utilizar 2 DNS locais com as mesmas configurações ou setar somente 1 DNS local na maquina ou servidor DHCP. Deixando 1 DNS local e 1 DNS público, a requisição irá passar pelo DNS local e irá consultar no DNS público, causando o mal funcionamento do bloqueio.
> Obs 2: For perfect operation, must if utility 2 Local DNS with the same settings or set only 1 local DNS on the machine or DHCP server. Leaving 1 local DNS and 1 public DNS, the requestion will pass by local DNS and will query the public DNS, causing the malfunction of block.

-------

## Instalar Zabbix | Install Zabbix

Exemplo do meu laboratório:  
Example of my laboratory:

Minha rede **LAN** é **192.168.25.0/24**  
My network **LAN** is **192.168.25.0/24**

O endereço IP do **Zabbix** é **192.168.25.4**  
The address IP of **Zabbix** is **192.168.25.4**

Meu domínio é **edvaldojr.com.br**  
My domain is **edvaldojr.com.br**

Versão: **Zabbix 6.4**  
Version: **Zabbix 6.4**

S.O.: **Ubuntu Server 22.04**  
O.S.: **Ubuntu Server 22.04**

-------

Instalando e atualizando o repositório Zabbix:  
Installing and updating the Zabbix repository:

Acessar modo superuser:  
Acess mode superuser:  
```sudo su```

Realizar o update:  
Realize the update:  
```apt update```

Realizar o upgrade:  
Realize the upgrade:  
```apt upgrade```

Baixe o repositório Zabbix:  
Download the Zabbix repository:  
```wget https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu22.04_all.deb```

Instale o pacote Zabbix:  
Install Zabbix package:  
```dpkg -i zabbix-release_6.4-1+ubuntu22.04_all.deb```

Realizar o update:  
Realize the update:  
```apt update```

Instalando o Zabbix Server, FrontEnd e Agent:  
Install Zabbix Server, FrontEnd and Agent  
```apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent```

Instalando o MySQL server:  
Install MySQL server:  
```apt install mysql-server```

Iniciar SQL:  
Start SQL:  
```systemctl start mysql```

Verificar o status do mysql:  
Verify status of mysql:  
```systemctl status mysql```

Resultado esperado: **Active: active (running)**  
Expected result: **Active: active (running)**

Criando o banco de dados inicial:  
Create the initial database:

> No lugar de "Password123", insira uma senha segura!  
> In place of "Password123", enter a strong password!

```
mysql
mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;
mysql> create user zabbix@localhost identified by 'Password123';
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> set global log_bin_trust_function_creators = 1;
mysql> quit;
```

No host do servidor Zabbix, importe o esquema e os dados iniciais. Você será solicitado a inserir sua senha recém-criada:  
On Zabbix server host import initial schema and data. You will be prompted to enter your newly created password:  
```zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix```

> Insira a mesma senha criada na criação do bando de dados.  
> Enter the same password created in creating the database.  
```
Insira sua senha: Password123
Enter password: Password123
```

Desabilite a opção “log_bin_trust_function_creators” depois de importar o esquema de banco de dados:  
Disable log_bin_trust_function_creators option after importing database schema:  
```
mysql
mysql> set global log_bin_trust_function_creators = 0;
mysql> quit;
```

Configure o banco de dados do servidor Zabbix:  
Configure the database for Zabbix server:  
```vim /etc/zabbix/zabbix_server.conf```

> Insira a mesma senha criada na criação do bando de dados.  
> Enter the same password created in creating the database.  
```
DBPassword=Password123
```

Inicie o servidor Zabbix e o agente de processos:  
Start Zabbix server and agent processes:  
```
systemctl restart zabbix-server zabbix-agent apache2
systemctl enable zabbix-server zabbix-agent apache2
```

Gere os arquivos de localização en_US e pt_BR:  
Generate the files of en_US and pt_BR location:  
```
locale-gen en_US.UTF-8
locale-gen pt_BR.UTF-8
```

Execute o comando abaixo:  
Run command below:  
```dpkg-reconfigure locales```

Irá abrir uma janela com o titulo “Locales a serem gerados”, selecione o idioma conforme sua necessidade, neste caso, irei selecionar os 3 abaixo:  
A window will open with the title “Locales to be generated”, select the language according to your need, in this case, i will select the 3 below:  
```
en_US.UTF-8 UTF-8
pt_BR.UTF-8 UTF-8
pt_PT.UTF-8 UTF-8
```

Irá abrir uma janela com o título “Locale padrão para o ambiente do sistema:”, selecione o idioma conforme sua necessidade, nesse caso irei selecionar o “pt_BR.UTF-8”.  
A window will open tich the title “Locale default to the system environment”, select the language according to your need, i will select the “pt_BR.UTF-8”.

> Obs: Altere o documento abaixo apenas se você quiser abrir o zabbix como http://localhost ou http://zabbix.seudominio.com.br!  
Se você continuar abrindo como http://localhost/zabbix ou http://zabbix.seudominio.com.br/zabbix, não altere o documento abaixo e prossiga com o tutorial:  
> Obs: Change the document below only if you wants open of zabbix as http://localhost or http://zabbix.yourdomain.com.br!  
If you continue open as http://localhost/zabbix or http://zabbix.seudominio.com.br/zabbix, do not change the document below and continue wich the tutorial:

Execute o comando abaixo e altere de  **DocumentRoot /var/www/html** para **DocumentRoot /usr/share/zabbix**:  
Run the command below and change of **DocumentRoot /var/www/html** for **DocumentRoot /usr/share/zabbix**:  
```vi /etc/apache2/sites-enabled/000-default.conf```

Reinicie o serviço do apache:  
Restart the apache service:  
```service apache2 restart```

Abra o navegador e digite o endereço do zabbix conforme as alterações acima, no meu caso:  
Open the browser and enter the address of zabbix according to changes above, in my case:  
**http://192.168.25.4/** or **http://zabbix.edvaldojr.com.br**

> Obs2: Se quiser configurar o domínio personalizado, é necessário a configuração do servidor DNS, segue o link para a configuração:  
> Obs2: If you want to configure the custom domain, it is necessary to configure DNS server, follow the link for the configuration:  
**https://edvaldojr.com.br/#configurar-servidor-dns-bind9--configure-dns-server-bind9**

Defina a “linguagem padrão” conforme sua necessidade:  
Define the “default language” according to your need:  
Default language: **Português Brasileiro (PT_BR)**

Na página de **Verificação de pré-requisitos**, clique em **Próximo passo**.  
In the page “Check prerequisites”, click in **Next step**.

Configura a conexão com o DB:  
Configure the connection to the DB:

> Em **Senha: Password123**, insira a mesma senha criada na criação do bando de dados.  
```
Tipo de banco de dados: MySQL
Host do banco de dados: localhost
Porta do banco de dados: 0
Nome do banco de dados: zabbix
Armazenar credenciais em: Texto puro
Usuário: zabbix
Senha: Password123
```

> In **Password: Password123**, enter the same password created in creating the database.  
```
Database type: MySQL
Database host: localhost
Database port: 0
Database name: zabbix
Store credentials in: Plain text
User: zabbix
Password: Password123
```

Configurações:  
Settings:  
> Selecione os dados conforme sua necessidade:  
> Select data as per to your need:  
```
Nome do servidor Zabbix: NomeDeTeste
Fuso horário padrão: (UTC-03:00) America/Sao_Paulo
Tema padrão: Escuro
```

```
Name Zabbix server: NomeDeTeste
Standard time zone: (UTC-03:00) America/Sao_Paulo
Default theme: Dark
```

Insira os dados abaixo no primeiro login, lembre-se de trocar a senha do usuário após o primeiro acesso:  
Enter the data below on first login, remember to change the user password after the first access:  
```
Login: Admin
Password: zabbix
```

-------

## Redimensionar disco no Linux | Resize disk in Linux

Acessar modo superusuario:  
Access mode superuser:  
```sudo su```

Identifique o nome do dispositivo que, por padrão, é /dev/sda e confirme o novo tamanho executando o comando:  
Identify the device name, by default, is /dev/sda and confirm the new size by running the command:  
```fdisk -l```

Crie uma nova partição primária:  
Create a new primary partition:  

Execute o comando:  
Run the command:  
```fdisk /dev/sda```

Pressione p para imprimir a tabela de partição para identificar o número de partições. Por padrão, existem duas: sda1 e sda2:  
Press p to print the partition table to identify the number of partitions. By default, there are two: sda1 and sda2:  
```Command (m for help): p```

Pressione n para criar uma nova partição primária:  
Press n to create a new primary partition:  
```Command (m for help): n```

Pressione 4 para o número de partições, dependendo da saída da impressão da tabela da partição:  
Press 4 for the number of partitions, depending on the partition table print output:  
```Partition number (4-128, default 4): 4```

Pressione Enter duas vezes:  
Press Enter twice:  
First sector (1258289152-3221225438, default 1258289152):  
Last sector, +/-sectors or +/-size{K,M,G,T,P} (1258289152-3221225438, default 3221225438):  
```Created a new partition 4 of type 'Linux filesystem' and of size 936 GiB.```

Pressione w para salvar as alterações na tabela de partição:  
Press w to save changes to the partition table:  
```Command (m for help): w```

Reinicie a máquina virtual.  
Restart the virtual machine.  

Execute este comando para verificar se as alterações foram salvas na tabela de partição e se a nova partição é do tipo 83:  
Run this command to verify that the changes were saved to the partition table and that new partition is type 83:  
```fdisk -l```

Execute este comando para converter a nova partição em um volume físico:  
Run this command to convert the new partition to a physical volume:  
```pvcreate /dev/sda4```

Execute este comando para estender o volume físico:  
Run this command to extend the physical volume:  
```vgextend ubuntu-vg /dev/sda4```

Execute este comando para verificar quantas extensões físicas estão disponíveis para o Grupo de Volume:  
Run this command to check how many physical extents are available for the Volume Group:  
```vgdisplay ubuntu-vg | grep "Free"```

Execute o seguinte comando para estender o Volume Lógico:  
Run the following command to extend the Logical Volume:  
```lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv```

Execute o seguinte comando para expandir o sistema de arquivos ext3 online, dentro do Volume Lógico:  
Run the following command to expand the ext3 file system online, inside of Logical Volume:  
```resize2fs /dev/ubuntu-vg/ubuntu-lv```

Execute o seguinte comando para verificar se o sistema de arquivos tem o novo espaço disponível:  
Run the following command to verify that the file system the new space available:  
```df -h /```
