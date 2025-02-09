# Настройка серверов

![image](https://github.com/user-attachments/assets/e870bcb7-0fb3-40db-82a1-6fe7347a2a71)

## Оборудование
* Маршрутизатор Cisco 3745

## NTP

>`sh clock`
>
>`clock timezone <hours-offset>`
>
>`clock set HH:MM:SS MONTH DAY YEAR`
>
>`sh ntp status`
>
>`sh ntp associations`

Использовать внешний сервер в качестве источника:

>(config) `ntp source <ip-address>`

Использовать программные часы устройства в качестве источника:

>(config) `ntp master`

Настройка NTP-сервера:

>(config) `ntp server <ip-address>`

## DNS

Настройка DNS-сервера:

>(config) `int Loopback0`
>
>(config-if) `ip add <ip-address>`
>
>(config) `ip dns server`
>
>(config) `ip host <host-name> <ip-address>`

Настройка DNS-клиента:

>(config) `ip name-server <ip-address>`
>
>(config) `ip domain lookup`

## DHCP

Настройка DHCP-сервера:

>(config) `ip dhcp excluded-address <low-address> <high-address>`
>
>(config) `ip dhcp pool <POOL_NAME>`
>
>(dhcp-config) `network <subnet> <subnet-mask>`
>
>(dhcp-config) `dns-server <ip-address>`
>
>(dhcp-config) `default-router <ip-address>`
>
>(dhcp-config) `lease 0 5 30`

## SSH

Создание пользователя и пароля:

>(config) `aaa new-model`
>
>(config) `username admin`
>
>(config) `username admin secret <admin-password>`
>
>(config) `enable secret <privileged-password>`

Настройка SSH-сервера:

>(config) `hostname <new-hostname>`
>
>(config) `ip domain-name <new-domain-name>`
>
>(config) `crypto key generate rsa`
>
>(config) `line vty 0 4`
>
>(config-line) `transport input ssh`
>
>`sh ip ssh`
>
>(config) `ip ssh version 2`

Удаленное подключение по SSH:

>`ssh -l <username> <ip-address>`
