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

Остался самый последний вариант, самый простой и радикальный (ну кроме разве что покупки билетов за границу): сделать так, чтобы два роутера не подключались с одного NATа.

План такой: у меня на руках два компьютера, один с Ubuntu (на котором была сделана первая лабораторная), второй с Windows. На комп с Windows тоже ставим виртуалбокс. Комп с Ubuntu будет подключаться к интернету через обычный роутер, а на комп с Windows раздадим интернет с 4G модема Мегафона (пытался использовать телефон на МТС в режиме модема, но не вышло, появляется ошибка). 

Используем знакомые образы Mikrotik CHR 6.49.18 Stable (на 7 версии команды для подключения почему-то не работают), на которых, как и в прошлой лабе, пробрасываем 22 порт, и знакомую виртуалку на Селектеле.

Командой *wget https://get.vpnsetup.net -O vpn.sh && VPN_SKIP_IKEV2=yes sh vpn.sh* создаем VPN-сервер, затем командой *addvpnuser.sh* добавляем ещё одного пользователя.

Теперь заходим на виртуалки по SSH и прописываем знакомые команды:

```
/ip ipsec profile add dh-group=modp2048 enc-algorithm=aes-256 name=ipsec-profile
/ip ipsec proposal add name=connect auth-algorithms=sha1 enc-algorithms=aes-256-cbc
/ip ipsec peer add address=<ip>/32 passive=no exchange-mode=main name=server profile=ipsec-profile
/ip ipsec identity add auth-method=pre-shared-key peer=server secret=<psk>
/interface l2tp-client add connect-to=<ip> disabled=no name=l2tp user=<username> password=<password> use-ipsec=yes
```

![Linux](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2-for-real/10.PNG)

![Windows](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2-for-real/11.PNG)

Часть работы, с которой я воевал больше двух месяцев, выполнена! Ура! Ура! Ура...

С предыдущих попыток у меня остались уже написанные inventory и playbook (добавлены в репозиторий), осталось только их исполнить.

Сначала нужно с сервера зайти на роутеры по SSH, чтобы они добавились в known_hosts:

![SSH](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2-for-real/ssh.PNG)

Затем прописать *pip install ansible-pylibssh* и *ansible-galaxy collection install community.routeros*.

После этого можно проверить связь с роутерами командой *ansible all -m ping -i inventory.yaml*:

![SSH](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2-for-real/ping.PNG)

Осталось только исполнить плейбук командой *ansible-playbook playbook.yaml -i inventory.yaml*:

![Изменения](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2-for-real/changes.PNG)


![Конфигурация](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2-for-real/system_info.PNG)

![OSPF](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2-for-real/ospf.PNG)

Всё!
