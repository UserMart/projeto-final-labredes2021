# <p align="center"> Instituto Federal de Alagoas - Campus Arapiraca </p>
### Prof. Alaelson Jatobá

### Alunos: José Martins, Jonathan Candido, Marcos Vinicius, Milca Elen e Matheus de Melo

## Sumário

## 1. Introdução
Projeto realizado pelo o grupo 5 (cinco) para a matéria de Serviços de Redes em conjunto com
a outra matéria Projeto e Infraestrutura de Redes, que tem por finalidade colocar em prática toda a teoria
vista em aulas síncronas. Esse projeto visa conhecimentos básicos em serviços de redes como: DNS master, slave, samba,
configuração de gateway e até mesmo trabalhar com serviços web.
Toda a implementação fica a cargo dos alunos que com muito esforço e dedicação realizaram esse projeto!


## 2. Definições Iniciais
Tabela 1: IP's
### Definições de Rede
|Tipo|IP
|-|-
|Rede|10.9.24.0
|Máscara|255.255.255
|Gateway|10.9.24.1
|Broadcast|10.9.24.255
|ns1|10.9.24.108
|ns2|10.9.24.109
|samba|10.9.24.114
|gw|10.9.24.117
|www|10.9.24.219
|bd|10.9.24.220


Tabela 2: Nomes das Vm's
```
------------------------------------------------------------------
|    Nome da VM     |                    NOME                     |
------------------------------------------------------------------
| Gateway (gw)      | gw.grupo5.turma924.ifalara.local            |                     
| Samba-SRV.        | samba.grupo5.turma924.ifalara.local         |
| NameServer1 (ns1) | ns1.grupo5.turma924.ifalara.local           |
| NameServer2 (ns2) | ns2.grupo5.turma924.ifalara.local           |
| FQDN WEB          | www.grupo5.turma924.ifalara.local	          |	
| FQDN BD           | bd.grupo5.turma924.ifalara.local            |
------------------------------------------------------------------

```

## 3. Realize a configuração estática do DNS na interface de rede nas VM's: ns1, ns2, samba e gateway;
*  Na maquina ns1 faça as seguintes configurações;

-  Depois edite o arquivo  ***00-installer-config.yaml*** com o seguinte comando:

```bash
$ sudo nano /etc/netplan/00-installer-config.yaml
```

