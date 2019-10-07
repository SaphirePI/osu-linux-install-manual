# Osu linux install manual
Коротко о том как загрузить РАБОЧУЮ осу, и играть БЕЗ ЗАДЕРЖКИ.

Briefly about how to load a WORKING osu!, and play WITHOUT DELAY.
=====
>Данный способ актуален для Ubuntu и производных дистрибутивов. Основан на статье на данной [статье](https://gist.github.com/Francesco149/a2f796683a4e5195458f4bb171d88eb0)

### Итак, приступим.
В первую очередь нам необходимо загрузить винишко третьей версии. Для этого добавим официальный репозиторий:

```sql
----------------- Для Ubuntu ----------------- 
sudo dpkg --add-architecture i386   
wget -nc https://dl.winehq.org/wine-builds/Release.key
sudo apt-key add Release.key
sudo apt-add-repository https://dl.winehq.org/wine-builds/ubuntu/
----------------- Для Linux Mint 17.x  ----------------- 
sudo apt-add-repository 'deb https://dl.winehq.org/wine-builds/ubuntu/ trusty main'
----------------- Для Linux Mint 18.x  ----------------- 
sudo apt-add-repository 'deb https://dl.winehq.org/wine-builds/ubuntu/ xenial main'
sudo apt-get update
sudo apt-get install --install-recommends winehq-stable
```

Устанавливаем скрипт winetricks. Он поможет с легкостью установить полезные компоненты Windows.

```sql
wget https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks
chmod +x winetricks
sudo mv -v winetricks /usr/local/bin
```

Создадим префикс и установим дополнения через winetricks, включающие в себя .NET, шрифты и прочие нужные штуки.

```ini
WINEPREFIX=~/osu-linux WINEARCH=win32 winetricks corefonts dotnet40 gdiplus d3dx9_36 cjkfonts
```

Префикс — это такая папка, куда будут скачиваться дополнения и, вообще, программы. Как виртуальный диск. В данном случае префиксом является ~/osu-linux. Это значит, что в Вашей домашней папке будет создана папка `osu-linux`. Вы можете выбрать и другую папку, если нужно. Если что-то пойдет не так, и вы захотите начать сначала, префикс лучше заменить. Папку при этом можно будет удалить.

#### Запустим конфигуратор:

```ini
WINEPREFIX=~/osu-linux WINEARCH=win32 winecfg
```

Там, на вкладке Графика, нужно проверить, чтобы стояли галочки на «Разрешить менеджеру окон декорировать окна» и «... управлять окнами Wine»

В целях уменьшения задержки будем использовать аудиодрайвер ALSA:

```ini
WINEPREFIX=~/osu-linux WINEARCH=win32 winetricks sound=alsa
```

Твик для еще большего уменьшения задержки аудио. Следующая команда создает файл редактора реестра и заполняет его параметрами. Копируйте целиком, а не построчно.

```cs
cat > dsound.reg << "EOF"
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Wine\DirectSound]
"HelBuflen"="512"
"SndQueueMax"="3"
EOF
```

Выполнить файл настроек реестра:

```ini
WINEPREFIX=~/osu-linux WINEARCH=win32 wine regedit dsound.reg
```

С этого момента мы отклонимся от пути, предлагаемого в англоязычной статье — у меня так и не получилось установить osu! с помощью установщика. По сему, будем использовать предустановленную. Единственная проблема, если у вас только Линукс, вы нигде ее не возьмете, поэтому, я сам установил ее на виртуальную машину и запаковал после установки. [Наслаждайтесь](https://drive.google.com/file/d/1L0oe8TWjMAM0JdDcYd28-zaLndcAPYj6/view). Если не доверяете, можете установить самостоятельно.

Распаковываем архив. Далее предполагается, что папка osu! у Вас в Домашней папке.

В принципе, уже можно посмотреть на результат:

```ini
WINEPREFIX=~/osu-linux WINEARCH=win32 wine ~/osu\!/osu\!.exe
```

Должно работать без лагов.

Создадим лаунчер, чтобы каждый раз не писать эту команду. В качестве иконки можно взять лого с официального сайта. Скачайте, переименуйте в icon.png и положите в папку с osu!. Далее создадим файл `osu.desktop` либо в `/usr/share/applications`, либо в `~/.local/share/applications`. В последнем случае лаунчер будет доступен только для текущего пользователя. Пропишем следующее содержимое в него:

```ini
[Desktop Entry]
Encoding=UTF-8
Version=1.0
Name=osu!
GenericName=osu!
Exec=WINEPREFIX=~/osu-linux WINEARCH=win32 wine ~/osu\!/osu\!.exe
Terminal=false
Icon=/home/iks/osu\!/icon.png            # путь к иконке лучше сделать абсолютным
Type=Application
Categories=Game;
Comment=click the circles!
Comment[ru_RU]=играть в кружочки
```

В пути к иконке поставьте свое имя пользователя, а в поле Exec поменяйте путь и префикс, если вы использовали другие данные. Теперь вы сможете найти игру в списке приложений.

В общем-то все, можно наслаждаться кружочками и линуксом одновременно. Более подробные инструкции, лайфхаки с планшетом и прочее есть в статье на гитхабе, если надо, могу перевести.


