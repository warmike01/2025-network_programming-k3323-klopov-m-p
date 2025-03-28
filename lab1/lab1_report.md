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

На неё нужно было установить VPN-сервер с протоколом, который не режется Роскомнадзором. Поэтому WireGuard, IKEv2 и OpenVPN не подходят (другие вроде как настраивали OpenVPN на 8080 порту и у них работало, но у меня не вышло). Shadowsocks работает, но это не VPN в привычном понимании. Поэтому остались только IPsec+L2TP с IKEv1 и SSTP. Материалов по первому варианту в интернете больше и при этом он гарантированно работает (так как ИТМОшный VPN именно на нём), поэтому решил делать его.

Сервер был настроен с помощью скрипта из https://github.com/hwdsl2/setup-ipsec-vpn командами *wget https://get.vpnsetup.net -O vpn.sh*, затем *sudo VPN_SKIP_IKEV2=yes sh vpn.sh*. Настраивать дополнительно ничего не нужно, по завершению выполнения скрипта он выдаёт данные для входа (ключ, логин, пароль):

![Данные для входа](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab1/login.PNG)

Работоспособность сервера можно сразу же проверить подключением с гарантированно работоспособного клиента - например, встроенного VPN-клиента Windows:

![Работает](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab1/windows.PNG)