-  Adicione as linhas para a configuração estática do IP. [Baixe o arquivo 00-installer-config.yaml](https://github.com/alaelson/labredes2020/blob/master/network/interface-config/00-installer-config.yaml)

```
network:
    ethernets:
        ens160:                           # nome da interface que está sendo configurada. Verifique com o comando 'ifconfig -a'
            addresses: [10.9.24.108/24]   # IP e Máscara do Host. Aqui é só um exemplo, tenha certeza do IP do seu host, ou perderá o acesso remoto.
            gateway4: 10.9.24.1           # IP do Gateway, Aqui é só um exemplo, tenha certeza do IP do seu gateway, ou perderá o acesso remoto.
            dhcp4: false                  # dhcp4 false -> cliente DHCP está desabilitado, logo o utilizará o IP do campo 'addresses'
            nameservers:
                addresses:
                   - 10.9.24.108              # IP do servidor de nomes 1, neste caso é o IP ns1.
                   - 10.9.24.109              # IP do servidor de nomes 2, neste caso é outro IP ns2.
                search: [grupo5.turma924.ifalara.local]     # identificação do domínio, [grupo5.turma924.ifalara.local] é apenas um exemplo.
        ens192:
            addresses: [192.168.24.35/29]

    version: 2
```
-  Após salvar o arquivo, aplique as novas configurações, com o **netplan apply**. Depois veja a configuração das interfaces com ****ifconfig -a***

```bash
$ sudo netplan apply
$ ifconfig -a
```

*  Na maquina ns2 faça as seguintes configurações;

-  Depois edite o arquivo  ***00-installer-config.yaml*** com o seguinte comando:

```bash
$ sudo nano /etc/netplan/00-installer-config.yaml
```

-  Adicione as linhas para a configuração estática do IP. [Baixe o arquivo 00-installer-config.yaml](https://github.com/alaelson/labredes2020/blob/master/network/interface-config/00-installer-config.yaml)

```
network:
    ethernets:
        ens160:                           # nome da interface que está sendo configurada. Verifique com o comando 'ifconfig -a'
            addresses: [10.9.24.109/24]  # IP e Máscara do Host. Aqui é só um exemplo, tenha certeza do IP do seu host, ou perderá o acesso remoto.
            gateway4: 10.9.24.1           # IP do Gateway, Aqui é só um exemplo, tenha certeza do IP do seu gateway, ou perderá o acesso remoto.
            dhcp4: false                  # dhcp4 false -> cliente DHCP está desabilitado, logo o utilizará o IP do campo 'addresses'
            nameservers:
                addresses:
                   - 10.9.24.108              # IP do servidor de nomes 1, neste caso é o IP ns1.
                   - 10.9.24.109              # IP do servidor de nomes 2, neste caso é outro IP ns2.
                search: [grupo5.turma924.ifalara.local]     # identificação do domínio, [grupo5.turma924.ifalara.local] é apenas um exemplo.
        ens192:
            addresses: [192.168.24.36/29]

    version: 2
```
-  Após salvar o arquivo, aplique as novas configurações, com o **netplan apply**. Depois veja a configuração das interfaces com ****ifconfig -a***

```bash
$ sudo netplan apply
$ ifconfig -a
```

*  Na maquina samba faça as seguintes configurações;

-  Depois edite o arquivo  ***00-installer-config.yaml*** com o seguinte comando:

```bash
$ sudo nano /etc/netplan/00-installer-config.yaml
```

-  Adicione as linhas para a configuração estática do IP. [Baixe o arquivo 00-installer-config.yaml](https://github.com/alaelson/labredes2020/blob/master/network/interface-config/00-installer-config.yaml)

```
network:
    ethernets:
        ens160:                           # nome da interface que está sendo configurada. Verifique com o comando 'ifconfig -a'
            addresses: [10.9.24.114/24]   # IP e Máscara do Host. Aqui é só um exemplo, tenha certeza do IP do seu host, ou perderá o acesso remoto.
            gateway4: 10.9.24.1           # IP do Gateway, Aqui é só um exemplo, tenha certeza do IP do seu gateway, ou perderá o acesso remoto.
            dhcp4: false                  # dhcp4 false -> cliente DHCP está desabilitado, logo o utilizará o IP do campo 'addresses'
            nameservers:
                addresses:
                   - 10.9.24.108              # IP do servidor de nomes 1, neste caso é o IP ns1.
                   - 10.9.24.109              # IP do servidor de nomes 2, neste caso é outro IP ns2.
                search: [grupo5.turma924.ifalara.local]     # identificação do domínio, [grupo5.turma924.ifalara.local] é apenas um exemplo.
        ens192:
            addresses: [192.168.24.34/29]

    version: 2
```
-  Após salvar o arquivo, aplique as novas configurações, com o **netplan apply**. Depois veja a configuração das interfaces com ****ifconfig -a***

```bash
$ sudo netplan apply
$ ifconfig -a
```

*  Na maquina gateway faça as seguintes configurações;

-  Depois edite o arquivo  ***00-installer-config.yaml*** com o seguinte comando:

```bash
$ sudo nano /etc/netplan/00-installer-config.yaml
```

-  Adicione as linhas para a configuração estática do IP. [Baixe o arquivo 00-installer-config.yaml](https://github.com/alaelson/labredes2020/blob/master/network/interface-config/00-installer-config.yaml)

```
network:
    ethernets:
        ens160:                           # nome da interface que está sendo configurada. Verifique com o comando 'ifconfig -a'
            addresses: [10.9.24.117/24]  # IP e Máscara do Host. Aqui é só um exemplo, tenha certeza do IP do seu host, ou perderá o acesso remoto.
            gateway4: 10.9.24.1           # IP do Gateway, Aqui é só um exemplo, tenha certeza do IP do seu gateway, ou perderá o acesso remoto.
            dhcp4: false                  # dhcp4 false -> cliente DHCP está desabilitado, logo o utilizará o IP do campo 'addresses'
            nameservers:
                addresses:
                   - 10.9.24.108              # IP do servidor de nomes 1, neste caso é o IP ns1.
                   - 10.9.24.109              # IP do servidor de nomes 2, neste caso é outro IP ns2.
                search: [grupo5.turma924.ifalara.local]     # identificação do domínio, [grupo5.turma924.ifalara.local] é apenas um exemplo.
        ens192:
            addresses: [192.168.24.33/29]

    version: 2
```
-  Após salvar o arquivo, aplique as novas configurações, com o **netplan apply**. Depois veja a configuração das interfaces com ****ifconfig -a***

```bash
$ sudo netplan apply
$ ifconfig -a
```

# 4. Implementação dos Serivços de Rede (Cada serviço uma sessão)
   ## *4.2 Confligurando o DNS Master:*
   
### Instalação necessária do Bind9 
   * Vale ressaltar que o BIND9 é a aplicação de DNS que roda no servidor.
   * Para instalar o bind9 de forma fácil é via comando:
```bash
$ sudo apt-get install bind9 dnsutils bind9-doc 
```
   * Verifique o status do serviço:
```bash
$ sudo systemctl status bind9
```
   * Se não estiver rodando:
```bash
$ sudo systemctl enable bind9
```



## Diretórios do bind
   * Os arquivos do bind ficam na no diretório **/etc/bind**. 
```bash
$ ls /etc/bind
```
```
bind.keys  
db.0  
db.127  
db.255  
db.empty 
db.local
named.conf  
named.conf.default-zones 
named.conf.local 
named.conf.options 
rndc.key  
zones  
zones.rfc1918

```

### Zonas
   * As zonas são especificadas em arquivos **db**. Vamos criar um diretório para armazendar os arquivos de zonas, que sera o diretório ***/etc/bind/zones***  
```bash
$ sudo mkdir /etc/bind/zones
```

#### Criar arquivos db
   * Criar o arquivo **db** no diretório ***/etc/bind/zones***. 
   * Os arquivos **db** são bancos de dados de resolução de nomes, ou seja, quando se sabe o nome da máquina mas não se conhece o IP. Cada zona no DNS deve ter seu próprio arquivo **db**, por exemplo: a zona *meusite.com.br* terá o arquivo **db.meusite.com.br**, já a zona *outrosite.net* terá o arquivo **db.outrosite.net**. 
   * No nosso caso o domínio/zona local será grupo5.turma924.ifalara.local. Assim o arquivo db será db.labredes.ifalarapiraca.local
   
##### zona direta
   * o arquivo db.grupo5.turma924.ifalara.local conterá os nomes das máquinas do domínio grupo5.turma924.ifalara.local
   * Para isso faremos uma cópia do arquivo /etc/bind/db.empty
```bash
$ sudo cp /etc/bind/db.empty /etc/bind/zones/db.grupo5.turma924.ifalara.local 
```

##### zona reversa
   * Utilizado quando não se conhece o IP mas sabe-se o nome do host.
   * vamos criar a zona reversa a partir do arquivo /etc/bind/db.127
```bash
  $ sudo cp /etc/bind/db.127 /etc/bind/zones/db.10.9.24.rev
```

   * Assim, o arquivo **db.10.9.24.rev** conterá a zona reversa da rede 10.9.24.0. 

   
### Editar arquivos db:

   #### zona direta: db.grupo5.turma924.ifalara.local
   * edite o arquivo  **db.grupo5.turma924.ifalara.local** para adcionar as informações do seu domínio
      
```bash   
    $ sudo nano db.grupo5.turma924.ifalara.local
```
---
```

; BIND data file for internal network
;
$ORIGIN grupo5.turma924.ifalara.local.
$TTL    3h
@       IN      SOA     ns1.grupo5.turma924.ifalara.local. root.turma924.ifalara.local. (
                              1         ; Serial
                              3h        ; Refresh
                              1h        ; Retry
                              1w        ; Expire
                              1h )      ; Negative Cache TTL
;nameservers
@       IN      NS      ns1.grupo5.turma924.ifalara.local.
@       IN      NS      ns2.grupo5.turma924.ifalara.local.
;hosts
ns1.grupo5.turma924.ifalara.local.        IN    A       10.9.24.108
ns2.grupo5.turma924.ifalara.local.        IN    A       10.9.24.109
samba.grupo5.turma924.ifalara.local.      IN    A       10.9.24.114
gw.grupo5.turma924.ifalara.local.         IN    A       10.9.24.117
www.grupo5.turma924.ifalara.local.        IN    A       10.9.24.219
bd.grupo5.turma924.ifalara.local.         IN    A       10.9.24.220

```

---
   #### zona reversa: db.10.9.24.rev
   * edite o arquivo **db.10.9.24.rev** para adcionar as informações da zona reversa
   
---
```
;
; BIND data file for internal network
;
;ORIGIN grupo5.turma924.ifalara.local.
$TTL    60
@       IN      SOA     ns1.grupo5.turma924.ifalara.local. root.turma924.ifalara.local. (
                              2         ; Serial
                              3h        ; Refresh
                              1h        ; Retry
                              1w        ; Expire
                              1h )      ; Negative Cache TTL
; name servers
@       IN      NS      ns1.grupo5.turma924.ifalara.local.
@       IN      NS      ns2.grupo5.turma924.ifalara.local.

; PTR Records
108      IN      PTR     ns1.grupo5.turma924.ifalara.local.       ;10.9.24.108
109      IN      PTR     ns2.grupo5.turma924.ifalara.local.       ;10.9.24.109
117      IN      PTR     samba.grupo5.turma924.ifalara.local.     ;10.9.24.114
114      IN      PTR     gw.grupo5.turma924.ifalara.local.        ;10.9.24.117
219      IN      PTR     www.grupo5.turma924.ifalara.local        ;10.9.24.219
220      IN      PTR     bd.grupo5.turma924.ifalara.local         ;10.9.24.220

```
---

### Configuração do named.conf.local
   * Para ativar as zonas descritas nos arquivos **db** deve-se editar o arquivo de configuracão do bind para informar onde eles foram salvos. As zonas são adicionadas em **/etc/bind/named.conf.local**.
   
```bash
$ sudo nano /etc/bind/named.conf.local
```
---
```
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "grupo5.turma924.ifalara.local" {
        type master;
        file "/etc/bind/zones/db.grupo5.turma924.ifalara.local";
        allow-transfer{ 10.9.24.109; };
        allow-query{any;};
};

zone "24.9.10.in-addr.arpa" IN {
        type master;
        file "/etc/bind/zones/db.10.9.24.rev";
        allow-transfer{ 10.9.24.109; };
};
```
---

### Verificação de sintaxe 
   * Para checar a sintaxe de configuração do BIND deve-se executar o comando named-checkconf. Este script checa os arquivos /etc/bind/named.conf.local.*

```bash
$sudo named-checkconf
```

###  Verificar a sintaxe dos arquivos de dados
   * Para verificar se a formatação da sintaxe dos arquivos db, utiliza-se o script named-checkconf da seguinte forma: ***named-check-zone <zone> <db_file>***

```bash
$ cd /etc/bind/zones
$ sudo named-checkzone grupo5.turma924.ifalara.local db.grupo5.turma924.ifalara.local
zone grupo5.turma924.ifalara.local/IN: loaded serial 1
OK
$ sudo named-checkzone 24.9.10.in-addr.arpa db.10.9.24.rev
zone 24.9.10.in-addr.arpa/IN: loaded serial 1
OK
```

### Configure para somente resolver endereços IPv4

```bash
$sudo nano /etc/default/named
```
- adicione a linha ***OPTIONS="-4 -u bind"***
```#
# run resolvconf?
RESOLVCONF=no

# startup options for the server
OPTIONS="-4 -u bind"
```


### Execute o BIND para ver se está ativo; 
```bash
$ sudo systemctl enable bind9
$ sudo systemctl restart bind9
```

### Configuração dos clientes;
   * Configure o dns no nas máquina ns1, ns2 e adicione os campos abaixo na interface de rede local (ens160) desses servidores.
```
            nameservers: 
                addresses:
                - 10.9.24.108
                - 10.9.24.109
                search: [[grupo5.turma924.ifalara.local]
```
   * O arquivo de configuração do netplan ficará da seguinte forma:

```bash
$ sudo nano /etc/netplan/00-installer.yaml 

network:
    ethernets:
        ens160:
            addresses: [10.9.24.108/24]
            gateway4: 10.9.24.1
            dhcp4: false
            nameservers:
                addresses:
                - 10.9.24.108
                - 10.9.24.109
                search: [grupo5.turma924.ifalara.local]
    version: 2
```
---
### Testando o servidor DNS:

#### Teste de configuração como cliente. 
   * Observe se os campos **DNS servers** e **DNS Domain** estão corretos.
```bash
$ systemd-resolve --status ens160
```
```
Link 2 (ens160)
      Current Scopes: DNS
DefaultRoute setting: yes
       LLMNR setting: yes
MulticastDNS setting: no
  DNSOverTLS setting: no
      DNSSEC setting: no
    DNSSEC supported: no
  Current DNS Server: 10.9.24.109
         DNS Servers: 10.9.24.109
                      10.9.24.108
          DNS Domain: grupo5.turma924.ifalara.local
```
---
#### Teste o serviço DNS para a máquina ns1. 
   * Veja a resposta em **ANSWER SECTION**.
```bash
$ dig ns1.grupo5.turma924.ifalara.local
```
![Captura de Tela (101)](https://user-images.githubusercontent.com/86020329/158037869-ea98bee2-c35c-43da-b921-c48396f081d6.png)
    
---
$ dig ns2.grupo5.turma924.ifalara.local
![Captura de Tela (102)](https://user-images.githubusercontent.com/86020329/158038197-906d2e9b-3d55-4e12-bbac-36c8cfd17617.png)
    
#### Teste o serviço DNS reverso para a máquina ns1. 
```bash    
$ dig -x 10.9.24.108
```
    
![Captura de Tela (103)](https://user-images.githubusercontent.com/86020329/158038336-650c46f0-0398-4904-a35d-7ec165b51b56.png)

---
#### Teste o serviço DNS reverso para a máquina ns2. 
```bash  
$ dig -x 10.9.24.109
```
![Captura de Tela (104)](https://user-images.githubusercontent.com/86020329/158038512-fc9dd972-6483-4a94-a8ab-84d5783a724c.png)

---
  ### *4.2 Confligurando o DNS Slave:*
   * Primeiro é preciso instalar e para isso usa-se o comando:
```bash
$ sudo apt-get install bind9 dnsutils bind9-doc -y
```
   * Verifique o status do serviço:
```bash
$ sudo systemctl status bind9
```
   * Se não estiver rodando:
```bash
$ sudo systemctl enable bind9
```

### Realizando as devidas configuração de zonas

```bash
$ sudo nano /etc/bind/named.conf.local
```
```
// Do any local configuration here
//

- As zonas que a máquina com o slave vai copiar é:
```    
---
   #### zona direta: db.grupo5.turma924.ifalara.local
---   
```   
```bash   
    $ sudo nano db.grupo5.turma924.ifalara.local
```
    
```

; BIND data file for internal network
;
$ORIGIN grupo5.turma924.ifalara.local.
$TTL    3h
@       IN      SOA     ns1.grupo5.turma924.ifalara.local. root.turma924.ifalara.local. (
                              1         ; Serial
                              3h        ; Refresh
                              1h        ; Retry
                              1w        ; Expire
                              1h )      ; Negative Cache TTL
;nameservers
@       IN      NS      ns1.grupo5.turma924.ifalara.local.
@       IN      NS      ns2.grupo5.turma924.ifalara.local.
;hosts
ns1.grupo5.turma924.ifalara.local.        IN    A       10.9.24.108
ns2.grupo5.turma924.ifalara.local.        IN    A       10.9.24.109
samba.grupo5.turma924.ifalara.local.      IN    A       10.9.24.114
gw.grupo5.turma924.ifalara.local.         IN    A       10.9.24.117
www.grupo5.turma924.ifalara.local.        IN    A       10.9.24.219
bd.grupo5.turma924.ifalara.local.         IN    A       10.9.24.220

```

---
   #### zona reversa: db.10.9.24.rev
---
    
```
;
; BIND data file for internal network
;
;ORIGIN grupo5.turma924.ifalara.local.
$TTL    60
@       IN      SOA     ns1.grupo5.turma924.ifalara.local. root.turma924.ifalara.local. (
                              2         ; Serial
                              3h        ; Refresh
                              1h        ; Retry
                              1w        ; Expire
                              1h )      ; Negative Cache TTL
; name servers
@       IN      NS      ns1.grupo5.turma924.ifalara.local.
@       IN      NS      ns2.grupo5.turma924.ifalara.local.

; PTR Records
108      IN      PTR     ns1.grupo5.turma924.ifalara.local.       ;10.9.24.108
109      IN      PTR     ns2.grupo5.turma924.ifalara.local.       ;10.9.24.109
117      IN      PTR     samba.grupo5.turma924.ifalara.local.     ;10.9.24.114
114      IN      PTR     gw.grupo5.turma924.ifalara.local.        ;10.9.24.117
219      IN      PTR     www.grupo5.turma924.ifalara.local        ;10.9.24.219
220      IN      PTR     bd.grupo5.turma924.ifalara.local         ;10.9.24.220

```
    
    
    
// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "grupo5.turma924.ifalara.local" {
        type slave;
        file "/etc/bind/zones/db.grupo5.turma924.ifalara.local";
        masters{ 10.9.24.109; };

};

zone "24.9.10.in-addr.arpa" IN{
        type slave;
        file "/etc/bind/zones/db.10.9.24.rev";
        masters { 10.9.24.109; };
};
```

### Realizando a checagem de sintaxe

```bash
$ sudo named-checkconf
```


```
     $  dig @10.9.24.108 ns1.grupo5.turma924.ifalara.local
```
* Resultado Esperado

![Captura de Tela (105)](https://user-images.githubusercontent.com/86020329/158038656-8a08c7d7-f190-48a3-bf35-8e1579b59cb3.png)


# 5. Configuração do Web LAMP (Linux+Apache+PHP+Mysql);
    ## Instalação do apache;
   * Apache é o servidor HTTP mais utilizado no mundo, e tem distribuições para diversos sistemas operacionais.
   * Neste roteiro utilizaremos o Ubuntu Server 20.04.4 LTS
   * É sempre recomendado atualizar os pacotes de repositórios e software:
```bash
$ sudo apt update | sudo apt upgrade -y
```
   * Para instalar o apache digite:
```bash
$ sudo apt install -y apache2 apache2-utils
```
   * Verifique o status do serviço:
```bash
$systemctl status apache2
```
![Captura de Tela (107)](https://user-images.githubusercontent.com/86020329/158045431-3b7db591-a261-4fc1-a787-42866dcdfb78.png)

```bash
$ sudo systemctl enable apache2

 Synchronizing state of apache2.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable apache2
```
![Captura de Tela (108)](https://user-images.githubusercontent.com/86020329/158046051-79ce8ba4-d4cd-443a-92ab-214ce9ee6691.png)

 ## Resultado página web:
 ![Captura de Tela (110)](https://user-images.githubusercontent.com/86020329/158049098-06be99b5-9d8d-4d2b-bd47-8ae228734dae.png)

   
 # Instalação do PHP7; 
   * O PHP7.4 é a versão mais recente do PHP7, é estável e é a versão mais utilizada para aplicações Web baseadas em PHP. Para informações e outras versões do PHP consulte a página [php.net](https://www.php.net/).
   * Neste roteiro utilizaremos o Ubuntu Server 20.04.4 LTS
   * É sempre recomendado atualizar os pacotes de repositórios e software:
```bash
$ sudo apt update | sudo apt upgrade -y
```
   * Para instalar o php7.4 digite:
```bash
$ sudo apt install php7.4 libapache2-mod-php7.4 php7.4-mysql php-common php7.4-cli php7.4-common php7.4-common php7.4-json php7.4-opcache php7.4-readline
```
   * Carregue php7.4 no apache2 e reinicio o serviço.


 ## Resultado do PHP7:
    
 ![Captura de Tela (112)](https://user-images.githubusercontent.com/86020329/158049145-91c2b58a-d4eb-42ea-a198-16d4a39ab5eb.png)

 # Virtual-Host resultado:
    
 ![Captura de Tela (113)](https://user-images.githubusercontent.com/86020329/158050169-0ff345da-a2a2-4e77-9338-305e71c17d21.png)
    
    
    
# 6. Configuração do servidor Gateway como NAT;
    
   * A configuração se inicia quando configuramos um servidor como gateway de rede, para isso é necessário configurar o firewall do linux (iptables). 
   * Iptables tem regras e podem ser digitadas no terminal ou podem ser executadas em um script.
   * Com um script, pode-se inicializar as regras do firewall todas as vezes que a máquina for reinicializada.

### habilitar o firewall 
   * Siga o passo-a-passo:
   
   1º. habilitar o firewall e permitir o acesso ssh:
```bash
 $ sudo ufw enable
 $ sudo ufw allow ssh
```
   2º. habilitar o encaminhamento de pacotes das interfaces WAN para LAN, ajustando-se os parâmetros no arquivo **/etc/ufw/sysctl.conf**, removendo-se a marca de comentário (#) da seguinte linha _# net/ipv4/ip_forwarding=1_

```bash
$ sudo nano /etc/ufw/sysctl.conf
``` 
```
...
net/ipv4/ip_forwarding=1
...
```

   3º. confira o nome das interfaces de rede
```bash
$ ifconfig -a
```
```
WAN interface: ens160 
LAN interface: ens192
```

   4º. Configurar as interfaces de rede (netplan) 

```bash
$ sudo nano /etc/netplan/50-cloud-init.yaml 
```

```
network:
    ethernets:
        ens160:
            dhcp4: true
        ens192:
            addresses: [10.0.0.1/24]
            dhcp4: false              
    version: 2
```

```bash
$ sudo netplan apply
$ ifconfig -a
```

   5º. no ubuntu 18.04 o arquivo /etc/rc.local não existe mais. Então é necessário recriá-lo.
```bash
$ sudo nano /etc/rc.local
```

   6º. A seguir, adicione o seguinte script no arquivo [/etc/rc.local](rc.local)

---
```bash
#!/bin/bash

# /etc/rc.local

# Default policy to drop all incoming packets.
# Politica padrão para bloquear (drop) todos os pacotes de entrada
iptables -P INPUT DROP
iptables -P FORWARD DROP

# Accept incoming packets from localhost and the LAN interface.
# Aceita pacotes de entrada a partir das interfaces localhost e the LAN.
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -i ens192 -j ACCEPT

# Accept incoming packets from the WAN if the router initiated the connection.
# Aceita pacotes de entrada a partir da WAN se o roteador iniciou a conexao
iptables -A INPUT -i ens160 -m conntrack \
--ctstate ESTABLISHED,RELATED -j ACCEPT

# Forward LAN packets to the WAN.
# Encaminha os pacotes da LAN para a WAN
iptables -A FORWARD -i ens192 -o ens160 -j ACCEPT

# Forward WAN packets to the LAN if the LAN initiated the connection.
# Encaminha os pacotes WAN para a LAN se a LAN inicar a conexao.
iptables -A FORWARD -i ens160 -o ens192 -m conntrack \
--ctstate ESTABLISHED,RELATED -j ACCEPT

# NAT traffic going out the WAN interface.
# Trafego NAT sai pela interface WAN
iptables -t nat -A POSTROUTING -o ens160 -j MASQUERADE

# rc.local needs to exit with 0
# rc.local precisa sair com 0
exit 0
```
---
   7º. converte o arquivo em executável e o torna inicializável no boot
```bash
$ sudo chmod 755 /etc/rc.local
```
   8º. verificar se o firewall está funcionando
```bash
$ sudo ufw status
```
ou
```bash
$ systemctl status ufw.service
```

   9º.  reiniciar a máquina
```bash
$ sudo reboot
```
   10º. Nas máquinas SAMBA, NS1 e NS2 ativar o gateway (gateway4: 10.0.0.1) na interface de rede:
```bash
$ sudo nano /etc/netplan/00--installer.yaml
```
```
    Ativando o gateway na ns1;
```
    
![Captura de Tela (106)](https://user-images.githubusercontent.com/86020329/158039382-443b7597-5cff-4b1a-9823-ed7e58365d07.png)


```bash
$ sudo netplan apply
$ ifconfig -a
```

  11º. Encaminhamento de portas para acesso externo à serviços da rede interna.
  
  * Para permitir que o serviço de compartilhamento de arquivos esteja disponível externamente, adicione as informações do IPTABLES sobre portas, IP e Interface no arquivo /etc/rc.local conforme o exemplo abaixo, depois reinicie a máquina:
  
   a. SAMBA: Para permitir que o serviço de compartilhamento de arquivos esteja disponível externamente:
        * Portas: 445 e 139
        * Interface Externa aqui é a WAN: ens160
        * IP do servidor = 10.0.0.100
        
```bash
#Recebe pacotes na porta 445 da interface externa do gw e encaminha para o servidor interno na porta 445
iptables -A PREROUTING -t nat -i ens160 -p tcp –-dport 445 -j DNAT –-to 10.0.0.100:445
iptables -A FORWARD -p tcp -d 10.0.0.100 –-dport 445 -j ACCEPT

#Recebe pacotes na porta 139 da interface externa do gw e encaminha para o servidor interno na porta 139
iptables -A PREROUTING -t nat -i ens160 -p tcp –-dport 139 -j DNAT –-to 10.0.0.100:139
iptables -A FORWARD -p tcp -d 10.0.0.100 –-dport 445 -j ACCEPT
```
   b. DNS: Para permitir que o serviço de resolução de nomes (DNS) esteja disponível externamente:
        * Porta: 53
        * Interface Externa aqui é a WAN: ens160
        * IP do servidor nameserver1 = 10.0.0.10
        
```bash
#Recebe pacotes na porta 53 da interface externa do gw e encaminha para o servidor DNS Master interno na porta 53
iptables -A PREROUTING -t nat -i ens160 -p tcp –-dport 53 -j DNAT –-to 10.0.0.10:53
iptables -A FORWARD -p udp -d 10.0.0.10 –-dport 53 -j ACCEPT
```


## Considerações Finais
