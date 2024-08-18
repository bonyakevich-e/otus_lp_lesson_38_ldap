### OTUS Linux Professional Lesson #38 | Subject: LDAP. Централизованная авторизация и аутентификация

#### ЦЕЛЬ: Научиться настраивать LDAP-сервер и подключать к нему LDAP-клиентов

#### Описание домашнего задания:
1) Установить FreeIPA
2) Написать Ansible-playbook для конфигурации клиента

Дополнительное задание:
3) *Настроить аутентификацию по SSH-ключам

4) **Firewall должен быть включен на сервере и на клиенте

### Инструкция по выполнению домашнего задания:

Создаем 3 виртуальных машины с ОС CentOS 8 Stream:
```
# vagrant up
```
__Настройка FreeIPA-сервер _ipa.otus.lan_.__
Установим часовой пояс: timedatectl set-timezone Europe/Moscow:
```
[root@ipa ~]# timedatectl set-timezone Europe/Moscow
```
Установим утилиту chrony:
```
[root@ipa ~]# yum install -y chrony
[root@ipa ~]# systemctl enable chronyd --now
```
Выключаем Firewall:
```
[root@ipa ~]# systemctl stop firewalld
[root@ipa ~]# systemctl disable firewalld
```
Остановим Selinux:
```
[root@ipa ~]# setenforce 0
```
Поменяем в файле /etc/selinux/config, параметр Selinux на disabled.

Для дальнейшей настройки FreeIPA нам потребуется, чтобы DNS-сервер хранил запись о нашем LDAP-сервере. В рамках данной лабораторной работы мы не будем настраивать отдельный DNS-сервер и просто добавим запись в файл /etc/hosts:
```
192.168.57.10 ipa.otus.lan ipa
```
Установим модуль DL1:
```
[root@ipa ~]# yum install -y @idm:DL1
```
Установим FreeIPA-сервер:
```
[root@ipa ~]# yum install -y ipa-server
```
Запустим скрипт установки:
```
[root@ipa ~]# ipa-server-install
```
Далее, нам потребуется указать параметры нашего LDAP-сервера, после ввода каждого параметра нажимаем Enter, если нас устраивает параметр, указанный в квадратных скобках, то можно сразу нажимать Enter:
```
Do you want to configure integrated DNS (BIND)? [no]: no
Server host name [ipa.otus.lan]: <Нажимаем Enter>
Please confirm the domain name [otus.lan]: <Нажимем Enter>
Please provide a realm name [OTUS.LAN]: <Нажимаем Enter>
Directory Manager password: <Указываем пароль минимум 8 символов>
Password (confirm): <Дублируем указанный пароль>
IPA admin password: <Указываем пароль минимум 8 символов>
Password (confirm): <Дублируем указанный пароль>
NetBIOS domain name [OTUS]: <Нажимаем Enter>
Do you want to configure chrony with NTP server or pool address? [no]: no
The IPA Master Server will be configured with:
Hostname:       ipa.otus.lan
IP address(es): 192.168.57.10
Domain name:    otus.lan
Realm name:     OTUS.LAN

The CA will be configured with:
Subject DN:   CN=Certificate Authority,O=OTUS.LAN
Subject base: O=OTUS.LAN
Chaining:     self-signed
Проверяем параметры, если всё устраивает, то нажимаем yes
Continue to configure the system with these values? [no]: yes
```

Далее начнется процесс установки. Процесс установки занимает примерно 10-15 минут (иногда время может быть другим). Если мастер успешно выполнит настройку FreeIPA то в конце мы получим сообщение: 
The ipa-server-install command was successful
