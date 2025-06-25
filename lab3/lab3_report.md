University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://itmo.ru/ru/viewfaculty/19/fakultet_prikladnoy_informatiki.htm)

Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)

Year: 2025

Group: K3323

Author: Klopov Mikhail Pavlovich

Lab: Lab3

Date of create: 24.06.2025

Date of finished: 25.06.2025

# Отчет по лабораторной работе №3 "Развертывание Netbox, сеть связи как источник правды в системе технического учета Netbox"

В этот раз создадим два CHR на компьютере с Linux. VirtualBox не позволяет одновременно смонтировать клоны одного диска, поэтому нужно рандомизировать образам UUID:

```
VBoxManage internalcommands sethduuid "/home/warmike01/Documents/chr-6.49.18_copy3.vdi"
VBoxManage internalcommands sethduuid "/home/warmike01/Documents/chr-6.49.18_copy4.vdi"
```
Также нужно настроить виртуалкам сетевой мост вместо NAT в качестве адаптера, чтобы у них был полноценный IP-адрес в локальной сети.

Теперь нужно по [инструкции](https://github.com/netbox-community/netbox-docker) установить Netbox, добавить в него виртуалки и выдать себе API token, а также командой *ansible-galaxy collection install netbox.netbox* установить модуль Netbox для Ansible.

Всё настроили, теперь можно командой *ansible-playbook collection_playbook.yaml* собирать данные. Они пришли в файл devices.json.

Теперь добавим устройствам в веб-интерфейсе Netbox желаемые IP-адреса. Для этого нужно сначала добавить им интерфейс:

![Интерфейс](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab3/Screenshot%20from%202025-06-25%2001-39-36.png)

Затем добавить IP-адрес:

![Адрес](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab3/Screenshot%20from%202025-06-25%2001-43-03.png)

![Результат](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab3/Screenshot%20from%202025-06-25%2001-44-06.png)

Сначала нужно с сервера зайти на роутеры по SSH, чтобы они добавились в known_hosts:

![Проверка SSH](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab3/Screenshot%20from%202025-06-25%2002-20-34.png)

После этого можно проверить связь с роутерами командой *ansible all -m ping -i inventory.yaml*:

![Проверка ping](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab3/Screenshot%20from%202025-06-25%2002-25-02.png)

Осталось только исполнить плейбук командой *ansible-playbook configuration_playbook.yaml -i inventory.yaml*:

![Исполнение](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab3/Screenshot%20from%202025-06-25%2003-05-29.png)

Выданные IP работают, по ним можно зайти по SSH:

![Исполнение](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab3/Screenshot%20from%202025-06-25%2002-49-22.png)

Последняя задача - собрать с виртуалок серийные номера и записать их в Netbox. Исполняем *ansible-playbook updateinfo_playbook.yaml -i inventory.yaml*:

![Вроде всё прошло...](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab3/Screenshot%20from%202025-06-25%2003-30-01.png)

Вроде всё прошло. Проверим результат:

![Так, стоп...](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab3/Screenshot%20from%202025-06-25%2003-40-35.png)

Так, стоп... Давайте разбираться. Добавим задачу вывода серийного номера на экран:

![Пустые строки](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab3/Screenshot%20from%202025-06-25%2003-37-40.png)

Откуда-то взялись пустые строки. Ну ладно, отсчитаем номер строки с текстом и возьмём именно её:

![Правильные номера](https://raw.githubusercontent.com/warmike01/2025-network_programming-k3323-klopov-m-p/refs/heads/master/lab3/Screenshot%20from%202025-06-25%2003-45-02.png)

Так-то лучше!
