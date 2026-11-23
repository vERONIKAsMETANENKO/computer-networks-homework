# Домашнє завдання 2. Транспортний рівень та служби мережі

Про дані в цьому файлі. Тут немає моїх реальних особистих даних. Усі команди я запускала через VPN. Адреси 127.x.x.x і 172.x.x.x внутрішні, ззовні їх не видно

## Завдання 1. Аналіз відкритих портів

Команда:

```bash
ss -tuln
```

Результат:

```
Netid State  Recv-Q Send-Q Local Address:Port  Peer Address:Port
udp   UNCONN 0      0         127.0.0.54:53         0.0.0.0:*
udp   UNCONN 0      0      127.0.0.53%lo:53         0.0.0.0:*
udp   UNCONN 0      0          127.0.0.1:323        0.0.0.0:*
udp   UNCONN 0      0          127.0.0.1:323        0.0.0.0:*
udp   UNCONN 0      0              [::1]:323           [::]:*
udp   UNCONN 0      0              [::1]:323           [::]:*
tcp   LISTEN 0      4096         0.0.0.0:22         0.0.0.0:*
tcp   LISTEN 0      4096       127.0.0.1:39073      0.0.0.0:*
tcp   LISTEN 0      4096   127.0.0.53%lo:53         0.0.0.0:*
tcp   LISTEN 0      4096      127.0.0.54:53         0.0.0.0:*
tcp   LISTEN 0      4096            [::]:22            [::]:*
```

TCP порт: 22, стан LISTEN -> SSH

TCP порт: 53, стан LISTEN -> локальний DNS

UDP порт: 53, стан UNCONN -> локальний DNS

UDP порт: 323, стан UNCONN -> синхронізація часу

TCP порт: 39073, стан LISTEN -> containerd (Docker)

## Завдання 2. Аналіз процесів, що слухають порти

Команда:

```bash
sudo ss -tulpn
```

Результат:

```
Netid State  Recv-Q Send-Q Local Address:Port  Peer Address:Port Process
udp   UNCONN 0      0         127.0.0.54:53         0.0.0.0:*    users:(("systemd-resolve",pid=81,fd=18))
udp   UNCONN 0      0      127.0.0.53%lo:53         0.0.0.0:*    users:(("systemd-resolve",pid=81,fd=16))
udp   UNCONN 0      0          127.0.0.1:323        0.0.0.0:*    users:(("chronyd",pid=259,fd=4))
udp   UNCONN 0      0          127.0.0.1:323        0.0.0.0:*
udp   UNCONN 0      0              [::1]:323           [::]:*
udp   UNCONN 0      0              [::1]:323           [::]:*    users:(("chronyd",pid=259,fd=5))
tcp   LISTEN 0      4096         0.0.0.0:22         0.0.0.0:*    users:(("systemd",pid=1,fd=79))
tcp   LISTEN 0      4096       127.0.0.1:39073      0.0.0.0:*    users:(("containerd",pid=256,fd=15))
tcp   LISTEN 0      4096   127.0.0.53%lo:53         0.0.0.0:*    users:(("systemd-resolve",pid=81,fd=17))
tcp   LISTEN 0      4096      127.0.0.54:53         0.0.0.0:*    users:(("systemd-resolve",pid=81,fd=19))
tcp   LISTEN 0      4096            [::]:22            [::]:*    users:(("systemd",pid=1,fd=81))
```

Процес: systemd-resolve
PID: 81
Порт: 53

Процес: chronyd
PID: 259
Порт: 323

Процес: containerd
PID: 256
Порт: 39073

## Завдання 3. Перевірка TCP-з'єднання

Команда:

```bash
nc -vz google.com 80
```

Результат:

```
Connection to google.com (142.251.142.206) 80 port [tcp/http] succeeded!
```

Тип з'єднання: TCP

Порт: 80

Результат: з'єднання встановлено

## Завдання 4. Порівняння TCP vs UDP

Команда:

```bash
nc -vz 8.8.8.8 53
```

Результат:

```
Connection to 8.8.8.8 53 port [tcp/domain] succeeded!
```

Команда:

```bash
nc -u -vz 8.8.8.8 53
```

Результат:

```
Connection to 8.8.8.8 53 port [udp/domain] succeeded!
```

TCP: сервер підтвердив з'єднання (handshake), тому succeeded означає, що порт справді відкритий

UDP: з'єднання немає, succeeded означає лише, що пакет відправлено, відповідь не факт

Різниця: TCP встановлює сесію, UDP ні

## Завдання 5. DNS резолвінг

Команда:

```bash
nslookup github.com
```

Результат:

```
;; Got recursion not available from 172.20.112.1
Server:		172.20.112.1
Address:	172.20.112.1#53

Non-authoritative answer:
Name:	github.com
Address: 140.82.121.3
```

Команда:

```bash
dig github.com
```

Результат:

```
; <<>> DiG 9.20.24-1ubuntu0.5-Ubuntu <<>> github.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 3556
;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
;; WARNING: recursion requested but not available

;; QUESTION SECTION:
;github.com.			IN	A

;; ANSWER SECTION:
github.com.		0	IN	A	140.82.121.3

;; Query time: 4 msec
;; SERVER: 172.20.112.1#53(172.20.112.1) (UDP)
;; MSG SIZE  rcvd: 54
```

Домен: github.com

IP: 140.82.121.3

DNS сервер: 172.20.112.1 (шлюз WSL2)
