# linux_BaseAlt


стенд : 

![image](https://github.com/user-attachments/assets/b1826329-bf73-4e94-b786-0a72045be8de)















1. Настройка FastCGI-приложений с nginx ( apache2 ) 

2.  Настройка пакетной фильтрации средствами iptables ( nftables )
  ![2025-05-30 23 51 31 (1)](https://github.com/user-attachments/assets/ee11b8e7-a922-4827-9e55-a3c183c408a9)

3.  Shorewall ( Межсетевой экран )
   тут немного изменённый стенд 
- altsrv1 - такая же настройка , единственное только интерфейсы местами изменены

- 192.168.100.100 - это altsrv2 из net1 ( здесь у меня apache2)

- 192.168.100.99 - это altwks2 из net1
- 192.168.200.123 - это altsrv3 из DMZ
- 10.0.0.2 altsrv4 - ничего не менял --> из inet
- 10.0.0.102 altwks - ничего не менял -->из inet

поэтому на скриншотах , я просто через curl проверяю apache и после уже утилитой ping работоспособность сборки shorewall


   
![5327764699007284056 (1)](https://github.com/user-attachments/assets/c02aa9fa-f9a1-4c1f-a50b-54448f0ed86c)
![5327764699007284055 (1)](https://github.com/user-attachments/assets/5d68d005-ccd8-44fd-bae3-90c095bb5e2d)



4 Прокси-сервер SQUID
    стенд уже базовый 
   - 4.1 настроил сетевые интерфейсы ( ipv4address , ipv4route , resolv.conf )
   - 4.2 на altsrv1 сделал $ sysctl net.ipv4.ip_forward=1       ---> net.ipv4.ip_forward = 1 + iptzbles -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
   - 4.3 скачал squid 
   - 4.4 настроил /etc/squid/squid.conf ![image](https://github.com/user-attachments/assets/7c155d12-eab1-44f5-9b7b-f485c07370d1)
    ,а именно закоментировал (http_access allow localnet) и добавил свои ip адресса локальной сети net 

 
