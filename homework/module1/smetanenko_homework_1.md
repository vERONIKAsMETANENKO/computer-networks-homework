# Домашнє завдання 1. Основи комп'ютерних мереж

Про дані в цьому файлі. Тут немає моїх реальних особистих даних. Усі команди я запускала через VPN, тому публічна адреса нижче належить VPN. MAC-адреси я замінила на xx. Внутрішні адреси 172.x.x.x я залишила

## Завдання 1. Дослідження мережі

Команда:

```bash
ip a
```

Результат:

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host proto kernel_lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1420 qdisc mq state UP group default qlen 1000
    link/ether xx:xx:xx:xx:xx:xx brd ff:ff:ff:ff:ff:ff
    altname enxxxxxxxxxxxxx
    inet 172.20.112.250/20 brd 172.20.127.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::xxxx:xxxx:xxxx:xxxx/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether xx:xx:xx:xx:xx:xx brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
```

Інтерфейс: eth0

Тип підключення: Ethernet, віртуальний (WSL2)

MAC-адреса: xx:xx:xx:xx:xx:xx, рівень 2 OSI (канальний)

IP-адреса: 172.20.112.250, рівень 3 OSI (мережевий)

Маска: /20 (255.255.240.0), рівень 3 OSI (мережевий)

Gateway: 172.20.112.1

Broadcast: 172.20.127.255

Hosts: 2^(32-20) - 2 = 4094

## Завдання 2. Дослідження маршрутизації

Команда:

```bash
ip r
```

Результат:

```
default via 172.20.112.1 dev eth0 proto kernel onlink
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
172.20.112.0/20 dev eth0 proto kernel scope link src 172.20.112.250
```

Протокол: kernel. Адресу і маршрути налаштувало ядро під час запуску WSL2, а не DHCP

## Завдання 3. Знаходження публічної IP-адреси

Команда:

```bash
curl ifconfig.io
```

Результат:

```
185.107.56.78
```

Локальна адреса 172.20.112.250, публічна 185.107.56.78. Вони різні

Локальна адреса приватна і працює тільки всередині мережі. Роутер через NAT замінює її на свою публічну адресу, коли запит іде в інтернет. У мене публічна адреса належить VPN-серверу, бо трафік іде через VPN
