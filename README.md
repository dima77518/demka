# <div align="center"><strong>⚙️</strong></div> <div align="center"><strong>МОДУЛЬ 1</strong></div>

<br/>

<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/%D0%A2%D0%BE%D0%BF%D0%BE%D0%BB%D0%BE%D0%B3%D0%B8%D1%8F.jpg" alt="Топология" />
</p>
<p align="center"><strong>Топология</strong></p>

<br/>

> [!WARNING]
> ## ПРЕДНАСТРОЙКА
>
> ```
> systemctl stop NetworkManager
> systemctl disable NetworkManager
> systemctl mask NetworkManager
> ```
></br>
>
> На всех устройствах, кроме **ISP**:
>```
>nano /etc/resolv.conf
>nameserver 77.88.8.8
>nameserver 1.1.1.1
>```
></br>
>
> На **РОУТЕРАХ** sysctl -p:
>```
> echo net.ipv4.ip_forward=1 > /etc/sysctl.conf
>```
></br> 
>
> **Сурс** листы везде:
> ```
>nano /etc/apt/sources.list
>```
>```
> # deb cdrom:.......
>  ↑
>  Ставим комментарий
>```
>```
>apt update
>```
</br>

<table>
  <thead>
    <tr>
      <th>Маска подсети</th>
      <th>Полная маска</th>
      <th>Сколько адресов вмещает</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/26</td>
      <td>255.255.255.192</td>
      <td align="center">62</td>
    </tr>
    <tr>
      <td>/27</td>
      <td>255.255.255.224</td>
      <td align="center">30</td>
    </tr>
    <tr>
      <td>/28</td>
      <td>255.255.255.240</td>
      <td align="center">14</td>
    </tr>
    <tr>
      <td>/29</td>
      <td>255.255.255.248</td>
      <td align="center">6</td>
    </tr>
  </tbody>
</table>

</br>

## ✔️ Задание 1 <code>[ Адрессация + Имя устройства]</code>

> [!WARNING]
> В инструкции по сетевой настройке используется редактирование: <strong>`etc/network/interfaces`</strong>

> [!NOTE]
> **Используй редактор файлов: `nano`**

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Произведите `базовую` настройку устройств

- Настройте имена устройств согласно топологии. Используйте полное доменное имя
- На всех устройствах необходимо сконфигурировать IPv4
- IP-адрес должен быть из приватного диапазона, в случае, если сеть локальная, согласно RFC1918
- Локальная сеть в сторону HQ-SRV(VLAN100) должна вмещать не более 64 адресов
- Локальная сеть в сторону HQ-CLI(VLAN200) должна вмещать не более 16 адресов
- Локальная сеть в сторону BR-SRV должна вмещать не более 32 адресов
- Локальная сеть для управления(VLAN999) должна вмещать не более 8 адресов
- Сведения об адресах занесите в отчёт, в качестве примера используйте Таблицу 3

</details>

<br/>

<details>
<summary><strong>Настройка имени</strong></summary>
  
<br/>

  - Для **Linux** используется команда: <strong>`hostnamectl set-hostname (имя устройства.au-team.irpo)`</strong>
  
  - Обновить имя можно введя команду: **`bash`**
### ISP
```
hostnamectl set-hostname isp.au-team.irpo
bash
```
<br/>

### HQ-RTR
```
hostnamectl set-hostname hq-rtr.au-team.irpo
bash
```
<br/>

### BR-RTR
```
hostnamectl set-hostname br-rtr.au-team.irpo
bash
```
<br/>

### HQ-SRV
```
hostnamectl set-hostname hq-srv.au-team.irpo
bash
```
<br/>

### HQ-CLI
```
hostnamectl set-hostname hq-cli.au-team.irpo
bash
```
<br/>

### BR-SRV
```
hostnamectl set-hostname br-srv.au-team.irpo
bash
```
<br/>

</details>

<details>
<summary><strong>Настройка адрессации в файле <code>etc/network/interfaces</code></strong></summary>
<br/>

### Настройка адресации (кроме HQ-CLI(он позже))
##
### ISP:
```
auto ens224
iface ens224 inet static
address 172.16.4.1/28
auto ens256
iface ens256 inet static
address 172.16.5.1/28
```

### HQ-RTR: (в 4 и 6 задании продолжение)
```
allow-hotplug ens192
iface ens192 inet static
address 172.16.4.2/28
gateway 172.16.4.1

auto gre1
iface gre1 inet tunnel
address 172.16.0.1
netmask 255.255.255.252
mode gre
local 172.16.4.2
endpoint 172.16.5.2
ttl 64
```

### BR-RTR: (в 6 задании продолжение)
```
allow-hotplug ens192
iface ens192 inet static
address 172.16.5.2/28
gateway 172.16.5.1

auto ens224
iface ens224 inet static
address 192.168.0.1/27

auto gre1
iface gre1 inet tunnel
address 172.16.0.2
netmask 255.255.255.252
mode gre
local 172.16.5.2
endpoint 172.16.4.2
ttl 64
```

### BR-SRV:

```
allow-hotplug ens192
iface ens192 inet static
address 192.168.0.2/27
gateway 192.168.0.1
dns-nameservers 192.168.100.62 192.168.0.2
dns-search au-team.irpo
```

### HQ-SRV:

```
allow-hotplug ens192
iface ens192 inet static
address 192.168.100.62/26
gateway 192.168.100.1
```

</details>

<details>
<summary><strong>Таблицы сетей</strong></summary>

</br>

<table align="center">
  <tr>
    <td align="center"><strong>Сеть</strong></td>
    <td align="center"><strong>Адрес подсети</strong></td>
    <td align="center"><strong>Пул-адресов</strong></td>
  </tr>
  <tr>
    <td align="center">SRV-Net (VLAN 100)</td>
    <td align="center">192.168.100.0/26</td>
    <td align="center">192.168.100.1-62</td>
  </tr>
  <tr>
    <td align="center">CLI-Net (VLAN 200)</td>
    <td align="center">192.168.200.0/28</td>
    <td align="center">192.168.200.1-14</td>
  </tr>
  <tr>
    <td align="center">MGMT (VLAN 999)</td>
    <td align="center">192.168.99.0/29</td>
    <td align="center">192.168.99.1-6</td>
  </tr>
  <tr>
    <td align="center">BR-Net</td>
    <td align="center">192.168.0.0/27</td>
    <td align="center">192.168.0.1-30</td>
  </tr>
  <tr>
    <td align="center">ISP-HQ</td>
    <td align="center">172.16.4.0/28</td>
    <td align="center">172.16.4.1 - 14</td>
  </tr>
  <tr>
    <td align="center">ISP-BR</td>
    <td align="center">172.16.5.0/28</td>
    <td align="center">172.16.5.1 - 14</td>
  </tr>
</table>
<p align="center"><strong>Таблица подсетей</strong></p>

#

<table align="center">
  <tr>
    <td align="center"><strong>Устройство</strong></td>
    <td align="center"><strong>Интерфейс</strong></td>
    <td align="center"><strong>IPv4/IPv6</strong></td>
    <td align="center"><strong>Маска/Префикс</strong></td>
    <td align="center"><strong>Шлюз</strong></td>
    <td align="center"><strong>Сеть</strong></td>
  </tr>
  <tr>
    <td align="center" rowspan="3">ISP</td>
    <td align="center">ens192</td>
    <td align="center">(DHCP)</td>
    <td align="center">(DHCP)</td>
    <td align="center">(DHCP)</td>
    <td align="center">INTERNET</td>
  </tr>
  <tr>
    <td align="center">ens224</td>
    <td align="center">172.16.4.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
    <td align="center">ISP-HQ-RTR</td>
  </tr>
  <tr>
    <td align="center">ens256</td>
    <td align="center">172.16.5.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
    <td align="center">ISP-BR-RTR</td>
  </tr>
  <tr>
    <td align="center" rowspan="3">HQ-RTR</td>
    <td align="center">ens192</td>
    <td align="center">172.16.4.2</td>
    <td align="center">/28</td>
    <td align="center">172.16.4.1</td>
    <td align="center">ISP-HQ-RTR</td>
  </tr>
  <tr>
    <td align="center">ens224.200</td>
    <td align="center">192.168.200.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
    <td align="center">HQ-RTR-CLI</td>
  </tr>
  <tr>
    <td align="center">ens224.100</td>
    <td align="center">192.168.100.1</td>
    <td align="center">/26</td>
    <td align="center"></td>
    <td align="center">HQ-RTR-SRV</td>
  </tr>
  <tr>
    <td align="center" rowspan="2">BR-RTR</td>
    <td align="center">ens192</td>
    <td align="center">172.16.5.2</td>
    <td align="center">/28</td>
    <td align="center">172.16.5.1</td>
    <td align="center">ISP-BR-RTR</td>
  </tr>
  <tr>
    <td align="center">ens224</td>
    <td align="center">192.168.0.1</td>
    <td align="center">/27</td>
    <td align="center"></td>
    <td align="center">BR-RTR-SRV</td>
  </tr>
  <tr>
    <td align="center">HQ-SRV</td>
    <td align="center">ens192</td>
    <td align="center">192.168.100.62</td>
    <td align="center">/26</td>
    <td align="center">192.168.100.1</td>
    <td align="center">HQ-RTR-SRV</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">ens192</td>
    <td align="center">192.168.0.2</td>
    <td align="center">/27</td>
    <td align="center">192.168.0.1</td>
    <td align="center">BR-RTR-SRV</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI</td>
    <td align="center">ens192</td>
    <td align="center">192.168.200.##(DHCP)</td>
    <td align="center">/28</td>
    <td align="center">192.168.200.1</td>
    <td align="center">HQ-RTR-CLI</td>
  </tr>
