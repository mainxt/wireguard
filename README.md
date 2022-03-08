## Установка своего VPN-сервера WireGuard на VPS-хостинге
<img src="https://raw.githubusercontent.com/avenom/wireguard/main/Wireguard/wireguard-header1.png">

[WireGuard](https://www.wireguard.com/) – самый актуальный и быстрый VPN-протокол на сегодняшний день. Обеспечивает максимальную скорость работы и надежную криптографическую защиту.

1. [Хостинг и клиенты](#hosting)
2. [Подключение к серверу](#connect)
3. [Установка WireGuard Manager](#install)
4. [Добавление новых устройств](#clients)
5. [Подключение к WireGuard VPN](#connectvpn)


## 1. Хостинг и клиенты <a name="hosting"></a>

VPS-хостинг желательно выбирать в [Европе](https://ru.hostings.info/filters/europe-hosting/vps-vds
), но сейчас оплатить его уже будет проблематично. Можно выбрать [российский хостинг](https://ru.hostings.info/filters/russia/vps-vds) с серверами в Европе. У меня хостинг от [FirstByte](https://firstbyte.ru/?from=87020) с сервером в Нидерландах. Если будете брать там, то буду признателен если перейдете по моей ссылке или воспользуетесь моим промокодом *refer_87020*.

Важно выбрать сервер с [KVM-виртуализацией](https://firstbyte.ru/vps-vds/kvm-ssd-eu/?from=87020), так как WireGuard Manager нельзя установить на сервер с OpenVZ-виртуализацией. На этапе выбора ОС для сервера, выбираем Ubuntu 18.04, однако скрипт WireGuard Manager может развернуть и на других [дистрибутивах Linux](https://github.com/complexorganizations/wireguard-manager#-compatibility-with-linux-distro).

На поднятие сервера нужно время, после чего приходит письмо из которого нам нужно три пункта: IP-адрес сервера, имя пользователя (root), пароль.

Заранее установите необходимые вам [клиенты WireGuard](https://www.wireguard.com/install/).

## 2. Подключение к серверу<a name="connect"></a>

Если у вас российский хостинг, то взаимодействие с сервером обычно происходит через запуск VNC-клиента в панели управления сервером, но я на винде предпочитаю использовать программу [PuTTY](https://www.putty.org/), в которой удобно вставлять текст из буфера обмена правой кнопкой мыши. Запускаем VNC-клиент (для PuTTY нужно вводить IP-адрес сервера), после подключения вводим логин (root) и пароль от сервера.

После подключения к серверу проверяемм ОС на наличие обновлений, выполнив следующую команду:

```
apt-get update && apt-get upgrade
```

## 3. Установка WireGuard Manager<a name="install"></a>

1. Сначала установим инструмент curl:

```
apt install curl -y
```

2. Затем запустим скачивание скрипта установки:

```
curl https://raw.githubusercontent.com/complexorganizations/wireguard-manager/main/wireguard-manager.sh --create-dirs -o /usr/local/bin/wireguard-manager.sh
```

3. Делаем скрипт исполняемым:

```
chmod +x /usr/local/bin/wireguard-manager.sh
```

4. Запускаем установку:

```
bash /usr/local/bin/wireguard-manager.sh
```

Установка будет разделена на этапы, выбираем везде Recommended, но останавливаемся на следующих этапах:

- «**Would you like to install unbound**», выбираем «**Custom (Advanced)**».
- «**Let the users choose their custom dns provider**», выбираем «**Cloudflare (Recommended)**».
- «**What would you like to name your first WireGuard peer**», удаляем длинную строку и задаем название клиентскому файлу конфигурации в виде одного слова wireguard.

После чего начнется установка WireGuard, которая займет не больше 5 минут.

5. По окончанию установки будет отображен QR-код (рекомендую сохранить) для подключения с мобильного клиента WireGuard. Выбираем «**Сканировать QR-код**» и пробуем подключиться к VPN.

На этом установка WireGuard для доступа с мобильного устройства завершена.

## 4. Добавление новых устройств<a name="clients"></a>

Для каждого отдельного устройства нужно генерировать отдельный конфигурационный файл.

Например для клиента WireGuard для Windows нужно будет создать еще один конфигурационный файл. Для этого мы запускаем скрипт WireGuard Manager с помощью уже знакомой команды:

```
bash /usr/local/bin/wireguard-manager.sh
```

Выбираем 5-й пункт «**Add WireGuard Peer (client)**», удаляем длинную строку и пишем wireguard-windows. Появится QR-код, но нам теперь нужен не он, а путь к конфигурационному файлу под ним. Открываем содержимое файла следующей командой:

```
nano /etc/wireguard/clients/wireguard-windows-wg0.conf
```

Затем копируем весь текст в любой блокнот и сохраняем файл с именем wireguard-windows-wg0.conf

На винде можно установить программу [WinSCP](https://winscp.net/eng/download.php), затем подключиться к вашему серверу по SFTP и получить доступ в виде двухпанельного проводника, потом нужно перейти к этому конфигурационному файлу и скопировать его к себе на ПК.

## 5. Подключение к WireGuard VPN<a name="connectvpn"></a>

Подключение с мобильного устройства самое простое. После сканирования QR-кода, создается соединение к вашему серверу и вам остается только к нему подключиться.

Для клиента WireGuard для Windows, нажимаем «**Добавить туннель**» и выбираем скопированный ранее конфигурационный файл wireguard-windows-wg0.conf и пробуем подключиться.

Заходим [сюда](https://whatismyipaddress.com/) и видим IP-адрес и страну вашего сервера.

Проверяем доступ к [заблокированным](https://twitter.com/) сайтам и замеряем скорость на [Speedcheck](https://www.speedcheck.org/ru/).
