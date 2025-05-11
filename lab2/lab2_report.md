University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://itmo.ru/ru/viewfaculty/19/fakultet_prikladnoy_informatiki.htm)

Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)

Year: 2025

Group: K3323

Author: Klopov Mikhail Pavlovich

Lab: Lab2

Date of create: A long time ago, in a galaxy far, far away...

Date of finished: 11.05.2025

# Отчет по лабораторной работе №2 "Развертывание дополнительного CHR, первый сценарий Ansible"

## Ход работы

У нас есть знакомая виртуалка с Ubuntu 20.04 на Селектеле. Но есть нюанс: к нашему IPsec/L2TP/IKEv1 VPN нельзя так просто взять и подключить более одного устройства за одним и тем же NATом. Как быть? WireGuard, OpenVPN и IKEv2 режутся, на SSTP в интернете почти нет информации (я попробовал поработать с тем, чем есть, но не получилось. Что интересно, помочь не могут даже ChatGPT и DeepSeek - написанные ими скрипты ссылаются на мёртвые репозитории). Остаётся только устаревший, полный уязвимостей PPTP. 

Что ещё интересно, когда я с ним игрался в городе, у меня внезапно начал глючить интернет (то скорость замедлена, то пропадает на время) и сменился выданный нам раньше, чем я себя помню, айпишник от провайдера. После таких экспериментов я решил отложить это дело до лучших времён.

Но вот наступили лучшие времена, а точнее майские праздники, и было решено возобновить работу на даче, с другим провайдером. И в итоге это дало результат.

Был взят скрипт https://github.com/saaiful/PPTP-VPN. После того, как он отработал, в файл /etc/ppp/chap-secrets был добавлен второй аккаунт для второго роутера. 

Теперь нужно создать две виртуалки. VirtualBox не позволяет одновременно смонтировать клоны одного диска, поэтому нужно рандомизировать образам UUID:

```
VBoxManage internalcommands sethduuid "/home/warmike01/Documents/chr-6.49.18_copy1.vdi"
VBoxManage internalcommands sethduuid "/home/warmike01/Documents/chr-6.49.18_copy2.vdi"
```

![Рандомизация](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2/Screenshot%20from%202025-05-11%2003-00-19.png)

После создания виртуалок ОЧЕНЬ ВАЖНО заменить NAT на Bridged Adapter в сетевых настройках (иначе микротики отказываются подключаться к VPN):

![Настройка сетевого адаптера](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2/Screenshot%20from%202025-05-11%2003-07-56.png)

Это значит, что микротики подключаются к нашей домашней сети. 

Что интересно, через панель управления роутера видно только один из них:

![Что интересно, через панель управления роутера видно только один из них.](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2/Screenshot%20from%202025-05-11%2003-21-28.png)

Но доступны оба.

![Но доступны оба.](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2/Screenshot%20from%202025-05-11%2003-22-31.png)

Подключимся к VPN с одного из них:

```
interface pptp-client add name=pptp-vpn connect-to=<ip> user=router1 password=<password> disabled=no
```
А другой, для разнообразия, подключим через WinBox:

![Подключение через VPN](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2/Screenshot%20from%202025-05-11%2003-28-39.png)

![Лог сервера](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab2/log.PNG)