</table>
<p align="center"><strong>Таблица адресации</strong></p>

</details>
</br>

## ✔️ Задание 2 <code>[NAT на ISP]</code>

<br/>

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Настройка `ISP`

- Настройте адресацию на интерфейсах:

  - Интерфейс, подключенный к магистральному провайдеру, получает адрес по DHCP **[Выполнено в задании 1]**

  - Настройте маршруты по умолчанию там, где это необходимо 

  - Интерфейс, к которому подключен HQ-RTR, подключен к сети 172.16.4.0/28 **[Выполнено в задании 1]**

  - Интерфейс, к которому подключен BR-RTR, подключен к сети 172.16.5.0/28 **[Выполнено в задании 1]**

  - На ISP настройте динамическую сетевую трансляцию в сторону HQ-RTR и BR-RTR для доступа к сети Интернет

</details>

<br/>

<details>
<summary><strong>Настройка динамической трансляции <code>NAT</code></strong></summary>

### Настройка динамической сетевой трансляции на _`ISP`_

```
echo net.ipv4.ip_forward=1 > /etc/sysctl.conf
```
```
apt-get install iptables iptables-persistent –y
```
```
iptables –t nat –A POSTROUTING –s 172.16.4.0/28 –o ens192 –j MASQUERADE  
iptables –t nat –A POSTROUTING –s 172.16.5.0/28 –o ens192 –j MASQUERADE
netfilter-persistent save
systemctl restart netfilter-persistent  
```
</br>

<details>
<summary><strong><code>Либо другая настройка</code></strong></summary>  

```  
apt install iptables  
apt install iptables iptables-persistent  
iptables –t nat –A POSTROUTING –s 172.16.4.0/28 –o ens192 –j MASQUERADE   
iptables –t nat –A POSTROUTING –s 172.16.5.0/28 –o ens192 –j MASQUERADE   
iptables-save > /etc/iptables/rules.v4  
```
  
> **`ens192`** - интерфейс с которого приходит **интернет**
> 
> Для проверки можно использовать команду: **`iptables –L –t nat`** - должны высветится в Chain POSTROUTING две настроенные подсети  

> Для того, чтобы сбросить настройку *nat*, можно использовать команду **`iptables -t nat -F`**

#
</details>

</details>

<br/>

## ✔️ Задание 3 `[Создание учетных записей]` 

> [!WARNING]
> Обратите внимание, что группа <code><strong>WHEEL</strong></code> не используется!
<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Создание локальных учетных записей

- Создайте пользователя sshuser на серверах HQ-SRV и BR-SRV

  - Пароль пользователя sshuser с паролем P@ssw0rd

  - Идентификатор пользователя 1010

  - Пользователь sshuser должен иметь возможность запускать sudo без дополнительной аутентификации.

- Создайте пользователя net_admin на маршрутизаторах HQ-RTR и BR-RTR

  - Пароль пользователя net_admin с паролем P@$$word

  - При настройке на EcoRouter пользователь net_admin должен обладать максимальными привилегиями

  - При настройке ОС на базе Linux, запускать sudo без дополнительной аутентификации
 
</details>

<br/>

<details>
<summary><strong>Как создать <code>sshuser</code></strong></summary>
<br/>

- ## Создание учёток _`sshuser`_ производится на _`HQ-SRV`_ и _`BR-SRV`_

**1.** Создаём sshuser следующими командами:
```
useradd sshuser -u 1010
passwd sshuser
P@ssw0rd
```
<br/>

**2.** После чего даем пользователю <strong><code>root</strong></code> права:
```yml
usermod -aG sudo sshuser
```

<br/>

**3.** Добавляем следующую строку в файле `/sudoers`:
```
nano /etc/sudoers
```

```yml
sshuser ALL=(ALL) NOPASSWD:ALL
```
> Позволяет запускать **`sudo`** без аутентификации

<br/>

**4.** Создаем и задаем необходимые права на домашнюю папку
```
mkdir /home/sshuser
chown sshuser:sshuser /home/sshuser
chmod 700 /home/sshuser
```
<br/>
</details>

<details>
<summary><strong>Как создать <code>net_admin</code></strong></summary>
<br/>

- ## Пользователь _`net_admin`_ на _`HQ-RTR`_ и _`BR-RTR`_

**1.** Создаём **`net_admin`**, следующими командами, но уже без `-u 1010` и с новым паролем:
```
useradd net_admin
passwd net_admin
P@$$word
```
<br/>

**2.** После чего даем пользователю <strong><code>root</strong></code> права:
```yml
usermod -aG sudo net_admin
```
<br/>

**3.** Добавляем следующую строку в `/sudoers`:
```
nano /etc/sudoers
```
```yml
net_admin ALL=(ALL) NOPASSWD:ALL
```
> Позволяет запускать **`sudo`** без аутентификации
<br/>

**4.** Создаем и задаем необходимые права на домашнюю папку
```
mkdir /home/net_admin
chown net_admin:net_admin /home/net_admin
chmod 700 /home/net_admin
```
<br/>

</details>

<br/>

## ✔️ Задание 4 `[VLAN]`

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Настройте на интерфейсе `HQ-RTR` в сторону офиса `HQ` виртуальный коммутатор

- Сервер HQ-SRV должен находиться в ID VLAN 100
- Клиент HQ-CLI в ID VLAN 200
- Создайте подсеть управления с ID VLAN 999
- Основные сведения о настройке коммутатора и выбора реализации разделения на VLAN занесите в отчёт

</details>
<br/>

<details>
<summary><strong>Настройка VLAN на <code>HQ-RTR</code></strong></summary>

## Настройка VLAN на **`HQ-RTR`**

- Для начала установи пакет _**`vlan`**_:

```
apt install vlan
```

- После чего добавляем модуль _**`modprobe 8021q`**_ командой:
```
echo 8021q >> /etc/modules
```
- Далее переходим к конфигурации файла _**`/etc/network/interfaces`**_ и приводим её к виду:
```
nano /etc/network/interfaces
```

```
# The primary network interface
auto ens192  
iface ens192 inet static  
address 172.16.4.2/28
gateway 172.16.4.1

auto gre1
iface gre1 inet tunnel
address 172.16.0.1
netmask 255.255.255.252
mode gre
local 172.16.4.2
endpoint 172.16.5.2
ttl 64
  
auto ens224  
iface ens224 inet static  
address 192.168.100.1/26 
  
auto ens224:1  
iface ens224:1 inet static  
address 192.168.200.1/28

auto ens224:2  
iface ens224:2 inet static  
address 192.168.99.9/29

auto ens224.100  
iface ens224.100 inet manual   
Vlan-raw-device ens224  
  
auto ens224.200  
iface ens224.200 inet manual   
Vlan-raw-device ens224:1

auto ens224.999  
iface ens224.999 inet manual   
Vlan-raw-device ens224:2
```

</details>

<br/>

## ✔️ Задание 5 `[SSH]`

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Настройка безопасного удаленного доступа на серверах `HQ-SRV` и `BR-SRV`

- Для подключения используйте порт 2024
- Разрешите подключения только пользователю sshuser
- Ограничьте количество попыток входа до двух
- Настройте баннер «Authorized access only»

</details>
<br/>

<details>
<summary><strong>Настройка <code>SSH</code></strong></summary>
<br/>

### SSH настраиваем на `HQ-SRV` и `BR-SRV`

**1.** Для настройки **SSH** необходимо его установить коммандой:
```
apt-get install openssh-server -y
```

</br>

**2.** После чего необходимо добавить строчки в файл **`sshd_config`**:
```
nano /etc/ssh/sshd_config
```

```
Port 2024
MaxAuthTries 2
PasswordAuthentication yes
Banner /etc/ssh/banner
AllowUsers  sshuser
           ^ - это TAB
```
<br/>

**3.** После чего требуется создать файл **`/etc/ssh/banner`** и привести его в следующую форму:
```
----------------------
Authorized access only
----------------------
```
</br>

**4.** Далее необходимо перезапустить **`SSH`** коммандой:
  
```
systemctl restart sshd
```


</details>

<br/>

## ✔️ Задание 6 `[GRE-TUNNEL]`

> [!WARNING]
> **Не используйте** устройства с именем **`tun0`, `gre0` и `sit0`**, т.к они являются зарезервированными в iproute2 («base devices») и имеют особое поведение.

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Между офисами `HQ` и `BR` необходимо сконфигурировать _`IP-туннель`_

- Сведения о туннеле занесите в отчёт  
- На выбор технологии GRE или IP in IP
</details>
<br/>

<details>
<summary><strong>Настройка GRE в файле <code>etc/network/interfaces</code>на <code>HQ-RTR</code></strong></summary>
<br/>

Для настройки _**`GRE`**_ туннеля на **`HQ-RTR`** переходим в файл конфигурации

```
nano /etc/network/interfaces
```

И убеждаемся в наличии этих строк:
```
auto gre1
iface gre1 inet tunnel
address 172.16.0.1
netmask 255.255.255.252
mode gre
local 172.16.4.2
endpoint 172.16.5.2
ttl 64
```

