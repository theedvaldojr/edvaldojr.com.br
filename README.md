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
