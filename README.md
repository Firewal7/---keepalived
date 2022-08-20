# -Отказоустойчивость - keepalived
Установим keepalived: apt install keepalived

# Интерфейс на Вирт машинех 
Сеть: 1) Сетевой мост Wifi 2) Внутреняя сеть 

# /etc/network/interfaces
The loopback network interface
auto lo
iface lo inet loopback

 The primary network interface
auto enp0s3
iface enp0s3 inet dhcp

 Виртуальный адаптер хоста
auto enp0s8
iface enp0s8 inet static
address 192.168.1.1
netmask 255.255.255.0

# Установка должна проводиться на двух системах (нодах).
# Файл конфигурации находится: /etc/keepalived/keepalived.conf

# Первая
vrrp_instance failover_test {
state MASTER
interface enp0s8
virtual_router_id 10
priority 150
advert_int 4
authentication {
auth_type AH
auth_pass 1111
}
unicast_peer {
192.168.1.1/24
}
virtual_ipaddress {
192.168.1.3/24 dev enp0s8 label enp0s8:vip
}
}

# Вторая

vrrp_instance failover_test {
state BACKUP
interface enp0s8
virtual_router_id 10
priority 100
advert_int 4
authentication {
auth_type AH
auth_pass 1111
}
unicast_peer {
192.168.1.2/24
}
virtual_ipaddress {
192.168.1.3/24 dev enp0s8 label enp0s8:vip   
}
}

# Запуск службы: systemctl start keepalived
# Добавление службы в автозагрузку: systemctl enable keepalived
# Проверка работоспособности сервиса: systemctl status keepalived

# Отключить интерфейс ip link set dev enp0s8 down
# Включить ip link set dev enp0s8 up