Для работы туннеля необходимо добавить строчку в файл `/etc/modules`
```
echo gre_ip >> /etc/modules
```
```
systemctl restart networking
```
</details>

<details>
<summary><strong>Настройка GRE в файле <code>etc/network/interfaces</code> на <code>BR-RTR</code></strong></summary>
<br/>
Для настройки *GRE* туннеля на *BR-RTR* переходим в файл конфигурации
  
```
nano /etc/network/interfaces
```

И убеждаемся в наличии этих строк:
```
auto gre1
iface gre1 inet tunnel
address 172.16.0.2
netmask 255.255.255.252
mode gre
local 172.16.5.2
endpoint 172.16.4.2
ttl 64
```

Для работы туннеля необходимо добавить строчку в файл `/etc/modules`
```
echo gre_ip >> /etc/modules
```
```
systemctl restart networking
```
</details>
<br/>


## ✔️ Задание 7 `[OSPF]`

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Обеспечьте динамическую маршрутизацию: ресурсы одного офиса должны быть доступны из другого офиса. Для обеспечения динамической маршрутизации используйте `link state` протокол на ваше усмотрение

- Разрешите выбранный протокол только на интерфейсах в ip туннеле  
- Маршрутизаторы должны делиться маршрутами только друг с другом  
- Обеспечьте защиту выбранного протокола посредством парольной защиты  
- Сведения о настройке и защите протокола занесите в отчёт

</details>
<br/>

<details>
<summary><strong>Настройка <code>OSPF</code></strong></summary>
<br/>


## Настройка `OSPF` производится на `HQ-RTR` и `BR-RTR`:

### HQ-RTR

**1.** Устанавливаем пакет `FRR`

```
sudo apt install -y frr
```

**2.** В конфигурационном файле `/etc/frr/daemons` необходимо активировать выбранный протокол `OSPF` для дальнейшей реализации его настройки:

```
nano /etc/frr/daemons

!!! Ищем следующую строку и меняем с (no) на (yes)
ospfd = yes

```

**3.** Далее перезаргужаем и добавляем в автозагрузку службу **`FRR`**

```
systemctl restart frr
systemctl enable --now frr
```

**4.** Переходим в интерфейс управления симуляцией **`FRR`** командой:
```
vtysh
```

**5.** Пишем команды для настройки **маршрутизации:**
 
```
conf t
router ospf
  passive-interface default
  router-id 1.1.1.1
  network 172.16.0.0/30 area 0
  network 192.168.100.0/26 area 1
  network 192.168.200.0/28 area 2
  area 0 authentication
exit

int gre1
  no ip ospf network broadcast
  no ip ospf passive
  ip ospf authentication
  ip ospf authentication-key password
(config-if)exit
(config)exit
#write
```
<br>

### BR-RTR

**1-4.** Пункты такие же как и в HQ-RTR

**5.** Пишем команды для настройки **маршрутизации:**

**Меняется:** 

- `id-router: 2.2.2.2`

- `network 192.168.0.0/27 area 3`

- `network 172.16.0.0/30 area 0`

```
conf t
router ospf
  passive-interface default
  router-id 2.2.2.2
  network 192.168.0.0/27 area 3
  network 172.16.0.0/30 area 0
  area 0 authentication
exit

int gre1
  no ip ospf network broadcast
  no ip ospf passive
  ip ospf authentication
  ip ospf authentication-key password
(config-if)exit
(config)exit
#write
```

### ПРОВЕРКА

Пингуем: **`BR-SRV - > HQ-SRV`** и **`BR-SRV - > HQ-CLI`**

Проверка в **FRR**:

```
vtysh
  show ip ospf neighbor
  show ip route ospf
```

</details>

<br/>


<br/>
Настройка маршрутов, если не работает <code><strong>OSPF</strong></code>:

<br/>
<details>
<summary><strong>Настройка <code>маршрутов</code></strong></summary>
<br/>

После создания влана настраиваем маршрут для подсетей (чтобы они видели друг друга)  
На роутере *HQ-RTR* настройка выглядит так:
```
sudo nano /etc/systemd/system/iproute.service
```

<br/>

После чего добавляем текст:
```
[Unit]
Description=iproute
After=network.target

[Service]
Type=oneshot
ExecStart=/sbin/ip route add 192.168.0.0/27 via 172.16.0.2 dev gre1
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

<br/>

На роутере *BR-RTR* создаем тот же файли настраивеим:
```
sudo nano /etc/systemd/system/iproute.service
Для HQ-RTR:
[Unit]
Description=iproute
After=network.target

[Service]
Type=oneshot
ExecStart=/sbin/ip route add 192.168.100.0/26 via 172.16.0.1 dev gre1
ExecStart=/sbin/ip route add 192.168.200.0/28 via 172.16.0.1 dev gre1
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

<br/>

После на обоих устройствах прописываем:
```
systemctl daemon-reload
systemctl enable iproute.service
systemctl start iproute.service
```

</details>

<br/>

## ✔️ Задание 8 `[NAT на HQ-rtr и BR-rtr]`

