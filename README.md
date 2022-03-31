# RedOS заметки

## Установка сертифицированной RedOS

Кому не лень, запросите образ сертифицированной RedOS 7.3 у производителя, тогда обновление до 7.3 можно пропустить  
  
Закинул образ сертифицированной версии 7.2 (образ создавал я) [сюда](https://disk.yandex.ru/d/GTulgkvV7m_qMA), если мне доверия нет, можешь использовать свой купленный диск   

Создание загрузочной флешки [тут](https://redos.red-soft.ru/base/manual/red-os-installation/iso-to-usb-cd-dvd/). Для винды рекомендую [rufus](https://github.com/pbatard/rufus/releases/download/v3.17/rufus-3.17.exe)  

- В ВЫБОРЕ ПРОГРАММ выбери MATE (промахнёшься - ничего страшного)
- При установке пользователю легкого пароля жми __Готово__ два раза (только не делай такого пользователя админом)  
- Если хочешь двойную загрузку _Windows Linux_, создай отдельный раздел формата __ExFAT__ для обмена между системами (про двойную загрузку, если нужно, отдельно напишу)  
- Минимальное место на диске для RedOS 7.2 - 20Gb  

## Обновление до версии 7.3 certified
На сайте редоса есть подробная [статья](https://redos.red-soft.ru/base/update/update-to-7-3/) по обновлению с 7.2 на 7.3  

Дальше опишу, что делал я (чистая сертифицированая система 7.2 сразу после установки):  
`su root`  
`yum update -y`  
`yum remove anaconda-core firstboot yumex python34-libs ipa-common python2-policycoreutils python2-caja caja-schemas mariadb-server pcs libreport python-ntplib libvncserver bind-libs-lite -y`  
`yum autoremove -y`  
`sed -i 's!7.2!7.3!g' /etc/yum.repos.d/*.repo && yum clean all && yum makecache`  

Переходим в консольный режим (в графическом будут ошибки - проверено):  
Жмём __Ctrl+Alt+F2__, логин _root_, пароль (при наборе не виден), набираем `systemctl stop gdm`, опять жмём __Ctrl+Alt+F2__  
`yum update kernel-lt systemd yum grub2 redos-release -x wine* -y`  
`dnf update -y`  
`dnf install libyui-mga-gtk -y`  
`dnf groupinstall mate-desktop -y`  
`reboot`  

Готово! Можно полюбоваться версией: `lsb_release -a` (Ещё загрузочная менюшка красивше, чем просто в 7.3)  


## CryptoPro
Скачай дистрибутив с сайта [КриптоПро](https://www.cryptopro.ru/products/csp/downloads) (КриптоПро CSP 5.0 для Linux (x64, rpm))  
или [отсюда](https://disk.yandex.ru/d/pAfPZadoqroDPA) (опять же - моё, обновлю по запросу)  
Для удобства сделай софтлинк на папку Загрузки (переключение раскладки это ужас):  
`ln -s Загрузки/ ./Downloads`  
`cd Downloads` (не забывай про Tab)  
`tar -xzf linux-amd64.tgz`  
`cd linux-amd64/`  
`su root`  
`./install_gui.sh` Далее - Далее - Установить  
Ввести лицензию свою лицензию или: _50500-00000-0Z000-00M22-0GF2D_ Выход  
В менюшке Инструменты КриптоПро - проверь лицензию  

### Установка VeraCrypt

КриптоПро видит контейнеры HDD только в определённом каталоге  
Флешки и токены видны без танцев с бубном  

Каталог, где КриптоПро видит контейнеры: /var/opt/cprocs/keys/имя_пользователя  

#### Я вместо флэшек использую зашифрованный файловый контейнер __VeraCrypt__  

Скачивай [VeraCrypt](https://veracrypt.fr/en/Downloads.html) (RPM packages: CentOS 8/Fedora 34: GUI)  
Устанавливай, запускай, дальше по шагам:  

- менюшка __Тома__ - __Создать новый том__
- __Создать зашифрованный файловый контейнер__ (можно просто Далее нажать)
- __Обычный том__ (Далее)  
- кнопка __Выбрать файл__ - сохранить куда-нибудь под каким-нибудь именем - __Далее__
- __Далее__
- размер тома - __32Мб__ (или меньше - чем больше том, тем он дольше расшифровывается)
- __задай суперсложный пароль!!!__
- файловая система __FAT__
- __двигай__ энергично мышкой)) полоску до конца можно не доводить - __Разметить__
- __Выход__
- __Слот 1__ - Выбрать файл - __Открыть__ - __Смонтировать__ 
- Ввести суперсложный пароль и нажать кнопку __Опции!__
- __Смонтировать в каталог__ - __Выбрать__ - __Другие места__ - _Компьютер_ - ___/var/opt/cprocs/keys/имя_пользователя___ - __Открыть__ - __ОК__ (ввести пароль админа)
-  менюшка __Избранное__ - __Добавить смонтированный том в список избранных томов__ - __ОК_

Дальше в зависимости от ситуации   
- если есть __токен__ с контейнерами, стандартными средствами криптопро копируй их на созданный диск - назначение будет HDIMAGE.....(надо проверить - токенами на линуксе не пользовался)  
- если есть __флешка__ с контейнерами, просто скопируй все контейнеры (каталоги) в каталог _/var/opt/cprocs/keys/имя_пользователя_

Для установки сертификата заходи:  
- Инструменты КриптоПро - Контейнеры - Установить сертификат

В дальнейшем для использования этого зашифрованного контейнера с контейнерами)) в __VeraCrypt__ щёлкай __Избранное__ - свой том (внизу)  

В линуксовой версии VeraCrypt нет опции авторазмонтирования по тайм-ауту (или я не нашёл). Размонтируй вручную!  

Если закинуть зашифрованый файловый контейнер на яндекс диск (к примеру), можно использовать его и в других системах на других компьютерах. Не забудь задать суперсложный пароль при создании файлового контейнера!  

Можно создать пару зашифрованных контейнеров - один для неактуальных ЭЦП, второй для актуальных  
VeraCrypt есть для всех ОС - рекомендую пользоваться ей и под виндой (там можно поставить авторазмонтирование - вообще красота)   

### Установка Chromium-Gost
Для работы с ЭЦП рекомендую использовать браузер chromium-gost и больше ни для чего его не юзать!  
Он часто обновляется, так что почаще жми ссылку ниже для обновления  

Скачай и установи [посдедний chromium-gost](https://github.com/deemru/Chromium-Gost/releases/latest/) (rpm)  

#### Настройка Chromium-Gost
Открой в chromium-gost:  
[Контур.Веб-диск](https://install.kontur.ru/uc)  

## Установка Р7-офис
Скачать актуальную версию Р7-офис для RedOS (и других ОС) можно по [ссылке](https://r7-office.ru/downloads#1_4)  

У кого нет лицензий на Р7-офис могут смело ставить [OnlyOffice](https://www.onlyoffice.com/ru/download-desktop.aspx) - это та же херня, только бесплатно (для RedOS выбрать версию CentOS и RHEL). Лицензию всё-таки купите или запросите

__Установка шрифта по умолчанию самостоятельно:__  
скопируй шрифты в каталог ~/.fonts (для текущего пользователя) или /usr/share/fonts (для всех пользователей)  
открой от имени админа файлы /opt/r7-office/desktopeditors/converter/empty/new.\*, меняй шрифт на нужный (PTAstra) и сохраняй 

__Установка шрифта по умолчанию скриптом:__  
качай [архив](https://disk.yandex.ru/d/Yh3G9KNkTfs5jA), распакуй и под рутом выполни ./install.sh  

__Установка шрифта по умолчанию в Windows:__  
Файлы находятся в папке C:\\Program Files\\R7-Office\\Editors\\converter\\empty  

__Плагины:__  
[плагин](https://disk.yandex.ru/d/W9ztR5q_FoXWrg) для подсчёта символов и слов в выделеном фрагменте  


## Pidgin - чат вместо spark
Из коробки pidgin не хочет подключаться к нашему серверу, пришлось компилировать его из исходников:  

`su root`  
Дальше жми кнопку копировать и вставляй в терминал  

    dnf remove -y pidgin
    dnf groupinstall -y "Development tools"
    dnf install -y pidgin-devel glib2-devel gtk2-devel gstreamer-devel gnutls-devel cyrus-sasl-devel libcurl-devel libpurple-devel libSM-devel libXScrnSaver-devel
    cd ~
    wget https://sourceforge.net/projects/pidgin/files/Pidgin/2.14.8/pidgin-2.14.8.tar.bz2 -O pidgin-source.tar.bz2
    mkdir pidgin-source
    tar -xvf pidgin-source.tar.bz2 -C pidgin-source --strip-components=1
    cd pidgin-source
    ./configure --enable-gnutls=yes --disable-gtkspell --disable-gevolution --disable-vv --disable-idn --disable-meanwhile --disable-avahi --disable-dbus --disable-tcl
    make -j5
    make install
    cd ~
    rm -rf ~/pidgin-source
    dnf remove -y pidgin-devel
    
__Добавить учетную запись__
Протокол: XMPP  
Имя пользователя:  
Домен: 10.13.62.1  
Пароль:  
Запомнить пароль  
Принять сертификат  

__Настройки:__  
Средства - Настройки - Звуки - снять первые две галки Собеседник входит/выходит  
Средства - Модули - Автоматический приём - Настроить модуль - Путь для сохранения файлов (/home/имя_пользователя/Документы), снять галочку Избегайте имён файлов.  
Дальше необходимо на каждом пользователе, от которого хочешь принимать файлы автоматически, щёлкнуть правой клавишей - Автоматический прием файлов - Принять автоматически  
Средства - Модули - История  
Средства - Модули - Кнопка отправки  

## Установка и настройка удалённого доступа x11vnc
`su root`

Устанавливаем пакет x11vnc:  
`dnf install x11vnc -y`  

Сохраняем пароль для доступа (пароль придумываем посложнее):  
`x11vnc -storepasswd "ПРИДУМАЙ_ПАРОЛЬ_И_ВСТАВЬ_СЮДА" /etc/vncpasswd`  

Настраиваем автоматический запуск:  
  
    echo '[Unit]
    Description=X11vnc server for GDM
    After=display-manager.service
    [Service]
    ExecStart=/usr/bin/x11vnc -many -shared -display :0 -auth guess -noxdamage -rfbauth /etc/vncpasswd
    Restart=on-failure
    RestartSec=3
    [Install]
    WantedBy=graphical.target' > /lib/systemd/system/x11vnc.service
    systemctl daemon-reload
    systemctl enable --now x11vnc.service
    
  
Проверим запуск службы:  
`systemctl status x11vnc.service`  

