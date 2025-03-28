University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)

Year: 2025

Group: K3323

Author: Klopov Mikhail Pavlovich

Lab: Lab1

Date of create: 25.03.2025

Date of finished: 28.03.2025

# Отчет по лабораторной работе №1 "Установка CHR и Ansible, настройка VPN"

## Ход работы

### Server side

Была взята виртуалка с Ubuntu 20.04 на Селектеле:

![Виртуалка](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab1/Screenshot%20from%202025-03-26%2002-06-59.png)

Сначала на него была установлен Ansible:

![Ansible](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab1/Screenshot%20from%202025-03-26%2002-06-59.png)

Это было просто. Сложнее - установить VPN-сервер с протоколом, который не режется Роскомнадзором. Поэтому WireGuard, IKEv2 и OpenVPN не подходят (другие вроде как настраивали OpenVPN на 8080 порту и у них работало, но у меня не вышло). Shadowsocks работает, но это не VPN в привычном понимании. Поэтому остались только IPsec+L2TP с IKEv1 и SSTP. Материалов по первому варианту в интернете больше и при этом он гарантированно работает (так как ИТМОшный VPN именно на нём), поэтому решил делать его.

Сервер был настроен с помощью скрипта из https://github.com/hwdsl2/setup-ipsec-vpn командами *wget https://get.vpnsetup.net -O vpn.sh*, затем *sudo VPN_SKIP_IKEV2=yes sh vpn.sh*. Настраивать дополнительно ничего не нужно, по завершению выполнения скрипта он выдаёт данные для входа (ключ, логин, пароль):

![Данные для входа](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab1/login.PNG)

Работоспособность сервера можно сразу же проверить подключением с гарантированно работоспособного клиента - например, встроенного VPN-клиента Windows:

![Работает](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab1/windows.PNG)

### Client side

В Virtualbox была создана виртуалка на базе образа диска с официального сайта:

![Виртуальный роутер](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab1/Screenshot%20from%202025-03-28%2021-35-31.png)

Для возможности доступа к виртуальному роутеру по SSH необходимо в его настройках пробросить порт:

![Проброс порта](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab1/Screenshot%20from%202025-03-28%2021-54-39.png)

После этого виртуалку можно настраивать по SSH. После многих проб и ошибок была выведена следующая последовательность команд для подключения к серверу (секреты замаскированы):

```
 /ip ipsec profile add dh-group=modp2048 enc-algorithm=aes-256 name=ipsec-profile
/ip ipsec proposal add name=connect auth-algorithms=sha1 enc-algorithms=aes-256-cbc
/ip ipsec peer add address=79.141.71.4/32 passive=no exchange-mode=main name=server profile=ipsec-profile
/ip ipsec identity add auth-method=pre-shared-key peer=server secret=<psk>
/interface l2tp-client add connect-to=79.141.71.4 disabled=no name=l2tp user=<username> password=<password> use-ipsec=yes
```
Сервер выдаёт роутеру IP-адрес:

![IP](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab1/Screenshot%20from%202025-03-28%2022-11-21.png)


По нему роутер можно пинговать с сервера:

![ping](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab1/ping.PNG)

Следить за работой сервера можно, прописав в его консоли *journalctl -f -u ipsec*:

![Лог сервера](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab1/log.PNG)

