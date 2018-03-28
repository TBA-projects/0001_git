# как создать загрузочный USB
    открываем браузер , пешем в строке поиска "как создать загрузочный USB",  ищем
    подходящее руководство , делаем как написано в руководстве и вуаля у вас есть
    загрузочная флешка, для ленивых будет два руководства одно для Linux и два для
    Оконцев.

## Как создать загрузочную флешку на nix
записать образ на USB Flash drive можно программой dd следующим образом:
```javascript
# dd bs=4M if=archlinux-2013.02.01-dual.iso of=/dev/sdx status=progress &amp;&amp; sync
```
/dev/sdx — это USB накопитель, на который вы хотите записать образ(обычно /dev/sdb).

[unetbootin](https://unetbootin.github.io/)  
[rufus]( https://rufus.akeo.ie/)  
[LinuxLive USB Creator](https://www.linuxliveusb.com/)  
я пользовался LinuxLive USB Creator'ом , unetbootin не помог :sad:
подробно описывать как всё делается можно но пока не буду , но в в этих ваших интернетах вогон
ссылок как это делать
# как создать загрузочный CD (на*** кому они нужны да?)
Записать образ на CD можно программой wodim из набора cdrkit.
```javascript
$ wodim -v dev=/dev/cdrw archlinux-2013.02.01-dual.iso
```
[как записать загрузочный диск](https://www.google.ru/search?q=%D0%BA%D0%B0%D0%BA+%D0%B7%D0%B0%D0%BF%D0%B8%D1%81%D0%B0%D1%82%D1%8C+%D0%B7%D0%B0%D0%B3%D1%80%D1%83%D0%B7%D0%BE%D1%87%D0%BD%D1%8B%D0%B9+%D0%B4%D0%B8%D1%81%D0%BA&ie=utf-8&oe=utf-8&gws_rd=cr&dcr=0&ei=-yi7WvPBGumV6ATPv57oCw)
