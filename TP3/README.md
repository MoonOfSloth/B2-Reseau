# Sommaire

* [I. Manipulation de switches et de VLAN](#i-manipulation-de-switches-et-de-vlan)
  * [1. Mise en place du lab](#1-mise-en-place-du-lab)
  * [2. Configuration des VLANs](#2-configuration-des-vlans)
* [II. Manipulation simple de routeurs](#ii-manipulation-simple-de-routeurs)
  * [1. Mise en place du lab](#1-mise-en-place-du-lab-1)
  * [2. Configuration du routage statique](#2-configuration-du-routage-statique)
* [III. Mise en place d'OSPF](#iii-mise-en-place-dospf)
  * [1. Mise en place du lab](#1-mise-en-place-du-lab-2)
  * [2. Configuration de OSPF](#2-configuration-de-ospf)
* [IV. Lab Final](#iv-lab-final)

# I. Manipulation de switches et de VLAN

## 1. Mise en place du lab

Tableau d'adressage

Hosts | `10.1.1.0/24`
--- | ---
`client1.lab1.tp3` | `10.1.1.1/24`
`client2.lab1.tp3` | `10.1.1.2/24`
`client3.lab1.tp3` | `10.1.1.3/24`

#### > Topologie
```
client1           SW1                  SW 2
+----+         +-------+            +-------+
|    +---------+       +------------+       |
+----+         +---+---+            +---+---+
                   |                    |
                   |                    |
                   |                    |
                   |                    |
                +--+-+               +--+-+
                |    |               |    |
                +----+               +----+
               client2               client3
```


Définition d'une ip static dans le réseau host-only ```10.1.2.0/24```
sur client 1 et 2

```
nano /etc/sysconfig/network-scripts/ifcfg-enp0s8
	TYPE=Ethernet
    BOOTPROTO=static
    NAME=enp0s8
    DEVICE=enp0s8
    ONBOOT=yes
    IPADDR=10.1.2.1
    NETMASK=255.255.255.0
```
On restart -> If up Ifdown

* Ping entre machines :

   ```
        [sloth@client1 ~]$ ping 10.1.1.2
        PING 10.1.1.2 (10.1.1.2) 56(84) bytes of data.
        64 bytes from 10.1.1.2: icmp_seq=1 ttl=64 time=3.21 ms
        64 bytes from 10.1.1.2: icmp_seq=2 ttl=64 time=2.13 ms
        64 bytes from 10.1.1.2: icmp_seq=3 ttl=64 time=2.46 ms
        --- 10.1.1.2 ping statistics ---
        3 packets transmitted, 3 received, 0% packet loss, time 2002ms
        rtt min/avg/max/mdev = 2.131/2.624/3.212/1.702 ms
   ``` 

On change le nom ->sudo hostname <NOM_VM>

## 2. Configuration des VLANs


```
client1           SW1                  SW 2
+----+  VLAN10 +-------+    TRUNK   +-------+
|    +---------+       +------------+       |
+----+         +-------+            +-------+
                   |VLAN20              |VLAN10
                   |                    |
                   |                    |
                   |                    |
                +--+-+               +--+-+
                |    |               |    |
                +----+               +----+
               client2               client3


```

* Configuration du Switch1 :

* Vlan 10

```
SW1#conf t
SW1(config)#vlan 10
SW1(config-vlan)#name Vlan10
SW1(config-vlan)#exit

SW1(config)#interface Ethernet 0/0
SW1(config-if)#switchport mode access
SW1(config-if)#switchport access vlan 10
```
* Vlan 20

```
SW1#conf t
SW1(config)#vlan 20
SW1(config-vlan)#name Vlan20
SW1(config-vlan)#exit

SW1(config)#interface Ethernet 0/1
SW1(config-if)#switchport mode access
SW1(config-if)#switchport access vlan 20
```

* Configuration Switch2 : 
```
SW2#conf t
SW2(config)#vlan 10
SW2(config-vlan)#name Vlan10
SW2(config-vlan)#exit

SW2(config)#interface Ethernet 0/0
SW2(config-if)#switchport mode access
SW2(config-if)#switchport access vlan 10
```

* TRUNK : 

connection de Switch 1 à Switch2 depuis Switch1

```
SW1#conf t
SW1(config-if)#interface Ethernet 0/2
SW1(config-if)#switchport trunk encapsulation dot1q
SW1(config-if)#switchport mode trunk
```
connection de Switch 1 à Switch2 depuis Switch2


```
SW2#conf t
SW2(config-if)#interface Ethernet 0/1
SW2(config-if)#switchport trunk encapsulation dot1q
SW2(config-if)#switchport mode trunk
```

# II. Manipulation simple de routeur
#### > Topologie

# 1. Mise en place du lab
```
                           10.2.12.0/30

                  router1                router2
client1          +------+               +------+
+----+.10        |      |.1           .2|      |.254     .10+----+
|    +-----------+      +---------------+      +------------+    |
+----+           +------+               +------+            +----+
                     |.254                                  server1
                     |
                     |
                     |
   10.2.1.0/24       |                         10.2.2.0/24
                     |.11
                  +----+
                  |    |
                  +----+
                  client2

```

#### > Réseau(x)

Nom | Adresse
--- | ---
`lab2-net1` | `10.2.1.0/24`
`lab2-net2` | `10.2.2.0/24`
`lab2-net12` | `10.2.12.0/30`

#### > Tableau d'adressage

Hosts | `lab2-net1` |  `lab2-net2` |  `lab2-net12` 
--- | --- | --- | ---
`client1.lab2.tp3` | `10.2.1.10/24` | x | x
`client2.lab2.tp3` | `10.2.1.11/24` | x | x
`server1.lab2.tp3` | x | `10.2.2.10/24` | x
`router1.lab2.tp3` | `10.2.1.254/24` | x | `10.2.12.1/30`
`router2.lab2.tp3` | x | `10.2.2.254/24` | `10.2.12.2/30`

## 2. Configuration du routage statique

* Routes


* Server1
```
sudo ip route add 10.2.1.0/24 via 10.2.2.254 dev enp0s3
```

* Client2

```
sudo ip route add 10.2.2.0/24 via 10.2.2.254 dev enp0s3
```

Router1 :

```
sudo ip route 10.2.2.0 255.255.255.0 10.2.12.2
```
Router2 :

```
sudo ip route 10.2.2.0 255.255.255.0 10.2.12.1
```

#### > Vérification

```
    ping 10.2.2.10
        
    PING 10.2.2.10 (10.2.2.10) 56(84) bytes of data.
    64 bytes from 10.2.2.10: icmp_seq=1 ttl=255 time=1.18 ms
    64 bytes from 10.2.2.10: icmp_seq=2 ttl=255 time=1.31 ms
```

## III. Mise en place d'OSPF

# 1. Mise en place du lab

#### > Tableau d'adressage

Hosts | `10.3.100.0/30` | `10.3.100.4/30` | `10.3.100.8/30` | `10.3.100.12/30` | `10.3.100.16/30` | `10.3.100.20/30` | `10.3.101.0/24` | `10.3.102.0/24`
--- | --- | --- | --- | --- | --- | --- | --- | --- 
`client1.lab3.tp3` | x | x | x | x | x | x | `10.3.101.10/24` | x 
`server1.lab3.tp3` | x | x | x | x | x | x | x | `10.3.102.10/24` 
`router1.lab3.tp3` | `10.3.100.1/30` | x | x | x | x | `10.3.100.22/30` | x | `10.3.102.254/24` 
`router2.lab3.tp3` | `10.3.100.2/30` | `10.3.100.4/30` | x | x | x | x | x | x 
`router3.lab3.tp3` | x | `10.3.100.5/30` | `10.3.100.9/30` | x | x | x | x | x 
`router4.lab3.tp3` | x | x | `10.3.100.10/30` | `10.3.100.13/30` | x | x | `10.3.101.254/24` | x 
`router5.lab3.tp3` | x | x | x | `10.3.100.14/30` | `10.3.100.17/30` | x | x | x 
`router6.lab3.tp3` | x | x | x | x | `10.3.100.18/30` | `10.3.100.21/30` | x | x 

# 2. Configuration de OSPF
* Pour chaque routeur:

	* Activation de l'ospf  : 

	```
	(config)# router ospf 1
	```

	* Attribution d'une router-id :
	```
	# router-id 1.1.1.1
	```

    * Partage de tous les réseaux sur lequelle le routeur est connecté :
        ```
           network 10.3.100.0 0.0.0.3 area 0
           network 10.3.102.0 0.0.0.255 area 2
        ```

## IV. Lab Final
