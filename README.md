## Установка своего VPN-сервера WireGuard на VPS-хостинг
<img src="https://raw.githubusercontent.com/avenom/wireguard/main/Wireguard/wireguard-header1.png">

[WireGuard](https://www.wireguard.com/) – самый актуальный и быстрый VPN-протокол на сегодняшний день. Обеспечивает максимальную скорость работы и надежную криптографическую защиту.

1. [VPS-хостинг и клиенты](#hosting)
2. [Подключение к серверу](#connect)
3. [Установка сервера WireGuard](#install)
4. [Добавление других клиентов](#clients)
5. [Подключение к WireGuard](#connectvpn)

## 1. VPS-хостинг и клиенты <a name="hosting"></a>

VPS-хостинг желательно выбирать в Европе, но сейчас оплатить его уже будет проблематично. Можно подобрать хостинг на сайтах [VPS.today](https://vps.today/) и [Hostings.info](https://ru.hostings.info/) в России с серверами в Европе, главное выбрать сервер с KVM-виртуализацией и наличием канала минимум 100 Мбит/с с безлимитным трафиком или с его приличным объемом. У меня хостинг от [FirstByte](https://firstbyte.ru/?from=87020) с сервером в Нидерландах. Если будете брать там, то буду признателен, если перейдете по моей ссылке или воспользуетесь моим промокодом *refer_87020*.

На этапе выбора ОС для сервера выбираем Ubuntu 18.04. На поднятие сервера нужно время, после чего приходит письмо из которого нам нужно три пункта: IP-адрес сервера, имя пользователя (root), пароль.

Заранее установите необходимые вам клиенты WireGuard: [Windows](https://download.wireguard.com/windows-client/wireguard-installer.exe), [macOS](https://itunes.apple.com/us/app/wireguard/id1451685025?ls=1&mt=12), [Android](https://play.google.com/store/apps/details?id=com.wireguard.android), [iOS](https://itunes.apple.com/us/app/wireguard/id1441195209?ls=1&mt=8).

## 2. Подключение к серверу<a name="connect"></a>

Для удобства будем использовать кроссплатформенный SSH-клиент [Hyper](https://hyper.is/#installation).

1. Подключаемся к серверу с вашим IP-адресом и именем пользователя следующей командой:

```
ssh root@IP
```

2. Пишем yes, затем вводим пароль от вашего сервера (не отображается при вводе, но можно вставить из буфера обмена):

```
пароль
```

3. После подключения к серверу проверяем ОС на наличие обновлений:

```
apt-get update && apt-get upgrade
```

## 3. Установка сервера WireGuard <a name="install"></a>

Существует автоматический способ установки сервера при помощи клиента [AmneziaVPN](https://ru.amnezia.org/) о котором я писал в своем [телеграм-канале](https://t.me/avennom/23), но в этом гайде мы будем использовать популярный скрипт WireGuard installer от angristan.

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

<img src="https://raw.githubusercontent.com/avenom/wireguard/main/Wireguard/1.png">

4. Отвечаем на все вопросы утвердительно, попросит нажать любую клавишу для начала установки, затем попросит ввести имя клиента (Client name) пишем **wireguard**, соглашаемся с предложенными IP-адресами.

<img src="https://raw.githubusercontent.com/avenom/wireguard/main/Wireguard/2.png">

После чего будет отображен QR-код.

<img src="https://raw.githubusercontent.com/avenom/wireguard/main/Wireguard/3.png">

Далее определимся с клиентом WireGuard:
1. Для смартфонов открываем мобильный клиент WireGuard, выбираем «**Сканировать QR-код**» и пробуем подключиться к VPN.

<img src="https://raw.githubusercontent.com/avenom/wireguard/main/Wireguard/iphone.gif">

2. Для компьютеров открываем конфигурационный файл командой:

```
nano ./wg0-client-wireguard.conf
```

Затем копируем весь текст в любой текстовой редактор и сохраняем файл с именем **wg0-client-wireguard.conf**.

## 4. Добавление других клиентов<a name="clients"></a>

Для каждого отдельного клиента нужно генерировать отдельный конфигурационный файл.

1. Для этого мы повторно запускаем скрипт установки:

```
bash ./wireguard-install.sh
```

<img src="https://raw.githubusercontent.com/avenom/wireguard/main/Wireguard/4.png">

2. Выбираем 1-й пункт «**Add a new user**», далее пишем имя например для клиента в Windows **wireguard-win** (не более 15 символов), соглашаемся с предложенными IP-адресами. Появится QR-код, но нам теперь нужен не он, а путь к конфигурационному файлу под ним. Открываем содержимое файла следующей командой:

```
nano ./wg0-client-wireguard-win.conf
```

Затем копируем весь текст в любой текстовой редактор и сохраняем файл с именем **wg0-client-wireguard-win.conf**.

## 5. Подключение к WireGuard<a name="connectvpn"></a>

По умолчанию прописывается DNS-сервер от AdGuard, с ним тоже все работает и открывается. При желании можно отредактировать соединение в клиенте и вручную прописать DNS-сервер 1.1.1.1 от Cloudflare, который не стал блокировать доступ из России.

Подключение с мобильного устройства самое простое. После сканирования QR-кода, создается соединение к вашему серверу и вам остается только к нему подключиться.

Для клиента WireGuard для Windows, нажимаем «**Добавить туннель**» и выбираем скопированный ранее конфигурационный файл wg0-client-wireguard-win.conf и пробуем подключиться.

Заходим [сюда](https://whatismyipaddress.com/) и видим IP-адрес и страну вашего сервера.

Проверяем доступ к заблокированным [сайтам](https://twitter.com/) и замеряем [скорость](https://www.speedcheck.org/ru/).
