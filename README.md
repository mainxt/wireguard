## Установка своего VPN-сервера WireGuard на VPS-хостинге
<img src="https://raw.githubusercontent.com/avenom/wireguard/main/Wireguard/wireguard-header1.png">

[WireGuard](https://www.wireguard.com/) – самый актуальный и быстрый VPN-протокол на сегодняшний день. Обеспечивает максимальную скорость работы и надежную криптографическую защиту.

1. [Хостинг и клиенты](#hosting)
2. [Подключение к терминалу сервера](#connect)
3. [Установка WireGuard](#install)
4. [Добавление новых устройств](#clients)
5. [Подключение к WireGuard VPN](#connectvpn)


## 1. Хостинг и клиенты <a name="hosting"></a>

VPS-хостинг желательно выбирать в [Европе](https://ru.hostings.info/filters/europe-hosting/vps-vds
), но сейчас оплатить его уже будет проблематично. Можно выбрать [российский хостинг](https://ru.hostings.info/filters/russia/vps-vds) с серверами в Европе. У меня хостинг от [FirstByte](https://firstbyte.ru/?from=87020) с сервером в Нидерландах. Если будете брать там, то буду признателен если перейдете по моей ссылке или воспользуетесь моим промокодом *refer_87020*.

Важно выбрать сервер с [KVM-виртуализацией](https://firstbyte.ru/vps-vds/kvm-ssd-eu/?from=87020). На этапе выбора ОС для сервера, выбираем Ubuntu 18.04, однако скрипт WireGuard Manager может развернуть и на других [дистрибутивах Linux](https://github.com/complexorganizations/wireguard-manager#-compatibility-with-linux-distro).

На поднятие сервера нужно время, после чего приходит письмо из которого нам нужно три пункта: IP-адрес сервера, имя пользователя (root), пароль.

Заранее установите необходимые вам клиенты WireGuard: [Windows](https://download.wireguard.com/windows-client/wireguard-installer.exe), [macOS](https://itunes.apple.com/us/app/wireguard/id1451685025?ls=1&mt=12), [Android](https://play.google.com/store/apps/details?id=com.wireguard.android), [iOS](https://itunes.apple.com/us/app/wireguard/id1441195209?ls=1&mt=8).

## 2. Подключение к терминалу сервера<a name="connect"></a>

Для удобства будем использовать кроссплатформенную программу [Hyper](https://hyper.is/#installation).

2.1. Подключаемся к серверу с вашим IP-адресом и именем пользователя следующей командой:

```
ssh root@IP-адрес
```

2.1. Далее пишем yes, затем вводим пароль от сервера.

2.1. После подключения к серверу проверяемм ОС на наличие обновлений, выполнив следующую команду:

```
apt-get update && apt-get upgrade
```

## 3. Установка WireGuard <a name="install"></a>

1. Сначала установим инструмент curl:

```
apt install curl -y
```

2. Затем запустим скачивание скрипта установки:

```
curl -O https://raw.githubusercontent.com/angristan/wireguard-install/master/wireguard-install.sh
chmod +x wireguard-install.sh
./wireguard-install.sh
```

3. Появится строка ./wireguard-install.sh, жмем Enter.

4. Отвечаем на все вопросы утвердительно, когда спросит имя клиента пишем wireguard, о окончанию установки будет отображен QR-код (рекомендую сохранить) 

Далее определимся с клиентом WireGuard:
1. Для смартфонов открываем мобильный клиент WireGuard, выбираем «**Сканировать QR-код**» и пробуем подключиться к VPN.
2. Для компьютеров открываем конфигурационные файл командой:

```
nano /root/wg0-client-wireguard.conf
```

Затем копируем весь текст в любой блокнот и сохраняем файл с именем wg0-client-wireguard.conf

## 4. Добавление других клиентов<a name="clients"></a>

Для каждого отдельного клиента нужно генерировать отдельный конфигурационный файл.

Для этого мы повторно запускаем скрипт установки с помощью команды:

```
bash /root/wireguard-install.sh
```

Выбираем 1-й пункт «**Add a new user**», далее пишем к примеру wireguard-win (нк более 15 символов. Появится QR-код, но нам теперь нужен не он, а путь к конфигурационному файлу под ним. Открываем содержимое файла следующей командой:

```
nano /root/wg0-client-wireguard-win.conf
```

Затем копируем весь текст в любой блокнот и сохраняем файл с именем wireguard-windows-wg0.conf

## 5. Подключение к WireGuard VPN<a name="connectvpn"></a>

Подключение с мобильного устройства самое простое. После сканирования QR-кода, создается соединение к вашему серверу и вам остается только к нему подключиться.

Для клиента WireGuard для Windows, нажимаем «**Добавить туннель**» и выбираем скопированный ранее конфигурационный файл wireguard-windows-wg0.conf и пробуем подключиться.

Заходим [сюда](https://whatismyipaddress.com/) и видим IP-адрес и страну вашего сервера.

Проверяем доступ к [заблокированным](https://twitter.com/) сайтам и замеряем скорость на [Speedcheck](https://www.speedcheck.org/ru/).
