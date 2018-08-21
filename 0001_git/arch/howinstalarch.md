## install arch!
###### [sourse](https://ziggi.org/ustanovka-i-nastroyka-arch-linux-xfce-chast-1/) and adds :+1:
## go
[dowload](https://www.archlinux.org/download/.) cd image, write cd or usb

[create boot cd or usb](https://github.com/TBA-projects/0001_git/blob/master/0001_git/arch/createbootcdorusb.md)
###### img
![a1 ](https://github.com/TBA-projects/0001_git/blob/master/0001_git/arch/img/a1.png)
###### Установим русскую раскладку:
```javascript
# loadkeys ru
```
###### Изменим консольный шрифт на тот, который поддерживает кириллицу:
```javascript
# setfont cyr-sun16
```
###### Добавим русскую локаль в систему установки:
###### В файле /etc/locale.gen раскомментируйте (уберите # вначале) строку #ru_RU.UTF-8 UTF-8
```javascript
# nano /etc/locale.gen
```
###### (закрыть файл: Ctrl + X)
###### Обновим текущую локаль системы:
```javascript
# locale-gen
```
```javascript
# export LANG=ru_RU.UTF-8
```
## Настройка сети
###### Сейчас при установке дистрибутива наличие сети обязательно, поэтому её настройка необходима.
###### Проверить подключение к сети можно так:
```javascript
ping -c 3 google.com
```

![a2](https://github.com/TBA-projects/0001_git/blob/master/0001_git/arch/img/a2.jpg)
## Создание разделов на жестком диске
Для управления разделами на жестком диске в процессе установки рекомендую использовать программу cfdisk.
Рекомендую создать следующие разделы:



    512 Мб с флагом Загрузочный(Boot) — для загрузчика (для MBR).
    Раздел, на 1 Гб больше, чем объем оперативной памяти — для раздела подкачки(swap), если он вам нужен.
    15 Гб (15360 Мб) для корневого раздела системы.
    И всё оставшееся для домашнего раздела.

Жмём кнопку Запись.

В итоге должно получиться что-то вроде этого:

![a3](https://github.com/TBA-projects/0001_git/blob/master/0001_git/arch/img/a3.jpg)

## !!!Форматирование разделов
Для загрузочного раздела будем использовать файловую систему ext2. Также мы будем использовать метки для удобства.
```javascript
# mkfs.ext2 /dev/sda1 -L boot
```
Для раздела подкачки (swap):
```javascript
# mkswap /dev/sda2 -L swap
```
Для корневого раздела используем ext4:
```javascript
# mkfs.ext4 /dev/sda3 -L root
```
Для домашнего раздела также используем ext4:
```javascript
# mkfs.ext4 /dev/sda4 -L home
```

![a4](https://github.com/TBA-projects/0001_git/blob/master/0001_git/arch/img/a4.jpg)

## Монтирование разделов
Смонтируем корневой раздел:
```javascript
# mount /dev/sda3 /mnt
```
Создадим каталоги для монтирования boot и home разделов:
```javascript
# mkdir /mnt/{boot,home}
```
Смонтируем загрузочный раздел:
```javascript
# mount /dev/sda1 /mnt/boot
```
Смонтируем домашний раздел:
```javascript
# mount /dev/sda4 /mnt/home
```
Подключим раздел подкачки(swap)
```javascript
# swapon /dev/sda2
```
## Выбор зеркал для pacman
#### Список по скорости
Самое быстрое зеркало можно определить при помощи скрипта /usr/bin/rankmirrors.
Сохраните текущий файл /etc/pacman.d/mirrorlist:
```javascript
 # cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup
```
```javascript
# sed -i 's/^#Server/Server/' /etc/pacman.d/mirrorlist.backup
```
```javascript
# rankmirrors -n 6 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist
```
## Установка пакетов
Установим базовые пакеты системы. В этот список вы можете добавить какие-нибудь нужные вам пакеты для установки.
```javascript
# pacstrap -i /mnt base base-devel grub
```
После выполнения команды и выбора всех пакетов, скачаются(около 150 Мб) и установятся(около 130) последние версии необходимых пакетов.

Важно: если вы ведёте установку по Wi-Fi, то вам необходимо установить пакеты netctl, dialog и wpa_supplicant:
```javascript
# pacstrap -i /mnt netctl dialog wpa_supplicant
```
Важно: если вы ведёте установку используя pppoe, то вам необходимо установить пакет rp-pppoe:
```javascript
# pacstrap -i /mnt rp-pppoe
```
## Установка пакета GRUB в устанавливаемую систему
```javascript
# arch-chroot /mnt pacman -S grub
```
Если вы используете EFI, то установите efibootmgr:
```javascript
# arch-chroot /mnt pacman -S efibootmgr
```
Если вы устанавливаете 32-х битную систему, то используйте пакет grub-efi-i386.
## Первичная настройка системы
Сгенерируем fstab, для этого используем следующую команду:
```javascript
# genfstab -p /mnt >> /mnt/etc/fstab
```
Перейдём в установленную систему:
```javascript
# arch-chroot /mnt /bin/bash
```
обавим русскую локаль в систему:

В файле /etc/locale.gen раскомментируйте(уберите # вначале) строку #en_US.UTF-8 UTF-8 и строку #ru_RU.UTF-8 UTF-8
```javascript
# nano /etc/locale.gen
```
(закрыть файл: Ctrl + X)
Обновим текущую локаль системы:
```javascript
# locale-gen
```
Добавим русскую локаль в консоль:
Откройте файл /etc/mkinitcpio.conf:
```javascript
# nano /etc/mkinitcpio.conf
```
В разделе HOOKS, должен быть прописан хук keymap.
В разделе MODULES нужно прописать свой драйвер видеокарты: i915 для Intel, radeon для AMD, nouveau для Nvidia. Пример, как это может выглядеть: https://pastebin.com/xknvDX33
(закрыть файл: Ctrl + X)

Создадим загрузочный RAM диск:
```javascript
# mkinitcpio -p linux
```
Установим загрузчик (для BIOS):
```javascript
# grub-install /dev/sda
```
Установим загрузчик (для EFI):
```javascript
# grub-install --target=x86_64-efi --efi-directory=/boot/EFI --bootloader-id=grub
```
Обновим grub.cfg:
```javascript
# grub-mkconfig -o /boot/grub/grub.cfg
```
Внимание: если при создании grub.cfg у вас были ошибки, то попробуйте добавить GRUB_DISABLE_SUBMENU=y в /etc/default/grub.

Установим root пароль:
```javascript
# passwd
```
Выйдем из установленной системы:
```javascript
# exit
```
Отмонтируем ранее монтируемые разделы:
```javascript
# umount /mnt/{boot,home,}
```
Сейчас следует перезагрузить систему.
```javascript
# reboot
```
## Грузимся в установленую систему
И нужно зайти в root пользователя с помощью ранее введённого пароля.

Изменим имя компьютера(замените myhostname на своё):
```javascript
# hostnamectl set-hostname myhostname
```
Установим временную зону:
```javascript
# timedatectl set-timezone Europe/Moscow
```
Локализуем систему:
```javascript
# localectl set-keymap ru
```
```javascript
# setfont cyr-sun16
```
```javascript
# localectl set-locale LANG="ru_RU.UTF-8"
```
```javascript
# export LANG=ru_RU.UTF-8
```

Добавим строку FONT=cyr-sun16 в /etc/vconsole.conf.
```javascript
nano /etc/vconsole.conf
```
Обновим загрузочный RAM диск:
```javascript
# mkinitcpio -p linux
```
Обновим grub.cfg(для локализации):
```javascript
# grub-mkconfig -o /boot/grub/grub.cfg
```
Настроим pacman (только для x86_64):
```javascript
# nano /etc/pacman.conf
```
Для работы 32-битных приложений в 64-битной системе нужно раскомментировать (удалить # вначале) репозиторий multilib:
```javascript
  #[multilib]
  #Include = /etc/pacman.d/mirrorlist
```
Внимание: если возникли какие-то проблемы при загрузке пакетов, попробуйте создать файл /etc/sysctl.d/40-ipv6.conf и записать в него это: net.ipv6.conf.all.disable_ipv6 = 1

Добавим пользователя (замените myusername на своё) и сразу поместим его в нужные группы:
```javascript
# useradd -m -g users -G audio,games,lp,optical,power,scanner,storage,video,wheel -s /bin/bash myusername
```
Установим ему пароль:
```javascript
# passwd myusername
```
Изменим ему информацию GECOS:
```javascript
# chfn myusername
```
## Настройка системы
###### Настройка сети

Для проводной сети
```javascript
# systemctl enable dhcpcd
```
```javascript
# systemctl start dhcpcd
```
Для беспроводной сети
```javascript
# wifi-menu
```
Обновим базы данных пакетов:
```javascript
# pacman -Syy
```
Обновим все пакеты:
```javascript
# pacman -Su
```
## Поставим и настроим sudo
```javascript
# pacman -S sudo
```
В файле /etc/sudoers раскомментируем строку # %wheel ALL=(ALL) ALL

Теперь мы можем использовать sudo для выполнения команд администратора.

Теперь выйдите из root пользователя с помощью команды exit и зайдите в пользователя, которого вы создали.
Графическое окружение

## Установим X:
```javascript
sudo pacman -S xorg-server xorg-xinit xorg-apps mesa-libgl xterm
```
(если вы используете тачпад или тачскрин, то установите xf86-input-synaptics)

Для x86_64 необходимо установить пакет lib32-mesa-libgl:
```javascript
sudo pacman -S lib32-mesa-libgl
```
Установим драйвер для видеокарты:

Intel:
```javascript
sudo pacman -S xf86-video-intel
```
Nvidia:
```javascript
sudo pacman -S xf86-video-nouveau
```
AMD:
```javascript
sudo pacman -S xf86-video-ati
```
Если вы устанавливаете систему на виртуальную машину:
```javascript
sudo pacman -S xf86-video-vesa
```
## Xfce + SDDM

Приступим к установке и настройке графического окружения Xfce с менеджером входа SDDM:
```javascript
sudo pacman -S xfce4 xfce4-goodies sddm
```
Добавим sddm в демоны:
```javascript
sudo systemctl enable sddm.service
```
Шрифты

Рекомендую установить следующие шрифты:
```javascript
sudo pacman -S ttf-ubuntu-font-family
```
Конец

Теперь можно перезагрузить систему командой
```javascript
sudo systemctl reboot
```
