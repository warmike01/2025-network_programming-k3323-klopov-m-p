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

Теперь нужно по [инструкции](https://github.com/netbox-community/netbox-docker) установить Netbox и добавить в него виртуалки, а также командой *ansible-galaxy collection install netbox.netbox* установить модуль Netbox для Ansible.

Всё настроили, теперь можно командой *ansible-playbook collection_playbook.yaml* собирать данные. Они пришли в файл devices.json.

Теперь добавим устройствам в веб-интерфейсе Netbox желаемые IP-адреса. Для этого нужно сначала добавить им интерфейс:

