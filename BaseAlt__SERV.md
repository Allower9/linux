 Сетевые службы и администрирование
## Стенд 
![image](https://github.com/user-attachments/assets/e214f24b-d49e-4364-9427-3b12b45615a7)

# изначально настройка на altsrv1 ( главный серв.)

1. Настройки параметров сети
- cd /etc/net/ifaces
- cp -r enp0s3 enp0s8
- sed -i '/BOOTPROTO/s/dhcp/static/' enp0s8/options
- sed -i '/SYSTEMD_BOOTPROTO/s/dhcp4/static4/' enp0s8/options
- echo "192.168.100.1/24" > enp0s8/ipv4address
- ifdown enp0s8; ifup enp0s8
- echo "/sbin/iptables -t nat -A POSTROUTING -s 192.168.100.0/24 -j MASQUERADE" > enp0s8/ifup-post
- chmod +x enp0s8/ifup-post
- ifdown enp0s3; ifup enp0s3
- sed -i '/net\.ipv4\.ip_forward/s/0/1/' /etc/net/sysctl.conf
- systemctl restart network
2. Проверка применения настроек
 - ip -br a
 - ip r
. . .
iptables -t nat -L
. . .
sysctl net.ipv4.ip_forward
 - net.ipv4.ip_forward=1

Параметры стека протоколов интерфейса enp0s3
◦ IP/MASK: 10.0.2.15/24
• Параметры стека протоколов интерфейса enp0s8
◦ IP/MASK: 192.168.100.1/24
• Параметры маршрутизации
◦ Def. GW: 10.0.2.2
3. Проверка работы внешнего подключения
 - ping ya.ru
. . .
4. Установка имени узла
 - hostnamectl set-hostname altsrv1 

# настройка altsrv2  ( для второстеппенных серверов  без графики)
Настройки ОС altsrv2
1. Настройки параметров сетевого интерфейса
# cd /etc/net/ifaces
# sed -i '/BOOTPROTO/s/dhcp/static/' enp0s3/options
# echo "192.168.100.2/24" > enp0s3/ipv4address
# echo "default via 192.168.100.1" > enp0s3/ipv4route
# echo "nameserver 8.8.8.8" > enp0s3/resolv.conf
# ifdown enp0s3; ifup enp0s3
2. Проверка применения настроек
# ip -br a
. . .
# ip r
. . .
# cat /etc/resolv.conf
. . .
• Параметры стека протоколов интерфейса enp0s3
◦ IP/MASK: 192.168.100.2/24
• Параметры маршрутизации
◦ Def. GW: 192.168.100.1
• Параметры разрешения имен
◦ DNS SRV: 8.8.8.8
3. Проверка работы внешнего подключения
$ ping ya.ru
4. Установка имени узла
# hostnamectl set-hostname altsrv2
#### настройка на altsrv2

# Настройки ОС altwks1
1. Настройки параметров сетевого интерфейса
# cd /etc/net/ifaces
# sed -i '/BOOTPROTO/s/dhcp/static/' enp0s3/options
# echo "192.168.100.201/24" > enp0s3/ipv4address
# echo "default via 192.168.100.1" > enp0s3/ipv4route
# echo "nameserver 8.8.8.8" > enp0s3/resolv.conf
# systemctl restart NetworkManager
2. Проверка применения настроек
# ip -br a
. . .
# ip r
. . .
# cat /etc/resolv.conf
. . .
• Параметры стека протоколов интерфейса enp0s3
◦ IP/MASK: 192.168.100.201/24
◦ Def. GW: 192.168.100.1
• Параметры разрешения имен
◦ DNS SRV: 8.8.8.8
3. Проверка работы внешнего подключения
$ ping ya.ru
. . .
4. Разрешение имен локальных узлов
# cat > /etc/hosts << EOF
192.168.100.201 altwks1 altwks1.courses.alt
192.168.100.1 altsrv1 altsrv1.courses.alt
192.168.100.2 altsrv2 altsrv2.courses.alt
EOF
5. Проверка доступности внутренних узлов по имени
$ ping altsrv1
. . .
$ ping altsrv2
. . .
6. Генерация и обмен пользовательскими ключами SSH
• Выполняется из под УЗ sysadmin
$ ssh-keygen
$ ssh-copy-id sysadmin@altsrv1
. . .
$ ssh-copy-id sysadmin@altsrv2
. . .
7. Обеспечение SSH-доступа под суперпользователем на узлы стенда
• Производится путем подключения к обоим узлам и копирования .ssh/authorized_keys в
каталог суперпользователя
$ ssh sysadmin@altsrv1
[sysadmin@altsrv1 ~]$ su -
Password:
[root@altsrv1 ~]# cat /home/sysadmin/.ssh/authorized_keys >> .ssh/authorized_keys
[root@altsrv1 ~]# exit
[sysadmin@altsrv1 ~]$ exit
$
$ ssh sysadmin@altsrv2
[sysadmin@altsrv2 ~]$ su -
Password:
[root@altsrv2 ~]# cat /home/sysadmin/.ssh/authorized_keys >> .ssh/authorized_keys
[root@altsrv2 ~]# exit
[sysadmin@altsrv2 ~]$ exit
$
8. Проверка SSH-доступа
• Должен производиться безпаролный доступ под УЗ суперпользователя
$ ssh root@altsrv1
[root@altsrv1 ~]# exit
$ ssh root@altsrv2
[root@altsrv2 ~]# exit
$
9. Установка имени узла
# hostnamectl set-hostname altwks1
• Выполните перезагрузку узла
------


## Тема 1. Служба динамического конфигурирования узлов (DHCP)
1.0. Инструкция по настройке стенда  
1.1. Протокол DHCP  
1.2. Установка и базовая настройка DHCP-сервера в ОС Альт  
1.3. Просмотр арендованных адресов, настройки резервирования  
1.4. Работа с DHCP-сервером средствами ЦУС  
🔹 **Лабораторная работа №1**: Настройка DHCP сервера в ОС Альт  


## настройка dhcp ( небольшие отличия от стенда, я поменял altsrv1 с altwks местами )
 ###( до начала настройки установить на сервер ``` apt-get install dhcp-server ```)
 ### 1) меняем настройки интерфейса  ( в basealt /etc/net/ifaces/enp0s?/options ,а также удалим файлы ipv4address,ipv4route,resolv.conf
 
![telegram-cloud-photo-size-2-5404715252932803334-x](https://github.com/user-attachments/assets/e609e58d-5451-41a8-81c4-49c20f5f9675)
### 2) настроим /etc/dhcp/dhcpd.conf
![telegram-cloud-photo-size-2-5404719028209054781-y](https://github.com/user-attachments/assets/3ed06403-6660-488e-b2eb-451f4979f735)
### 3) теперь сделаем статический ip адрес через dhcp. Из файла см фото ниже там есть путь --> берём мак адреса
![telegram-cloud-photo-size-2-5404715252932803454-y](https://github.com/user-attachments/assets/7666228e-9144-4754-9d9c-af13722bda5c)
### 4) и прописываем вот так
![telegram-cloud-photo-size-2-5404715252932803465-y](https://github.com/user-attachments/assets/0947f236-92de-4b75-9312-16f850d1b240)
### 5) после restart dhcpd на сервере и на узле ifdown и ifup ( опустим и поднимем интерфейс )
![telegram-cloud-photo-size-2-5404715252932803688-y](https://github.com/user-attachments/assets/feed475a-96bb-4708-998e-8229e5c9dab0)
### 6) итог dhcp настроен, 1 узел настроен на статику через dhcp другой динамически через dhcp 


------
## Тема 2. Служба разрешения имен узлов (DNS)
2.0. Инструкция по настройке стенда  
2.1. DNS - Domain Name System  
2.2. Процесс разрешения DNS-имени  
2.3. Форматы ресурсных записей DNS  
2.4. Зоны DNS  
2.5. Установка ISC BIND в ОС Альт  
2.6. Настройка кэширующего сервера, настройки форвардинга  
2.7. Настройка зоны на DNS-сервере  
2.8. Использование утилит диагностики  
2.9. Зонные трансферы. Настройка вторичного DNS-сервера  
2.10. Зоны обратного просмотра. Создание поддоменов, делегирование  
📌 Дополнительно: Настройка Динамического DNS (DDNS)  
🔹 **Лабораторная работа №2**: Настройка DNS-сервера в ОС Альт  
# DNS ( кэширующий  + разрешает локальные имена )
----
### 1) Установим компоненты  `apt-get install bind` and `apt-get install bind-utils`
2) ### unit(службу) сразу можно запустить systemctl enable --now bind.service
  
3) ![telegram-cloud-photo-size-2-5404715252932803689-y](https://github.com/user-attachments/assets/59690ae9-f333-47b1-aa7a-caa5fbed15c5)
  ###  видим что все работает, но только пока для самого сервера, (нужно скачать утилиту host) чтобы также проверить
4)# в /etc/bind/options.conf 
`listen-on { 127.0.0.1; 192.168.100.1; };
  и ниже раскоментровал 
  allow-recursion {192.168.100.0/24; };`

###  здесь мы видим что мы обращаемся к днс 192.168.100.1  допустим ( listen-on) 
### а здесь кому можно обратиться к dns (allow-recursion)
  
### так же в этом же файлы закоментировал ipv6  под listen-on и указал в /etc/sysconfig/bind    "-4" см фото ниже
![telegram-cloud-photo-size-2-5404715252932803912-y](https://github.com/user-attachments/assets/1c88039e-811d-468e-90f4-80d4247ab512)
5) ![telegram-cloud-photo-size-2-5404715252932803907-y](https://github.com/user-attachments/assets/9944753b-a937-42af-a723-de417cb4efdd)
 ### здесь уже мы запускаем через узел и также видим что dns работает 
6) в dhcp можно поменять параметр "resolv.conf" -- чтобы не писать наш сервер (192.168.100.1)
7) также настроим зону ... см фото ниже
<img width="769" alt="Снимок экрана 2025-03-03 в 20 49 51 (3)" src="https://github.com/user-attachments/assets/744de130-9e71-4b02-9751-0cdb7d32e0d3" />
<img width="960" alt="Снимок экрана 2025-03-03 в 20 49 42 (1) (3)" src="https://github.com/user-attachments/assets/b5c616f3-ea5d-4da6-8d9e-079a71cfecde" />
<img width="959" alt="Снимок экрана 2025-03-03 в 20 49 07 (3)" src="https://github.com/user-attachments/assets/e45145f1-5e0b-4cd7-83d1-df2cc90acf71" />
![5348187745241134192 (1) (3)](https://github.com/user-attachments/assets/f9240986-4334-4a45-9ce3-a990f146bd31)
![5348112540363779630 (1) (3)](https://github.com/user-attachments/assets/e0daef41-3b2d-494b-b569-c1e2068ed9d0)



9) Итог получили  DNS ( кэширующий  + разрешает локальные имена )



-----
## Тема 3. Службы сетевого доступа к файлам (NFS, SMB)
3.0. Инструкция по настройке стенда  
3.1. Network File System (NFS)  
3.2. Работа служб NFS на сервере. Разграничение доступа  
3.3. Экспорт разделяемых ресурсов  
3.4. Монтирование NFS-ресурсов  
3.5. NFS версии 4  
3.6. SMB/CIFS. Установка CIFS-сервера в ОС Альт  
3.7. Samba в режиме Standalone-сервера  
3.8. Разграничение доступа к SMB-ресурсам  
3.9. Монтирование/автомонтирование SMB-ресурсов  
🔹 **Лабораторная работа №3**: Сетевой доступ по протоколу SMB  

## Тема 4. Домены ActiveDirectory
4.0. Подготовка стенда  
4.1. Вводная информация  
4.2. Подготовка к развертыванию контроллера домена AD  
4.3. Установка контроллера нового домена  
4.4. Проверка работоспособности служб домена  
4.5. Добавление узла в домен  
4.6. Управление пользователями-группами в домене  
4.7. Механизм ролей  
4.8. Обеспечение отказоустойчивости домена  
4.9. Использование ADMC для управления AD  
4.10. Корректировки стенда  
🔹 **Лабораторная работа №4**: Active Directory  
