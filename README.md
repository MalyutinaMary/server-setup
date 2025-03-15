# Настройка серверов

![image](https://github.com/user-attachments/assets/e870bcb7-0fb3-40db-82a1-6fe7347a2a71)

## Эмулятор
* GNS3

## Оборудование
* Маршрутизатор Cisco 3745

## NTP

С помощью команды `sh clock detail` можно отобразить показания системных часов (system clock) и источник времени. По умолчанию системные часы синхронизируются с аппаратными (hardware clock). В рамках данного проекта мы работаем с образом, для которого не предусмотрена настройка аппаратных часов, в связи с чем при каждом новом запуске устройства системные часы получают устаревшие показания:

![image](https://github.com/user-attachments/assets/1a6fde21-edde-402c-8d6c-77f9affce70a)

Можно задать показания системных часов вручную (но они сбросятся при перезагрузке):
>`clock timezone <hours-offset>`
>
>`clock set HH:MM:SS MONTH DAY YEAR`

Также можно указать NTP-сервер в качестве источника времени (эта настройка сохранится при перезагрузке):
>(config) `ntp server <ip-address>`

Адреса реальных NTP-серверов Google можно найти с помощью команды `nslookup time.google.com`, но в этом проекте мы их не используем.

Будем считать, что системные часы R1 не уступают по точности атомным, и назначим их в качестве источника времени для R1:
> R1#(config) `ntp master`

Далее укажем R1 в качестве источника времени для R2: 
> R2#(config) `ntp server 10.10.10.1`

Проверим статус синхронизации:

![image](https://github.com/user-attachments/assets/55dba27e-8ea3-41c9-a8a5-d5fbf40c22f7)

## DNS

В рамках данного проекта не используются реальные доменные имена и адреса. Доменное имя my.site разрешается в адрес 10.10.11.1, присвоенный loopback-интерфейсу устройства R1. Команды для настройки loopback-интерфейса приведены ниже:

> R1#(config) `int Loopback0`
>
> R1#(config-if) `ip add 10.10.11.1 255.255.255.0`

Далее конфигурируем R1 как DNS-сервер:
> R1#(config) `ip dns server`

Создаем запись о соответствии доменного имени my.site адресу 10.10.11.1: 
> R1#(config) `ip host my.site 10.10.11.1`

Конфигурируем R2 как DNS-клиент, указывая адрес DNS-сервера:
> R2#(config) `ip name-server 10.10.10.1`

Разрешаем R2 выполнять DNS-запросы:
> R2#(config) `ip domain lookup`

Проверяем корректность разрешения доменного имени в адрес:

![image](https://github.com/user-attachments/assets/4480f846-83e9-4107-bce4-67e642e8ccc1)

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
