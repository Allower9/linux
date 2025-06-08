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
    SQUID в непрозрачном режиме
    - скачать nginx на altsrv3 + поднять  его (подробнее ... )
   - 4.1 настроил сетевые интерфейсы ( ipv4address , ipv4route , resolv.conf )
   - 4.2 на altsrv1 сделал $ sysctl net.ipv4.ip_forward=1       ---> net.ipv4.ip_forward = 1 + iptzbles -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
   - 4.3 скачал squid 
   - 4.4 настроил /etc/squid/squid.conf ![image](https://github.com/user-attachments/assets/7c155d12-eab1-44f5-9b7b-f485c07370d1)
    ,а именно закоментировал (http_access allow localnet) и добавил свои ip адресса локальной сети net 
  - ПРОВЕРКА  --->  открыть через altwks ссылку  
     ---------------------------------
     КЭШИРОВАНИЕ
      - в /etc/squid/squid.con ---> добавим настроку кэша ![image](https://github.com/user-attachments/assets/4252ef3f-d9a9-46ca-92d8-15b22b131953)
      -  в /etc/squid/squid.conf  ---> dns_nameservers 8.8.8.8 1.1.1.1
     
      - после "squid -z" ( делаем уже в остановленном squid ) 
     
      - для принятия трафика по прокси для рабочей станции через ... ---> ![image](https://github.com/user-attachments/assets/54531d21-64b5-414e-8b4d-ba51a60bde50) , а также через http можно настроить так ---> ![image](https://github.com/user-attachments/assets/f603c5b0-fa3a-431f-9315-3e10bb3053e2)

     
      -  найстройка для сервера без графики на прокси ---> ![image](https://github.com/user-attachments/assets/91dce768-1fdb-457e-809e-296d08745014)
      - ну и также добавление сети в /etc/squid/squid.conf ![image](https://github.com/user-attachments/assets/c26e7095-7cf3-4f1f-b3ba-93959a0ef004)    ---> добавили только 200 сеть


 - итог видим при обновление TCP_MEM_HIT ( при повторном использовании  apt-get update -d или на разных узлах )  ![image](https://github.com/user-attachments/assets/a70749ec-d2d0-469a-8284-9cbc1041b7aa)

---------------------------------
АВТОРИЗАЦИЯ


