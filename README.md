# Домашнее задание к занятию "`Кластеризация и балансировка нагрузки`" - `Кузьмина Варвара`


---

## Задание 1. Балансировка Round-robin на 4 уровне с помощью HAProxy

### 1. Запуск двух простых Python-серверов

Созданы два каталога с разным содержимым `index.html`:

- `~/server1/index.html` → содержимое «Server 1 Port 8888»
- `~/server2/index.html` → содержимое «Server 2 Port 9999»

Команды:

```bash
mkdir -p ~/server1 ~/server2
echo "Server 1 Port 8888" > ~/server1/index.html
echo "Server 2 Port 9999" > ~/server2/index.html
```

Запуск серверов на разных портах (8888 и 9999):

```bash
cd ~/server1 && python3 -m http.server 8888
cd ~/server2 && python3 -m http.server 9999
```

Проверка, что оба сервера отвечают:

```bash
curl http://127.0.0.1:8888
curl http://127.0.0.1:9999
```

![Проверка серверов напрямую](images/servers_direct.png)

---

### 2. Установка и настройка HAProxy

Установка:

```bash
sudo apt update
sudo apt install haproxy -y
```

### Конфигурационный файл `/etc/haproxy/haproxy.cfg`

```haproxy
global
    log /dev/log local0
    log /dev/log local1 notice
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660 level admin
    stats timeout 30s
    user haproxy
    group haproxy
    daemon

defaults
    log global
    mode tcp
    option tcplog
    option dontlognull
    timeout connect 5000
    timeout client  50000
    timeout server  50000

listen web_tcp
    bind :1325
    mode tcp
    balance roundrobin
    server s1 127.0.0.1:8888 check inter 3s
    server s2 127.0.0.1:9999 check inter 3s
```

Проверка конфига и перезапуск:

```bash
sudo haproxy -f /etc/haproxy/haproxy.cfg -c
sudo systemctl restart haproxy
sudo systemctl status haproxy
```

![Конфигурация HAProxy](images/haproxy_config.png)

---

### 3. Проверка балансировки Round-robin

Серия запросов через HAProxy (порт 1325):

```bash
for i in {1..6}; do curl -s http://127.0.0.1:1325; echo; done
```

Результат — ответы чередуются между двумя серверами:

```
Server 1 Port 8888
Server 2 Port 9999
Server 1 Port 8888
Server 2 Port 9999
Server 1 Port 8888
Server 2 Port 9999
```

![Round-robin через HAProxy](images/roundrobin.png)

---

### Вывод

Настроена балансировка нагрузки на транспортном уровне (L4, TCP) с алгоритмом **Round-robin**. HAProxy слушает порт `1325` и поочерёдно перенаправляет TCP-соединения на два Python-сервера: `127.0.0.1:8888` и `127.0.0.1:9999`. Проверка через `curl` подтверждает равномерное распределение запросов между бэкендами.

---

### Задание 2

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота 2](ссылка на скриншот 2)`


