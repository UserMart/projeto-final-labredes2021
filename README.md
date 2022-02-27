# <p align="center"> Instituto Federal de Alagoas - Campus Arapiraca </p>
### Prof. Alaelson Jatobá

### Alunos: José Martins, Jonathan Candido, Marcos Vinicius e Milca Elen 

## Sumário

## 1. Introdução
Projeto realizado pelo o grupo 5 (cinco) para a matéria de Serviços de Redes em conjunto com
a outra matéria Projeto e Infraestrutura de Redes, que tem por finalidade colocar em prática toda a teoria
vista em aulas síncronas. Esse projeto visa conhecimentos básicos em serviços de redes como: DNS master e slave.
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
|ns1|10.9.24.114
|samba|10.9.24.114


Tabela 2: Nomes das Vm's
```
------------------------------------------------------------------
|    Nome da VM     |                    NOME                     |
------------------------------------------------------------------
| Gateway (gw)      | gw.grupo5.labredes.ifalara.local            |                     
| Samba-SRV.        | samba.grupo5.labredes.ifalara.local         |
| NameServer1 (ns1) | ns1.grupo5.labredes.ifalara.local           |
| NameServer2 (ns2) | ns2.grupo5.labredes.ifalara.local           |
------------------------------------------------------------------

```

## 3. Realize a configuração estática do DNS na interface de rede do Ubuntu-Server;
* Para que a máquina acesse os sites e hosts remotos por meio de nomes (Ex. www.google.com) é necessário adcionar os nameservers na configuração da interface de rede.
* Logo, configure o arquivo YAML que encontra-se na pasta **/etc/netplan/**.
* Verifique o nome correto do arquivo no seu servidor. No exemplo a seguir, o nome do arquivo é  ***00-installer-config.yaml***


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
                   - 8.8.8.8              # IP do servidor de nomes 1, neste caso é o IP de DNS do google
                   - 8.8.4.4              # IP do servidor de nomes 2, neste caso é outro IP de DNS do google
                search: [grupo5.turma924.ifalara.local]     # identificação do domínio, aqui neste caso está vazio.
        ens192:
            addresses: [192.168.24.34/29]

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
   * o arquivo db.turma924.ifalara.local conterá os nomes das máquinas do domínio grupo5.turma924.ifalara.local
   * Para isso faremos uma cópia do arquivo /etc/bind/db.empty
```bash
$ sudo cp /etc/bind/db.empty /etc/bind/zones/db.turma924.ifalara.local 
```

##### zona reversa
   * Utilizado quando não se conhece o IP mas sabe-se o nome do host.
   * vamos criar a zona reversa a partir do arquivo /etc/bind/db.127
```bash
  $ sudo cp /etc/bind/db.127 /etc/bind/zones/db.10.9.24.rev
```

   * Assim, o arquivo **db.10.9.24.rev** conterá a zona reversa da rede 10.9.14.0. 

   
### Editar arquivos db:

   #### zona direta: db.turma924.ifalara.local
   * edite o arquivo  **db.turma924.ifalara.local** para adcionar as informações do seu domínio
      
```bash   
    $ sudo nano db.turma924.ifalara.local
```
---
```

; BIND data file for internal network
;
$ORIGIN turma924.ifalara.local.
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
dh1.grupo5.turma924.ifalara.local.        IN    A       10.9.24.100
gw.grupo5.turma924.ifalara.local.         IN    A       10.9.24.1
desktophost1    CNAME     dh1                 ; CNAME é um apelido
```

---
   #### zona reversa: db.10.9.24.rev
   * edite o arquivo **db.10.9.24.rev** para adcionar as informações da zona reversa
   
---
```
;
; BIND reverse data file of reverse zone for local area network 10.9.24.0/24
;
$TTL    604800
@       IN      SOA     grupo5.turma924.ifalara.local. root.turma924.ifalara.local. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

; name servers
@      IN      NS      ns1.grupo5.turma924.ifalara.local.
@      IN      NS      ns2.grupo5.turma924.ifalara.local.

; PTR Records
10   IN      PTR     ns1.grupo5.turma924.ifalara.local.             ; 10.9.24.108
11   IN      PTR     ns2.grupo5.turma924.ifalara.local.             ; 10.9.14.109
100  IN      PTR     dh1.grupo5.turma924.ifalara.local.             ; 10.9.24.100
1    IN      PTR     gw.grupo5.turma924.ifalara.local.              ; 10.9.24.1


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

zone "grupo5.turma924.ifalarapiraca.local" {
        type master;
        file "/etc/bind/zones/db.grupo5.turma924.ifalarapiraca.local";
        allow-transfer{ 10.9.24.108; };
        allow-query{any;};
};

zone "24.9.10.in-addr.arpa" IN {
        type master;
        file "/etc/bind/zones/db.10.9.24.rev";
        allow-transfer{ 10.9.24.108; };
};
```
---

### Verificação de sintaxe 
   * Para checar a sintaxe de configuração do BIND deve-se executar o comando named-checkconf. Este script checa os arquivos /etc/bind/named.conf.local.*

```bash
$sudo named-checkconf
```

```
/etc/bind/named.conf.local:12: option 'masters' is not allowed in 'master' zone 'grupo5.turma924.ifalarapiraca.local'
/etc/bind/named.conf.local:17: option 'masters' is not allowed in 'master' zone '24.9.10.in-addr.arpa'
/etc/bind/named.conf.local:15: zone '24.9.10.in-addr.arpa': missing 'file' entry
```

###  Verificar a sintaxe dos arquivos de dados
   * Para verificar se a formatação da sintaxe dos arquivos db, utiliza-se o script named-checkconf da seguinte forma: ***named-check-zone <zone> <db_file>***

```bash
$ cd /etc/bind/zones
$ sudo named-checkzone grupo5.turma924.ifalara.local db.turma924.ifalara.local
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

### Configuração dos clientes
   * Configure o dns no nas máquina ns1, ns2 e adicione os campos abaixo na interface de rede local (ens160) desses servidores.
```
            nameservers: 
                addresses:
                - 10.9.14.10
                - 10.9.14.11
                search: [labredes.ifalarapiraca.local]
```
   * O arquivo de configuração do netplan ficará da seguinte forma:

```bash
$ sudo nano /etc/netplan/50-cloud-init.yaml 

network:
    ethernets:
        enp0s3:
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
Link 2 (enp0s3)
      Current Scopes: DNS
       LLMNR setting: yes
MulticastDNS setting: no
      DNSSEC setting: no
    DNSSEC supported: no
         DNS Servers: 10.9.14.10
                      10.9.14.11
         DNS Domain: labredes.ifalarapiraca.local
```
---
#### Teste o serviço DNS para a máquina ns1. 
   * Veja a resposta em **ANSWER SECTION**.
```bash
$ dig ns1.labredes.ifalarapiraca.local
```

```
; <<>> DiG 9.11.3-1ubuntu1.9-Ubuntu <<>> ns1.labredes.ifalarapiraca.local
;; global options: +cmd
;; Got answer:
;; WARNING: .local is reserved for Multicast DNS
;; You are currently testing what happens when an mDNS query is leaked to DNS
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 47542
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;ns1.labredes.ifalarapiraca.local. IN	A

;; ANSWER SECTION:
ns1.labredes.ifalarapiraca.local. 5204 IN A	10.9.14.10

;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Tue Oct 15 06:02:20 UTC 2019
;; MSG SIZE  rcvd: 7
```
---

#### Teste o serviço DNS reverso para a máquina ns1. 
```bash    
$ dig -x 10.9.14.10
```
```
; <<>> DiG 9.11.3-1ubuntu1.9-Ubuntu <<>> -x 10.9.14.10
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 48674
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;10.9.14.10.in-addr.arpa.		IN	PTR

;; ANSWER SECTION:
10.9.14.10.in-addr.arpa.	6141	IN	PTR	ns1.labredes.ifalarapiraca.local.

;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Tue Oct 15 06:01:23 UTC 2019
;; MSG SIZE  rcvd: 97
```
---
#### Teste o serviço DNS reverso para a máquina ns2. 
```bash  
$ dig -x 10.9.14.11
```
```
; <<>> DiG 9.11.3-1ubuntu1.9-Ubuntu <<>> -x 10.9.14.11
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 56462
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;11.14.9.10.in-addr.arpa.		IN	PTR

;; ANSWER SECTION:
11.14.9.10.in-addr.arpa.	6177	IN	PTR	ns2.labredes.ifalarapiraca.local.

;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Tue Oct 15 06:01:01 UTC 2019
;; MSG SIZE  rcvd: 97
```
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

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "turma924.ifalara.local" {
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
     $  dig @10.9.24.109 ns1.turma924.ifalara.local
```
* Resultado Esperado

```
; <<>> DiG 9.16.6-Ubuntu <<>> @10.9.24.109 ns1.turma924.ifalara.local
; (1 server found)
;; global options: +cmd
;; Got answer:
;; WARNING: .local is reserved for Multicast DNS
;; You are currently testing what happens when an mDNS query is leaked to DNS
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 21582
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: e6f9391aca2117c8010000006218d44a88cd6f9923ed0209 (good)
;; QUESTION SECTION:
;ns1.turma924.ifalara.local.    IN      A

;; AUTHORITY SECTION:
.                       10800   IN      SOA     a.root-servers.net. nstld.verisign-grs.com. 2022022500 1800 900 604800 86400

;; Query time: 345 msec
;; SERVER: 10.9.24.109#53(10.9.24.109)
;; WHEN: Fri Feb 25 13:06:18 UTC 2022
;; MSG SIZE  rcvd: 158

```


## Considerações Finais
