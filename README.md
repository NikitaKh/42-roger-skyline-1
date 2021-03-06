# roger-skyline-1 #

Задание (проект) roger-skyline-1 (42 association / 21 school).

## A. VM Part ##

* Если вы делаете задание дома: Загружаем Oracle VirtualBox 6.x -- *[по ссылке](https://www.virtualbox.org/wiki/Downloads)*.
* Если вы делаете задание дома: Устанавливаем Oracle VirtualBox 6.x v нашу операционную систему.
* Звгружаем ISO-образ установочного CD-диска Debian GNU/Linux. Текущая (последняя) версия -- **debian 10.0.0  amd64 NetInst** -- *[по ссылкеe](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/)*.
* Запускаем Oracle VirtualBox и [следуем инструкциям](00-VM/README.md). 

## B. Network and Security Part

1. Создаём пользователя ([скрипт A](01-network-and-security/01a-add-user.sh), затем [скрипт B](01-network-and-security/01b-add-user.sh)).
2. Включаем для него sudo ([скрипт A](01-network-and-security/02a-sudo.sh), затем [скрипт B](01-network-and-security/02b-sudo.sh))
3. Настроить сетевой интрефейс -- убрать dhcp и установить сетевую маску ([скрпит](01-network-and-security/03-make-DHCP-of_and-set-mask.sh))
4. Сделать   SSH-доступ с RSA-ключами, перенастроить SSH-порты сервера и запретить логин root через SSH ([скрипт для сервера](01-network-and-security/04a-set-SSH-port-and-lock-root-login.sh) и [скрипт для клиента](01-network-and-security/04b-set-SSH-RSA-security-keys.sh) )
5. Настроить правила брандмауэра на своем сервере только с сервисами, используемыми вне виртуальной машины. 

   Обычно, если первоначальная установка виртуальной машины производилать как рекомендовано выше, все и так настроено правильно. Но можно дополнительно усилить правила обслуживания TCP-портов с помощью пакета `iptables` и [скрипта](01-network-and-security/05-rules-of-firewall.sh) (и почитайте [как проверять текущие соединения по портам и прослушиваемые порты](01-network-and-security/05-rules-of-firewall.md)).
   
   Проверить что все закрыто после исполнения скрипта можно с помощью `ping` на нашу виртуальную машину. Она не будет отвечать на ping-запросы (но, на самом деле, это не всегда хорошо).
   
6. Установить защиту DoS (атака отказа в обслуживании) на открытые порты вашей виртуальной машины.

   DoS и DDoS-атака -- это агрессивное внешнее воздействие на сервер, проводимое с целью перегрузить его запросами, доведения его до отказа или брутфорс-взлома (brute force -- грубая сила, например, подбор паролей перебором и т.п.).

   Если атака проводится с одиночного компьютера -- ее называют DoS (Denial of Service), если с нескольких, распределенных в сети, компьютеров — DDoS (Distributed Denial of Service).

   Защиту от DoS может обеспечить пакет `fail2ban`. Для его установки и инастройки используйте [скрипт](01-network-and-security/06-DOS-protection.sh).
   
7. Защита от сканирования.
 
    Настройки iptables и route управляют блокировками по портами, но не предупреждает о UDP- и stealth-сканирований, которые обычно предшествуют реальным атакам.
    
    Для определения и реакции на сканирования портов предназначен пакет `PortSentry` (ранее назависимый разработч, а сейчас Cisco). PortSentry в реальном времени обнаруживает сканирование и отработать по одному из следующих сценариев:
    
    * Заносит информацию об инциденте в системный log-журнал `/var/log/syslog` -- сценарий по умолчанию.
    * Сканировующий хост заносится в файл `/etc/host.deny` для TCP-обёрток (wrappers).
    * Компьютер настраивается и перенаправляет весь трафик атакующего на несуществующий компьютер.
    * Компьютер настраивается на блокировку атакующих пакетов с помощью фильтра.

    [Используемый скрипт](01-network-and-security/07-scans-protection.sh) работает по последнему сцернарию. Для большего понимания работы `PortSentry` см. [конфигурационный файл с русскоязычными комментариями](01-network-and-security/portsentry.conf).   