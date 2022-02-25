# <p align="center"> Instituto Federal de Alagoas - Campus Arapiraca </p>
### Prof. Alaelson Jatobá

### Alunos: José Martins, Jonathan Candido, Marcos Vinicius e Milca Elen 

## Sumário

## 1. Introdução
Projeto realizado pelo o grupo 5 (cinco) para a matéria de Serviços de Redes em conjunto com
a outra matéria Projeto e Infraestrutura de Redes, que tem por finalidade colocar em prática toda a teoria
vista em aulas síncronas. Esse projeto visa conhecimentos básicos em serviços de redes como: DNS master e slave. 


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

## 3. Configuração estática do DNS na interface de rede do Ubuntu Server;
* Para que a máquina acesse os sites e hosts remotos por meio de nomes (Ex. www.google.com) é necessário adcionar os nameservers na configuração da interface de rede.
* Logo, configure o arquivo YAML que encontra-se na pasta **/etc/netplan/**.
* Verifique o nome correto do arquivo no seu servidor. No exemplo a seguir, o nome do arquivo é ***00-installer-config.yaml***


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
            gateway4: 10.0.0.1            # IP do Gateway, Aqui é só um exemplo, tenha certeza do IP do seu gateway, ou perderá o acesso remoto.
            dhcp4: false                  # dhcp4 false -> cliente DHCP está desabilitado, logo o utilizará o IP do campo 'addresses'
            nameservers:
                addresses:
                   - 8.8.8.8              # IP do servidor de nomes 1, neste caso é o IP de DNS do google
                   - 8.8.4.4              # IP do servidor de nomes 2, neste caso é outro IP de DNS do google
                search: [grupo5.turma924.ifalara.local]                # identificação do domínio, aqui neste caso está vazio.
    version: 2
```
-  Após salvar o arquivo, aplique as novas configurações, com o **netplan apply**. Depois veja a configuração das interfaces com ****ifconfig -a***

```bash
$ sudo netplan apply
$ ifconfig -a
```


## 4. Implementação dos Serivços de Rede (Cada serviço uma sessão)
     ## 4.2 Confligurando o DNS Master:
    
    
    
    
    
    
    
    
```  
    ## 4.2 Confligurando o DNS Slave:

  administrador@gw:~$  dig @10.9.24.109 ns1.turma924.ifalara.local

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
````


## Considerações Finais