> [!NOTE]
> Пул адрессов можно посмотреть в таблице <strong>[Задания 1](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module1#задание-1)</strong> или же отталкиваться от вашей адрессации.

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Настройка динамической трансляции адресов

- Настройте динамическую трансляцию адресов для обоих офисов.  
- Все устройства в офисах должны иметь доступ к сети Интернет

</details>
<br/>

<details>
<summary><strong>Настройка NAT на <code>BR</code> и <code>HQ</code></strong></summary>
<br/>

## > Настройка динамической трансляции адресов <

> ### Настройка на `ISP` выполнена в [Задании 2](https://github.com/Flicks1383/Demo09.02.06_2025/tree/main/module1#задание-2)

### Настройка динамической сетевой трансляции на `HQ-RTR`
```
apt-get install iptables iptables-persistent –y
iptables –t nat –A POSTROUTING –s 192.168.100.0/26 –o ens192 –j MASQUERADE
iptables –t nat –A POSTROUTING –s 192.168.200.0/28 –o ens192 –j MASQUERADE
netfilter-persistent save
systemctl restart netfilter-persistent  
```
### Настройка динамической сетевой трансляции на `BR-RTR`

```
apt-get install iptables iptables-persistent –y
iptables –t nat –A POSTROUTING –s 192.168.0.0/27 –o ens192 –j MASQUERADE
netfilter-persistent save
systemctl restart netfilter-persistent  
```
> Для того, чтобы **сбросить** настройку *nat*, можно использовать команду **`iptables -t nat -F`**

</br>

</details>

</br>

## ✔️ Задание 9 `[DHCP]`

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Настройка протокола динамической конфигурации хостов

- Настройте нужную подсеть  
- Для офиса HQ в качестве сервера DHCP выступает маршрутизатор HQ-RTR.  
- Клиентом является машина HQ-CLI.  
- Исключите из выдачи адрес маршрутизатора  
- Адрес шлюза по умолчанию – адрес маршрутизатора HQ-RTR.  
- Адрес DNS-сервера для машины HQ-CLI – адрес сервера HQ-SRV.  
- DNS-суффикс для офисов HQ – au-team.irpo  
- Сведения о настройке протокола занесите в отчёт

</details>
<br/>

<details>
<summary><strong>Настройка DHCP на <code>HQ-RTR</code></strong></summary>
<br/>

## > Настройка _`DHCP`_ на _`HQ-RTR`_ для `CLI` <

<br/>

**1.** Устанавливаем сам **DHCP-сервер**:  
```
apt install isc-dhcp-server
```
<br/>

**2.** После чего переходим в конфигурацию файла `dhcpd.conf` и добавляем следующие строчки:
```
nano /etc/dhcp/dhcpd.conf
```

```
subnet 192.168.200.0 netmask 255.255.255.240 {
  range 192.168.200.2 192.168.200.14;
  option domain-name-servers 192.168.100.62;
  option domain-name "au-team.irpo";
  option routers 192.168.200.1;
  default-lease-time 600;
  max-lease-time 7200;
}
```
<br/>

**3.** После чего переходим в конфигурацию файла `isc-dhcp-server` и меняем ее добалвяя данный текст:
```
nano /etc/default/isc-dhcp-server

INTERFACESv4="ens224:1" - порт смотрящий в сторону CLI
```

<br/>

**4.** Включаем сервиc **`DHCP`** и добавляем в автозагрузку на **`HQ-RTR`**:
```
systemctl start isc-dhcp-server
systemctl enable isc-dhcp-server
```

**5.** Далее на клиенсткой машине необходимо в настройках сет.адаптера выбрать режим **DHCP** и проверить работоспособность
<br/>

</details>

</br>

## ✔️ Задание 10 `[DNS]`

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Настройка DNS для офисов HQ и BR  
- Основной DNS-сервер реализован на HQ-SRV.  
- Сервер должен обеспечивать разрешение имён в сетевые адреса устройств и обратно в соответствии с таблицей 2  
- В качестве DNS сервера пересылки используйте любой общедоступный DNS сервер  

</details>
<br/>

<table align="center">
  <tr>
    <td align="center">Устройство</td>
    <td align="center">Запись</td>
    <td align="center">Тип</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">hq-rtr.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">br-rtr.au-team.irpo</td>
    <td align="center">A</td>
  </tr>
  <tr>
    <td align="center">HQ-SRV</td>
    <td align="center">hq-srv.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI</td>
    <td align="center">hq-cli.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">br-srv.au-team.irpo</td>
    <td align="center">A</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">moodle.au-team.irpo</td>
    <td align="center">CNAME</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">wiki.au-team.irpo</td>
    <td align="center">CNAME</td>
  </tr>
</table>

<p align="center"><strong>Таблица 2</strong></p>

<br/>

<details>
<summary><strong>Настройка при помощи <code>bind9</code></strong>[Дольше]</summary>

<br/>

## > Настройка BIND9 на `HQ-SRV`<

### HQ-SRV

<br/>

**1.** Для работы с **DNS** требуется установить **`bind`** и доп. пакет командой:

```
apt-get install bind9 bind9-utils
```
<br/>

**2.** Далее необходимо сконфигурировать файл **`named.conf.options`** таким образом:

```
nano /etc/bind/named.conf.options
```

```
  listen-on { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; 172.16.0.0/30; };
  forwarders { 127.0.0.1; 8.8.8.8; 192.168.100.62; 8.8.4.4; };
  recursion yes;
  allow-query { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; 172.16.0.0/30; };
  allow-query-cache { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; 172.16.0.0/30; };
  allow-recursion { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; 172.16.0.0/30; };
  dnssec-validation auto;
```
<br/>

**3.** Конфигурация ключей **`rndc`**:
```
rndc-confgen > /etc/rndc.key 
```
</br>

**❗ Для проверки, можно использовать комманду:** 

```
named-checkconf
```
</br>

**4.** Далее необходимо запустить **утилиту** коммандой:

```
systemctl enable --now named
```
</br>

**5.** Далее требуется изменить конфигурацию файла на **`HQ-SRV`** **`resolv.conf`**:

```
nano /etc/resolv.conf
```

```
nameserver 192.168.100.62
search au-team.irpo
```
</br>

**6.** После чего требуется прописать в **`/etc/bind/named.conf.local`**:

```
zone "au-team.irpo" {
  type master;
  file "/etc/bind/au-team.irpo";
};

zone "100.168.192.in-addr.arpa" {
  type master;
  file "/etc/bind/100.168.192.in-addr.arpa";
};

zone "200.168.192.in-addr.arpa" {
  type master;
  file "/etc/bind/200.168.192.in-addr.arpa";
};

zone "0.168.192.in-addr.arpa" {
  type master;
  file "/etc/bind/0.168.192.in-addr.arpa";
};
```
</br>

**7.** Далее следующими командами **создаём копию** файла и присваиваем права:

```
cp /etc/bind/db.local /etc/bind/au-team.irpo
```

</br>

**8.** После чего приводим **файл `au-team.irpo`** к следующему виду:
```
nano /etc/bind/au-team.irpo
```

```
$TTL    1D
@       IN      SOA     hq-srv.au-team.irpo. root.au-team.irpo. (
                                2024102200      ; serial
                                12H             ; refresh
                                1H              ; retry
                                1W              ; expire
                                1H              ; ncache
                        )
@       IN      NS      hq-srv.au-team.irpo.
hq-rtr  IN      A       192.168.100.1
br-rtr  IN      A       192.168.0.1
hq-srv  IN      A       192.168.100.62
hq-cli  IN      A       192.168.200.3
br-srv  IN      A       192.168.0.2
moodle  IN      CNAME   hq-rtr
wiki    IN      CNAME   br-rtr
_ldap._tcp.au-team.irpo. IN SRV 0 5 389 br-srv.au-team.irpo.
_kerberos._tcp.au-team.irpo.        IN      SRV     0 100 88  br-srv.au-team.irpo.
_kdc._tcp.au-team.irpo.             IN      SRV     0 100 88  br-srv.au-team.irpo.
_kpasswd._tcp.au-team.irpo.         IN      SRV     0 100 464 br-srv.au-team.irpo.
```
</br>

</br>

**9.** После чего **создаем файлы** командами:
```
cp /etc/bind/db.127 /etc/bind/100.168.192.in-addr.arpa
cp /etc/bind/db.127 /etc/bind/200.168.192.in-addr.arpa
cp /etc/bind/db.127 /etc/bind/0.168.192.in-addr.arpa
```
</br>

**10.** После изменений файл **`100.168.192.in-addr.arpa`** выглядит так:
```
nano /etc/bind/100.168.192.in-addr.arpa
```

```
$TTL    1D
@       IN      SOA     hq-srv.au-team.irpo. root.au-team.irpo. (
                                2024102200      ; Serial
                                12H             ; Refresh
                                1H              ; Retry
                                1W              ; Expire
                                1H              ; Ncache
                        )
@       IN      NS      localhost.
1       IN      PTR     hq-rtr.au-team.irpo.
62      IN      PTR     hq-srv.au-team.irpo.
```

</br>

**11.** После изменений файл **`200.168.192.in-addr.arpa`** выглядит так:
```
nano /etc/bind/200.168.192.in-addr.arpa
```

```
$TTL    1D
@       IN      SOA     hq-srv.au-team.irpo. root.au-team.irpo. (
                                2024102200      ; Serial
                                12H             ; Refresh
                                1H              ; Retry
                                1W              ; Expire
                                1H              ; Ncache
                        )
@       IN      NS      localhost.
2       IN      PTR     hq-cli.au-team.irpo.
```

</br>

**12.** После изменений файл **`0.168.192.in-addr.arpa`** выглядит так:
```
nano /etc/bind/0.168.192.in-addr.arpa
```

```
$TTL    1D
@       IN      SOA     hq-srv.au-team.irpo. root.au-team.irpo. (
                                2024102200      ; Serial
                                12H             ; Refresh
                                1H              ; Retry
                                1W              ; Expire
                                1H              ; Ncache
                        )
@       IN      NS      localhost.
1       IN      PTR     br-rtr.au-team.irpo.
2       IN      PTR     br-srv.au-team.irpo.
```

### ❗ ❗ ❗ Все пробелы выше ^ ставятся TAB'ом

</br>

**13.** После чего можно проверить **ошибки** командой:
```
named-checkconf -z
```
</br>

**14.** А также перезапускаем **`bind`** командой:

```
systemctl restart named bind9
```
</br>

**15.** На всех устройствах локальной сети необходимо указать в конфигурационном файле `resolv.conf`:
```
nano /etc/resolv.conf
```

```
nameserver 192.168.100.62
search au-team.irpo
```
</br>

**16.** Проверить работоспособность можно **командой**:
```
nslookup **IP-адрес/DNS-имя**
```
</br>

</details>

<br/>

<details>
<summary><strong>Настройка при помощи <code>dnsmasq</code></strong>[Быстрее]</summary>
<br/>

Для начала устанавливаются необходимые пакеты:
```
apt install iptables iptables-persistent dnsmasq -y
```

После чего необходимо открыть порт 53
```
iptables -I INPUT -p udp --dport 53 -j ACCEPT
netfilter-persistent save
```
Дальше конфигурируем файл `/etc/dnsmasq.conf`
```
no-resolv
interface=ens192
read-ethers
listen-address=192.168.100.62
server=8.8.8.8
server=8.8.4.4
address=/hq-rtr.au-team.irpo/192.168.100.1
address=/hq-srv.au-team.irpo/192.168.100.62
address=/br-rtr.au-team.irpo/192.168.0.1
address=/br-srv.au-team.irpo/192.168.0.2
address=/hq-cli.au-team.irpo/192.168.200.3
srv-host=_ldap._tcp.au-team.irpo,br-srv.au-team.irpo,389
srv-host=_kerberos._tcp.au-team.irpo,br-srv.au-team.irpo,88
srv-host=_kdc._tcp.au-team.irpo,br-srv.au-team.irpo,88
srv-host=_kpasswd._tcp.au-team.irpo,br-srv.au-team.irpo,464
cname=moodle.au-team.irpo,hq-rtr.au-team.irpo
cname=wiki.au-team.irpo,br-rtr.au-team.irpo
```
Далее в файле hosts на HQ-SRV правим:
```
nano /etc/hosts
```
```
127.0.0.1  localhost
127.0.1.1  server.localdomain  server
192.168.100.1  hq-rtr.au-team.irpo  hq-rtr
192.168.100.62  hq-srv.au-team.irpo  hq-srv
192.168.0.1  br-rtr.au-team.irpo  br-rtr
192.168.0.2  br-srv.au-team.irpo  br-srv
192.168.200.3  hq-cli.au-team.irpo  hq-cli
```

Далее по окончанию настройки:
```
systemctl restart dnsmasq
```

После чего, на ВСЕХ машинах, в конфигурационном файле `/etc/resolv.conf` добавляем строку:
```
search au-team.irpo
nameserver 192.168.100.62
```

</details>
  
<br/>

## ✔️ Задание 11 `[ВРЕМЯ/ДАТА]`

### Настройте часовой пояс на всех устройствах, согласно месту проведения экзамена

<br/>

<details>
<summary><strong>Настройка <code>часового</code> пояса</strong></summary>
<br/>

## > Настройте часовой пояс на всех устройствах <
- На Linux настраивается часовой пояс командой:
```
timedatectl set-timezone Asia/Tomsk
```  

- Если на `CLI` возникли проблемы, делаем через настройки в графическом интерфейсе и настраиваем там временную зону

</details>
# <div align="center"><strong>⚙️</strong></div> <div align="center"><strong>МОДУЛЬ 1</strong></div>

<br/>

<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/%D0%A2%D0%BE%D0%BF%D0%BE%D0%BB%D0%BE%D0%B3%D0%B8%D1%8F.jpg" alt="Топология" />
</p>
<p align="center"><strong>Топология</strong></p>

<br/>

> [!WARNING]
> ## ПРЕДНАСТРОЙКА
>
> ```
> systemctl stop NetworkManager
> systemctl disable NetworkManager
> systemctl mask NetworkManager
> ```
></br>
>
> На всех устройствах, кроме **ISP**:
>```
>nano /etc/resolv.conf
>nameserver 77.88.8.8
>nameserver 1.1.1.1
>```
></br>
>
> На **РОУТЕРАХ** sysctl -p:
>```
> echo net.ipv4.ip_forward=1 > /etc/sysctl.conf
>```
></br> 
>
> **Сурс** листы везде:
> ```
>nano /etc/apt/sources.list
>```
>```
> # deb cdrom:.......
>  ↑
>  Ставим комментарий
>```
>```
>apt update
>```
</br>

<table>
  <thead>
    <tr>
      <th>Маска подсети</th>
      <th>Полная маска</th>
      <th>Сколько адресов вмещает</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/26</td>
      <td>255.255.255.192</td>
      <td align="center">62</td>
    </tr>
    <tr>
      <td>/27</td>
      <td>255.255.255.224</td>
      <td align="center">30</td>
    </tr>
    <tr>
      <td>/28</td>
      <td>255.255.255.240</td>
      <td align="center">14</td>
    </tr>
    <tr>
      <td>/29</td>
      <td>255.255.255.248</td>
      <td align="center">6</td>
    </tr>
  </tbody>
</table>

</br>

## ✔️ Задание 1 <code>[ Адрессация + Имя устройства]</code>

> [!WARNING]
> В инструкции по сетевой настройке используется редактирование: <strong>`etc/network/interfaces`</strong>

> [!NOTE]
> **Используй редактор файлов: `nano`**

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Произведите `базовую` настройку устройств

- Настройте имена устройств согласно топологии. Используйте полное доменное имя
- На всех устройствах необходимо сконфигурировать IPv4
- IP-адрес должен быть из приватного диапазона, в случае, если сеть локальная, согласно RFC1918
- Локальная сеть в сторону HQ-SRV(VLAN100) должна вмещать не более 64 адресов
- Локальная сеть в сторону HQ-CLI(VLAN200) должна вмещать не более 16 адресов
- Локальная сеть в сторону BR-SRV должна вмещать не более 32 адресов
- Локальная сеть для управления(VLAN999) должна вмещать не более 8 адресов
- Сведения об адресах занесите в отчёт, в качестве примера используйте Таблицу 3

</details>

<br/>

<details>
<summary><strong>Настройка имени</strong></summary>
  
<br/>

  - Для **Linux** используется команда: <strong>`hostnamectl set-hostname (имя устройства.au-team.irpo)`</strong>
  
  - Обновить имя можно введя команду: **`bash`**
### ISP
```
hostnamectl set-hostname isp.au-team.irpo
bash
```
<br/>

### HQ-RTR
```
hostnamectl set-hostname hq-rtr.au-team.irpo
bash
```
<br/>

### BR-RTR
```
hostnamectl set-hostname br-rtr.au-team.irpo
bash
```
<br/>

### HQ-SRV
```
hostnamectl set-hostname hq-srv.au-team.irpo
bash
```
<br/>

### HQ-CLI
```
hostnamectl set-hostname hq-cli.au-team.irpo
bash
```
<br/>

### BR-SRV
```
hostnamectl set-hostname br-srv.au-team.irpo
bash
```
<br/>

</details>

<details>
<summary><strong>Настройка адрессации в файле <code>etc/network/interfaces</code></strong></summary>
<br/>

### Настройка адресации (кроме HQ-CLI(он позже))
##
### ISP:
```
auto ens224
iface ens224 inet static
address 172.16.4.1/28
auto ens256
iface ens256 inet static
address 172.16.5.1/28
```

### HQ-RTR: (в 4 и 6 задании продолжение)
```
allow-hotplug ens192
iface ens192 inet static
address 172.16.4.2/28
gateway 172.16.4.1

auto gre1
iface gre1 inet tunnel
address 172.16.0.1
netmask 255.255.255.252
mode gre
local 172.16.4.2
endpoint 172.16.5.2
ttl 64
```

### BR-RTR: (в 6 задании продолжение)
```
allow-hotplug ens192
iface ens192 inet static
address 172.16.5.2/28
gateway 172.16.5.1

auto ens224
iface ens224 inet static
address 192.168.0.1/27

auto gre1
iface gre1 inet tunnel
address 172.16.0.2
netmask 255.255.255.252
mode gre
local 172.16.5.2
endpoint 172.16.4.2
ttl 64
```

### BR-SRV:

```
allow-hotplug ens192
iface ens192 inet static
address 192.168.0.2/27
gateway 192.168.0.1
dns-nameservers 192.168.100.62 192.168.0.2
dns-search au-team.irpo
```

### HQ-SRV:

```
allow-hotplug ens192
iface ens192 inet static
address 192.168.100.62/26
gateway 192.168.100.1
```

</details>

<details>
<summary><strong>Таблицы сетей</strong></summary>

</br>

<table align="center">
  <tr>
    <td align="center"><strong>Сеть</strong></td>
    <td align="center"><strong>Адрес подсети</strong></td>
    <td align="center"><strong>Пул-адресов</strong></td>
  </tr>
  <tr>
    <td align="center">SRV-Net (VLAN 100)</td>
    <td align="center">192.168.100.0/26</td>
    <td align="center">192.168.100.1-62</td>
  </tr>
  <tr>
    <td align="center">CLI-Net (VLAN 200)</td>
    <td align="center">192.168.200.0/28</td>
    <td align="center">192.168.200.1-14</td>
  </tr>
  <tr>
    <td align="center">MGMT (VLAN 999)</td>
    <td align="center">192.168.99.0/29</td>
    <td align="center">192.168.99.1-6</td>
  </tr>
  <tr>
    <td align="center">BR-Net</td>
    <td align="center">192.168.0.0/27</td>
    <td align="center">192.168.0.1-30</td>
  </tr>
  <tr>
    <td align="center">ISP-HQ</td>
    <td align="center">172.16.4.0/28</td>
    <td align="center">172.16.4.1 - 14</td>
  </tr>
  <tr>
    <td align="center">ISP-BR</td>
    <td align="center">172.16.5.0/28</td>
    <td align="center">172.16.5.1 - 14</td>
  </tr>
</table>
<p align="center"><strong>Таблица подсетей</strong></p>

#

<table align="center">
  <tr>
    <td align="center"><strong>Устройство</strong></td>
    <td align="center"><strong>Интерфейс</strong></td>
    <td align="center"><strong>IPv4/IPv6</strong></td>
    <td align="center"><strong>Маска/Префикс</strong></td>
    <td align="center"><strong>Шлюз</strong></td>
    <td align="center"><strong>Сеть</strong></td>
  </tr>
  <tr>
    <td align="center" rowspan="3">ISP</td>
    <td align="center">ens192</td>
    <td align="center">(DHCP)</td>
    <td align="center">(DHCP)</td>
    <td align="center">(DHCP)</td>
    <td align="center">INTERNET</td>
  </tr>
  <tr>
    <td align="center">ens224</td>
    <td align="center">172.16.4.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
    <td align="center">ISP-HQ-RTR</td>
  </tr>
  <tr>
    <td align="center">ens256</td>
    <td align="center">172.16.5.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
    <td align="center">ISP-BR-RTR</td>
  </tr>
  <tr>
    <td align="center" rowspan="3">HQ-RTR</td>
    <td align="center">ens192</td>
    <td align="center">172.16.4.2</td>
    <td align="center">/28</td>
    <td align="center">172.16.4.1</td>
    <td align="center">ISP-HQ-RTR</td>
  </tr>
  <tr>
    <td align="center">ens224.200</td>
    <td align="center">192.168.200.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
    <td align="center">HQ-RTR-CLI</td>
  </tr>
  <tr>
    <td align="center">ens224.100</td>
    <td align="center">192.168.100.1</td>
    <td align="center">/26</td>
    <td align="center"></td>
    <td align="center">HQ-RTR-SRV</td>
  </tr>
  <tr>
    <td align="center" rowspan="2">BR-RTR</td>
    <td align="center">ens192</td>
    <td align="center">172.16.5.2</td>
    <td align="center">/28</td>
    <td align="center">172.16.5.1</td>
    <td align="center">ISP-BR-RTR</td>
  </tr>
  <tr>
    <td align="center">ens224</td>
    <td align="center">192.168.0.1</td>
    <td align="center">/27</td>
    <td align="center"></td>
    <td align="center">BR-RTR-SRV</td>
  </tr>
  <tr>
    <td align="center">HQ-SRV</td>
    <td align="center">ens192</td>
    <td align="center">192.168.100.62</td>
    <td align="center">/26</td>
    <td align="center">192.168.100.1</td>
    <td align="center">HQ-RTR-SRV</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">ens192</td>
    <td align="center">192.168.0.2</td>
    <td align="center">/27</td>
    <td align="center">192.168.0.1</td>
    <td align="center">BR-RTR-SRV</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI</td>
    <td align="center">ens192</td>
    <td align="center">192.168.200.##(DHCP)</td>
    <td align="center">/28</td>
    <td align="center">192.168.200.1</td>
    <td align="center">HQ-RTR-CLI</td>
  </tr>
</table>
<p align="center"><strong>Таблица адресации</strong></p>

</details>
</br>

## ✔️ Задание 2 <code>[NAT на ISP]</code>

<br/>

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Настройка `ISP`

- Настройте адресацию на интерфейсах:

  - Интерфейс, подключенный к магистральному провайдеру, получает адрес по DHCP **[Выполнено в задании 1]**

  - Настройте маршруты по умолчанию там, где это необходимо 

  - Интерфейс, к которому подключен HQ-RTR, подключен к сети 172.16.4.0/28 **[Выполнено в задании 1]**

  - Интерфейс, к которому подключен BR-RTR, подключен к сети 172.16.5.0/28 **[Выполнено в задании 1]**

  - На ISP настройте динамическую сетевую трансляцию в сторону HQ-RTR и BR-RTR для доступа к сети Интернет

</details>

<br/>

<details>
<summary><strong>Настройка динамической трансляции <code>NAT</code></strong></summary>

### Настройка динамической сетевой трансляции на _`ISP`_

```
echo net.ipv4.ip_forward=1 > /etc/sysctl.conf
```
```
apt-get install iptables iptables-persistent –y
```
```
iptables –t nat –A POSTROUTING –s 172.16.4.0/28 –o ens192 –j MASQUERADE  
iptables –t nat –A POSTROUTING –s 172.16.5.0/28 –o ens192 –j MASQUERADE
netfilter-persistent save
systemctl restart netfilter-persistent  
```
</br>

<details>
<summary><strong><code>Либо другая настройка</code></strong></summary>  

```  
apt install iptables  
apt install iptables iptables-persistent  
iptables –t nat –A POSTROUTING –s 172.16.4.0/28 –o ens192 –j MASQUERADE   
iptables –t nat –A POSTROUTING –s 172.16.5.0/28 –o ens192 –j MASQUERADE   
iptables-save > /etc/iptables/rules.v4  
```
  
> **`ens192`** - интерфейс с которого приходит **интернет**
> 
> Для проверки можно использовать команду: **`iptables –L –t nat`** - должны высветится в Chain POSTROUTING две настроенные подсети  

> Для того, чтобы сбросить настройку *nat*, можно использовать команду **`iptables -t nat -F`**

#
</details>

</details>

<br/>

## ✔️ Задание 3 `[Создание учетных записей]` 

> [!WARNING]
> Обратите внимание, что группа <code><strong>WHEEL</strong></code> не используется!
<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Создание локальных учетных записей

- Создайте пользователя sshuser на серверах HQ-SRV и BR-SRV

  - Пароль пользователя sshuser с паролем P@ssw0rd

  - Идентификатор пользователя 1010

  - Пользователь sshuser должен иметь возможность запускать sudo без дополнительной аутентификации.

- Создайте пользователя net_admin на маршрутизаторах HQ-RTR и BR-RTR

  - Пароль пользователя net_admin с паролем P@$$word

  - При настройке на EcoRouter пользователь net_admin должен обладать максимальными привилегиями

  - При настройке ОС на базе Linux, запускать sudo без дополнительной аутентификации
 
</details>

<br/>

<details>
<summary><strong>Как создать <code>sshuser</code></strong></summary>
<br/>

- ## Создание учёток _`sshuser`_ производится на _`HQ-SRV`_ и _`BR-SRV`_

**1.** Создаём sshuser следующими командами:
```
useradd sshuser -u 1010
passwd sshuser
P@ssw0rd
```
<br/>

**2.** После чего даем пользователю <strong><code>root</strong></code> права:
```yml
usermod -aG sudo sshuser
```

<br/>

**3.** Добавляем следующую строку в файле `/sudoers`:
```
nano /etc/sudoers
```

```yml
sshuser ALL=(ALL) NOPASSWD:ALL
```
> Позволяет запускать **`sudo`** без аутентификации

<br/>

**4.** Создаем и задаем необходимые права на домашнюю папку
```
mkdir /home/sshuser
chown sshuser:sshuser /home/sshuser
chmod 700 /home/sshuser
```
<br/>
</details>

<details>
<summary><strong>Как создать <code>net_admin</code></strong></summary>
<br/>

- ## Пользователь _`net_admin`_ на _`HQ-RTR`_ и _`BR-RTR`_

**1.** Создаём **`net_admin`**, следующими командами, но уже без `-u 1010` и с новым паролем:
```
useradd net_admin
passwd net_admin
P@$$word
```
<br/>

**2.** После чего даем пользователю <strong><code>root</strong></code> права:
```yml
usermod -aG sudo net_admin
```
<br/>

**3.** Добавляем следующую строку в `/sudoers`:
```
nano /etc/sudoers
```
```yml
net_admin ALL=(ALL) NOPASSWD:ALL
```
> Позволяет запускать **`sudo`** без аутентификации
<br/>

**4.** Создаем и задаем необходимые права на домашнюю папку
```
mkdir /home/net_admin
chown net_admin:net_admin /home/net_admin
chmod 700 /home/net_admin
```
<br/>

</details>

<br/>

## ✔️ Задание 4 `[VLAN]`

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Настройте на интерфейсе `HQ-RTR` в сторону офиса `HQ` виртуальный коммутатор

- Сервер HQ-SRV должен находиться в ID VLAN 100
- Клиент HQ-CLI в ID VLAN 200
- Создайте подсеть управления с ID VLAN 999
- Основные сведения о настройке коммутатора и выбора реализации разделения на VLAN занесите в отчёт

</details>
<br/>

<details>
<summary><strong>Настройка VLAN на <code>HQ-RTR</code></strong></summary>

## Настройка VLAN на **`HQ-RTR`**

- Для начала установи пакет _**`vlan`**_:

```
apt install vlan
```

- После чего добавляем модуль _**`modprobe 8021q`**_ командой:
```
echo 8021q >> /etc/modules
```
- Далее переходим к конфигурации файла _**`/etc/network/interfaces`**_ и приводим её к виду:
```
nano /etc/network/interfaces
```

```
# The primary network interface
auto ens192  
iface ens192 inet static  
address 172.16.4.2/28
gateway 172.16.4.1

auto gre1
iface gre1 inet tunnel
address 172.16.0.1
netmask 255.255.255.252
mode gre
local 172.16.4.2
endpoint 172.16.5.2
ttl 64
  
auto ens224  
iface ens224 inet static  
address 192.168.100.1/26 
  
auto ens224:1  
iface ens224:1 inet static  
address 192.168.200.1/28

auto ens224:2  
iface ens224:2 inet static  
address 192.168.99.9/29

auto ens224.100  
iface ens224.100 inet manual   
Vlan-raw-device ens224  
  
auto ens224.200  
iface ens224.200 inet manual   
Vlan-raw-device ens224:1

auto ens224.999  
iface ens224.999 inet manual   
Vlan-raw-device ens224:2
```

</details>

<br/>

## ✔️ Задание 5 `[SSH]`

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Настройка безопасного удаленного доступа на серверах `HQ-SRV` и `BR-SRV`

- Для подключения используйте порт 2024
- Разрешите подключения только пользователю sshuser
- Ограничьте количество попыток входа до двух
- Настройте баннер «Authorized access only»

</details>
<br/>

<details>
<summary><strong>Настройка <code>SSH</code></strong></summary>
<br/>

### SSH настраиваем на `HQ-SRV` и `BR-SRV`

**1.** Для настройки **SSH** необходимо его установить коммандой:
```
apt-get install openssh-server -y
```

</br>

**2.** После чего необходимо добавить строчки в файл **`sshd_config`**:
```
nano /etc/ssh/sshd_config
```

```
Port 2024
MaxAuthTries 2
PasswordAuthentication yes
Banner /etc/ssh/banner
AllowUsers  sshuser
           ^ - это TAB
```
<br/>

**3.** После чего требуется создать файл **`/etc/ssh/banner`** и привести его в следующую форму:
```
----------------------
Authorized access only
----------------------
```
</br>

**4.** Далее необходимо перезапустить **`SSH`** коммандой:
  
```
systemctl restart sshd
```


</details>

<br/>

## ✔️ Задание 6 `[GRE-TUNNEL]`

> [!WARNING]
> **Не используйте** устройства с именем **`tun0`, `gre0` и `sit0`**, т.к они являются зарезервированными в iproute2 («base devices») и имеют особое поведение.

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Между офисами `HQ` и `BR` необходимо сконфигурировать _`IP-туннель`_

- Сведения о туннеле занесите в отчёт  
- На выбор технологии GRE или IP in IP
</details>
<br/>

<details>
<summary><strong>Настройка GRE в файле <code>etc/network/interfaces</code>на <code>HQ-RTR</code></strong></summary>
<br/>

Для настройки _**`GRE`**_ туннеля на **`HQ-RTR`** переходим в файл конфигурации

```
nano /etc/network/interfaces
```

И убеждаемся в наличии этих строк:
```
auto gre1
iface gre1 inet tunnel
address 172.16.0.1
netmask 255.255.255.252
mode gre
local 172.16.4.2
endpoint 172.16.5.2
ttl 64
```

Для работы туннеля необходимо добавить строчку в файл `/etc/modules`
```
echo gre_ip >> /etc/modules
```
```
systemctl restart networking
```
</details>

<details>
<summary><strong>Настройка GRE в файле <code>etc/network/interfaces</code> на <code>BR-RTR</code></strong></summary>
<br/>
Для настройки *GRE* туннеля на *BR-RTR* переходим в файл конфигурации
  
```
nano /etc/network/interfaces
```

И убеждаемся в наличии этих строк:
```
auto gre1
iface gre1 inet tunnel
address 172.16.0.2
netmask 255.255.255.252
mode gre
local 172.16.5.2
endpoint 172.16.4.2
ttl 64
```

Для работы туннеля необходимо добавить строчку в файл `/etc/modules`
```
echo gre_ip >> /etc/modules
```
```
systemctl restart networking
```
</details>
<br/>


## ✔️ Задание 7 `[OSPF]`

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Обеспечьте динамическую маршрутизацию: ресурсы одного офиса должны быть доступны из другого офиса. Для обеспечения динамической маршрутизации используйте `link state` протокол на ваше усмотрение

- Разрешите выбранный протокол только на интерфейсах в ip туннеле  
- Маршрутизаторы должны делиться маршрутами только друг с другом  
- Обеспечьте защиту выбранного протокола посредством парольной защиты  
- Сведения о настройке и защите протокола занесите в отчёт

</details>
<br/>

<details>
<summary><strong>Настройка <code>OSPF</code></strong></summary>
<br/>


## Настройка `OSPF` производится на `HQ-RTR` и `BR-RTR`:

### HQ-RTR

**1.** Устанавливаем пакет `FRR`

```
sudo apt install -y frr
```

**2.** В конфигурационном файле `/etc/frr/daemons` необходимо активировать выбранный протокол `OSPF` для дальнейшей реализации его настройки:

```
nano /etc/frr/daemons

!!! Ищем следующую строку и меняем с (no) на (yes)
ospfd = yes

```

**3.** Далее перезаргужаем и добавляем в автозагрузку службу **`FRR`**

```
systemctl restart frr
systemctl enable --now frr
```

**4.** Переходим в интерфейс управления симуляцией **`FRR`** командой:
```
vtysh
```

**5.** Пишем команды для настройки **маршрутизации:**
 
```
conf t
router ospf
  passive-interface default
  router-id 1.1.1.1
  network 172.16.0.0/30 area 0
  network 192.168.100.0/26 area 1
  network 192.168.200.0/28 area 2
  area 0 authentication
exit

int gre1
  no ip ospf network broadcast
  no ip ospf passive
  ip ospf authentication
  ip ospf authentication-key password
(config-if)exit
(config)exit
#write
```
<br>

### BR-RTR

**1-4.** Пункты такие же как и в HQ-RTR

**5.** Пишем команды для настройки **маршрутизации:**

**Меняется:** 

- `id-router: 2.2.2.2`

- `network 192.168.0.0/27 area 3`

- `network 172.16.0.0/30 area 0`

```
conf t
router ospf
  passive-interface default
  router-id 2.2.2.2
  network 192.168.0.0/27 area 3
  network 172.16.0.0/30 area 0
  area 0 authentication
exit

int gre1
  no ip ospf network broadcast
  no ip ospf passive
  ip ospf authentication
  ip ospf authentication-key password
(config-if)exit
(config)exit
#write
```

### ПРОВЕРКА

Пингуем: **`BR-SRV - > HQ-SRV`** и **`BR-SRV - > HQ-CLI`**

Проверка в **FRR**:

```
vtysh
  show ip ospf neighbor
  show ip route ospf
```

</details>

<br/>


<br/>
Настройка маршрутов, если не работает <code><strong>OSPF</strong></code>:

<br/>
<details>
<summary><strong>Настройка <code>маршрутов</code></strong></summary>
<br/>

После создания влана настраиваем маршрут для подсетей (чтобы они видели друг друга)  
На роутере *HQ-RTR* настройка выглядит так:
```
sudo nano /etc/systemd/system/iproute.service
```

<br/>

После чего добавляем текст:
```
[Unit]
Description=iproute
After=network.target

[Service]
Type=oneshot
ExecStart=/sbin/ip route add 192.168.0.0/27 via 172.16.0.2 dev gre1
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

<br/>

На роутере *BR-RTR* создаем тот же файли настраивеим:
```
sudo nano /etc/systemd/system/iproute.service
Для HQ-RTR:
[Unit]
Description=iproute
After=network.target

[Service]
Type=oneshot
ExecStart=/sbin/ip route add 192.168.100.0/26 via 172.16.0.1 dev gre1
ExecStart=/sbin/ip route add 192.168.200.0/28 via 172.16.0.1 dev gre1
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

<br/>

После на обоих устройствах прописываем:
```
systemctl daemon-reload
systemctl enable iproute.service
systemctl start iproute.service
```

</details>

<br/>

## ✔️ Задание 8 `[NAT на HQ-rtr и BR-rtr]`

> [!NOTE]
> Пул адрессов можно посмотреть в таблице <strong>[Задания 1](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module1#задание-1)</strong> или же отталкиваться от вашей адрессации.

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Настройка динамической трансляции адресов

- Настройте динамическую трансляцию адресов для обоих офисов.  
- Все устройства в офисах должны иметь доступ к сети Интернет

</details>
<br/>

<details>
<summary><strong>Настройка NAT на <code>BR</code> и <code>HQ</code></strong></summary>
<br/>

## > Настройка динамической трансляции адресов <

> ### Настройка на `ISP` выполнена в [Задании 2](https://github.com/Flicks1383/Demo09.02.06_2025/tree/main/module1#задание-2)

### Настройка динамической сетевой трансляции на `HQ-RTR`
```
apt-get install iptables iptables-persistent –y
iptables –t nat –A POSTROUTING –s 192.168.100.0/26 –o ens192 –j MASQUERADE
iptables –t nat –A POSTROUTING –s 192.168.200.0/28 –o ens192 –j MASQUERADE
netfilter-persistent save
systemctl restart netfilter-persistent  
```
### Настройка динамической сетевой трансляции на `BR-RTR`

```
apt-get install iptables iptables-persistent –y
iptables –t nat –A POSTROUTING –s 192.168.0.0/27 –o ens192 –j MASQUERADE
netfilter-persistent save
systemctl restart netfilter-persistent  
```
> Для того, чтобы **сбросить** настройку *nat*, можно использовать команду **`iptables -t nat -F`**

</br>

</details>

</br>

## ✔️ Задание 9 `[DHCP]`

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Настройка протокола динамической конфигурации хостов

- Настройте нужную подсеть  
- Для офиса HQ в качестве сервера DHCP выступает маршрутизатор HQ-RTR.  
- Клиентом является машина HQ-CLI.  
- Исключите из выдачи адрес маршрутизатора  
- Адрес шлюза по умолчанию – адрес маршрутизатора HQ-RTR.  
- Адрес DNS-сервера для машины HQ-CLI – адрес сервера HQ-SRV.  
- DNS-суффикс для офисов HQ – au-team.irpo  
- Сведения о настройке протокола занесите в отчёт

</details>
<br/>

<details>
<summary><strong>Настройка DHCP на <code>HQ-RTR</code></strong></summary>
<br/>

## > Настройка _`DHCP`_ на _`HQ-RTR`_ для `CLI` <

<br/>

**1.** Устанавливаем сам **DHCP-сервер**:  
```
apt install isc-dhcp-server
```
<br/>

**2.** После чего переходим в конфигурацию файла `dhcpd.conf` и добавляем следующие строчки:
```
nano /etc/dhcp/dhcpd.conf
```

```
subnet 192.168.200.0 netmask 255.255.255.240 {
  range 192.168.200.2 192.168.200.14;
  option domain-name-servers 192.168.100.62;
  option domain-name "au-team.irpo";
  option routers 192.168.200.1;
  default-lease-time 600;
  max-lease-time 7200;
}
```
<br/>

**3.** После чего переходим в конфигурацию файла `isc-dhcp-server` и меняем ее добалвяя данный текст:
```
nano /etc/default/isc-dhcp-server

INTERFACESv4="ens224:1" - порт смотрящий в сторону CLI
```

<br/>

**4.** Включаем сервиc **`DHCP`** и добавляем в автозагрузку на **`HQ-RTR`**:
```
systemctl start isc-dhcp-server
systemctl enable isc-dhcp-server
```

**5.** Далее на клиенсткой машине необходимо в настройках сет.адаптера выбрать режим **DHCP** и проверить работоспособность
<br/>

</details>

</br>

## ✔️ Задание 10 `[DNS]`

<details>
<summary><strong>[ОПИСАНИЕ ЗАДАНИЙ]</strong></summary>

### Настройка DNS для офисов HQ и BR  
- Основной DNS-сервер реализован на HQ-SRV.  
- Сервер должен обеспечивать разрешение имён в сетевые адреса устройств и обратно в соответствии с таблицей 2  
- В качестве DNS сервера пересылки используйте любой общедоступный DNS сервер  

</details>
<br/>

<table align="center">
  <tr>
    <td align="center">Устройство</td>
    <td align="center">Запись</td>
    <td align="center">Тип</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">hq-rtr.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">br-rtr.au-team.irpo</td>
    <td align="center">A</td>
  </tr>
  <tr>
    <td align="center">HQ-SRV</td>
    <td align="center">hq-srv.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI</td>
    <td align="center">hq-cli.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">br-srv.au-team.irpo</td>
    <td align="center">A</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">moodle.au-team.irpo</td>
    <td align="center">CNAME</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">wiki.au-team.irpo</td>
    <td align="center">CNAME</td>
  </tr>
</table>

<p align="center"><strong>Таблица 2</strong></p>

<br/>

<details>
<summary><strong>Настройка при помощи <code>bind9</code></strong>[Дольше]</summary>

<br/>

## > Настройка BIND9 на `HQ-SRV`<

### HQ-SRV

<br/>

**1.** Для работы с **DNS** требуется установить **`bind`** и доп. пакет командой:

```
apt-get install bind9 bind9-utils
```
<br/>

**2.** Далее необходимо сконфигурировать файл **`named.conf.options`** таким образом:

```
nano /etc/bind/named.conf.options
```

```
  listen-on { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; 172.16.0.0/30; };
  forwarders { 127.0.0.1; 8.8.8.8; 192.168.100.62; 8.8.4.4; };
  recursion yes;
  allow-query { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; 172.16.0.0/30; };
  allow-query-cache { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; 172.16.0.0/30; };
  allow-recursion { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; 172.16.0.0/30; };
  dnssec-validation auto;
```
<br/>

**3.** Конфигурация ключей **`rndc`**:
```
rndc-confgen > /etc/rndc.key 
```
</br>

**❗ Для проверки, можно использовать комманду:** 

```
named-checkconf
```
</br>

**4.** Далее необходимо запустить **утилиту** коммандой:

```
systemctl enable --now named
```
</br>

**5.** Далее требуется изменить конфигурацию файла на **`HQ-SRV`** **`resolv.conf`**:

```
nano /etc/resolv.conf
```

```
nameserver 192.168.100.62
search au-team.irpo
```
</br>

**6.** После чего требуется прописать в **`/etc/bind/named.conf.local`**:

```
zone "au-team.irpo" {
  type master;
  file "/etc/bind/au-team.irpo";
};

zone "100.168.192.in-addr.arpa" {
  type master;
  file "/etc/bind/100.168.192.in-addr.arpa";
};

zone "200.168.192.in-addr.arpa" {
  type master;
  file "/etc/bind/200.168.192.in-addr.arpa";
};

zone "0.168.192.in-addr.arpa" {
  type master;
  file "/etc/bind/0.168.192.in-addr.arpa";
};
```
</br>

**7.** Далее следующими командами **создаём копию** файла и присваиваем права:

```
cp /etc/bind/db.local /etc/bind/au-team.irpo
```

</br>

**8.** После чего приводим **файл `au-team.irpo`** к следующему виду:
```
nano /etc/bind/au-team.irpo
```

```
$TTL    1D
@       IN      SOA     hq-srv.au-team.irpo. root.au-team.irpo. (
                                2024102200      ; serial
                                12H             ; refresh
                                1H              ; retry
                                1W              ; expire
                                1H              ; ncache
                        )
@       IN      NS      hq-srv.au-team.irpo.
hq-rtr  IN      A       192.168.100.1
br-rtr  IN      A       192.168.0.1
hq-srv  IN      A       192.168.100.62
hq-cli  IN      A       192.168.200.3
br-srv  IN      A       192.168.0.2
moodle  IN      CNAME   hq-rtr
wiki    IN      CNAME   br-rtr
_ldap._tcp.au-team.irpo. IN SRV 0 5 389 br-srv.au-team.irpo.
_kerberos._tcp.au-team.irpo.        IN      SRV     0 100 88  br-srv.au-team.irpo.
_kdc._tcp.au-team.irpo.             IN      SRV     0 100 88  br-srv.au-team.irpo.
_kpasswd._tcp.au-team.irpo.         IN      SRV     0 100 464 br-srv.au-team.irpo.
```
</br>

</br>

**9.** После чего **создаем файлы** командами:
```
cp /etc/bind/db.127 /etc/bind/100.168.192.in-addr.arpa
cp /etc/bind/db.127 /etc/bind/200.168.192.in-addr.arpa
cp /etc/bind/db.127 /etc/bind/0.168.192.in-addr.arpa
```
</br>

**10.** После изменений файл **`100.168.192.in-addr.arpa`** выглядит так:
```
nano /etc/bind/100.168.192.in-addr.arpa
```

```
$TTL    1D
@       IN      SOA     hq-srv.au-team.irpo. root.au-team.irpo. (
                                2024102200      ; Serial
                                12H             ; Refresh
                                1H              ; Retry
                                1W              ; Expire
                                1H              ; Ncache
                        )
@       IN      NS      localhost.
1       IN      PTR     hq-rtr.au-team.irpo.
62      IN      PTR     hq-srv.au-team.irpo.
```

</br>

**11.** После изменений файл **`200.168.192.in-addr.arpa`** выглядит так:
```
nano /etc/bind/200.168.192.in-addr.arpa
```

```
$TTL    1D
@       IN      SOA     hq-srv.au-team.irpo. root.au-team.irpo. (
                                2024102200      ; Serial
                                12H             ; Refresh
                                1H              ; Retry
                                1W              ; Expire
                                1H              ; Ncache
                        )
@       IN      NS      localhost.
2       IN      PTR     hq-cli.au-team.irpo.
```

</br>

**12.** После изменений файл **`0.168.192.in-addr.arpa`** выглядит так:
```
nano /etc/bind/0.168.192.in-addr.arpa
```

```
$TTL    1D
@       IN      SOA     hq-srv.au-team.irpo. root.au-team.irpo. (
                                2024102200      ; Serial
                                12H             ; Refresh
                                1H              ; Retry
                                1W              ; Expire
                                1H              ; Ncache
                        )
@       IN      NS      localhost.
1       IN      PTR     br-rtr.au-team.irpo.
2       IN      PTR     br-srv.au-team.irpo.
```

### ❗ ❗ ❗ Все пробелы выше ^ ставятся TAB'ом

</br>

**13.** После чего можно проверить **ошибки** командой:
```
named-checkconf -z
```
</br>

**14.** А также перезапускаем **`bind`** командой:

```
systemctl restart named bind9
```
</br>

**15.** На всех устройствах локальной сети необходимо указать в конфигурационном файле `resolv.conf`:
```
nano /etc/resolv.conf
```

```
nameserver 192.168.100.62
search au-team.irpo
```
</br>

**16.** Проверить работоспособность можно **командой**:
```
nslookup **IP-адрес/DNS-имя**
```
</br>

</details>

<br/>

<details>
<summary><strong>Настройка при помощи <code>dnsmasq</code></strong>[Быстрее]</summary>
<br/>

Для начала устанавливаются необходимые пакеты:
```
apt install iptables iptables-persistent dnsmasq -y
```

После чего необходимо открыть порт 53
```
iptables -I INPUT -p udp --dport 53 -j ACCEPT
netfilter-persistent save
```
Дальше конфигурируем файл `/etc/dnsmasq.conf`
```
no-resolv
interface=ens192
read-ethers
listen-address=192.168.100.62
server=8.8.8.8
server=8.8.4.4
address=/hq-rtr.au-team.irpo/192.168.100.1
address=/hq-srv.au-team.irpo/192.168.100.62
address=/br-rtr.au-team.irpo/192.168.0.1
address=/br-srv.au-team.irpo/192.168.0.2
address=/hq-cli.au-team.irpo/192.168.200.3
srv-host=_ldap._tcp.au-team.irpo,br-srv.au-team.irpo,389
srv-host=_kerberos._tcp.au-team.irpo,br-srv.au-team.irpo,88
srv-host=_kdc._tcp.au-team.irpo,br-srv.au-team.irpo,88
srv-host=_kpasswd._tcp.au-team.irpo,br-srv.au-team.irpo,464
cname=moodle.au-team.irpo,hq-rtr.au-team.irpo
cname=wiki.au-team.irpo,br-rtr.au-team.irpo
```
Далее в файле hosts на HQ-SRV правим:
```
nano /etc/hosts
```
```
127.0.0.1  localhost
127.0.1.1  server.localdomain  server
192.168.100.1  hq-rtr.au-team.irpo  hq-rtr
192.168.100.62  hq-srv.au-team.irpo  hq-srv
192.168.0.1  br-rtr.au-team.irpo  br-rtr
192.168.0.2  br-srv.au-team.irpo  br-srv
192.168.200.3  hq-cli.au-team.irpo  hq-cli
```

Далее по окончанию настройки:
```
systemctl restart dnsmasq
```

После чего, на ВСЕХ машинах, в конфигурационном файле `/etc/resolv.conf` добавляем строку:
```
search au-team.irpo
nameserver 192.168.100.62
```

</details>
  
<br/>

## ✔️ Задание 11 `[ВРЕМЯ/ДАТА]`

### Настройте часовой пояс на всех устройствах, согласно месту проведения экзамена

<br/>

<details>
<summary><strong>Настройка <code>часового</code> пояса</strong></summary>
<br/>

## > Настройте часовой пояс на всех устройствах <
- На Linux настраивается часовой пояс командой:
```
timedatectl set-timezone Asia/Tomsk
```  

- Если на `CLI` возникли проблемы, делаем через настройки в графическом интерфейсе и настраиваем там временную зону

</details>
