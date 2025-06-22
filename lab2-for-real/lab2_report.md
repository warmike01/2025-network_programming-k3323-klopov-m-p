University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://itmo.ru/ru/viewfaculty/19/fakultet_prikladnoy_informatiki.htm)

Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)

Year: 2025

Group: K3323

Author: Klopov Mikhail Pavlovich

Lab: Lab2

Date of create: A long time ago, in a galaxy far, far away...

Date of finished: 22.06.2025

# Отчет по лабораторной работе №2 "Развертывание дополнительного CHR, первый сценарий Ansible"

Всё.

Я сдаюсь.

Я попробовал все протоколы, которые только поддерживает микротик. Ничего не работает. Только IPsec/L2TP/IKEv1, позволяющий подключиться только одному устройству с одного NATа. А нам нужно два.

Остался самый последний вариант, самый простой и радикальный: сделать так, чтобы два роутера не подключались с одного NATа.

План такой: у меня на руках два компьютера, один с Ubuntu (на котором была сделана первая лабораторная), второй с Windows. На комп с Windows тоже ставим виртуалбокс. Он будет подключаться к интернету через обычный роутер, а на комп с Ubuntu раздадим интернет с 4G модема (с телефона не вышло, появляется ошибка). 

Используем знакомые образы Mikrotik CHR 6.49.18 Stable (на 7 версии команды для подключения почему-то не работают), и знакомую виртуалку на Селектеле.

Командой *wget https://get.vpnsetup.net -O vpn.sh && VPN_SKIP_IKEV2=yes sh vpn.sh* создаем VPN-сервер, затем командой *addvpnuser.sh* добавляем ещё одного пользователя.

