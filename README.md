# Otus_8 Загрузка Linux

Задание

1. Попасть в систему без пароля несколькими способами
2. Установить систему с LVM, после чего переименовать VG
3. Добавить модуль в initrd

   
## Задание 1:  Попасть в систему без пароля несколькими способами

Способ 1

![](https://github.com/Sunabak/Otus_8/blob/main/screens/%D0%A1%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%201.png)
![](https://github.com/Sunabak/Otus_8/blob/main/screens/%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1_1_%D1%81%D0%BC%D0%B5%D0%BD%D0%B0%20%D0%BF%D0%B0%D1%80%D0%BE%D0%BB%D1%8F.png)
![](https://github.com/Sunabak/Otus_8/blob/main/screens/%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1_1_%D0%B2%D1%85%D0%BE%D0%B4_%D1%81_%D0%BD%D0%BE%D0%B2%D1%8B%D0%BC%20%D0%BF%D0%B0%D1%80%D0%BE%D0%BB%D0%B5%D0%BC.png)

Способ 2


![](https://github.com/Sunabak/Otus_8/blob/main/screens/%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%202.png)
![](https://github.com/Sunabak/Otus_8/blob/main/screens/%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1_2_%D1%81%D0%BC%D0%B5%D0%BD%D0%B0%20%D0%BF%D0%B2.png)

Способ 3

![](https://github.com/Sunabak/Otus_8/blob/main/screens/%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%203.png)
![](https://github.com/Sunabak/Otus_8/blob/main/screens/%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%203%20%D1%81%D0%BC%D0%B5%D0%BD%D0%B0%20%D0%BF%D0%B2.png)

## Задание 2:  Установить систему с LVM, после чего переименовать VG

Текущее состояние системы

```
[root@otus-task8 vagrant]# vgs
  VG         #PV #LV #SN Attr   VSize   VFree  
  VolGroup00   1   3   0 wz--n- <38,97g <27,47g
  vg_var       2   1   0 wz--n-   1,99g 128,00m
```
Переименуем VG группу

```
[root@otus-task8 vagrant]# vgrename VolGroup00 OtusRoot
  Volume group "VolGroup00" successfully renamed to "OtusRoot"
```
Везде заменāем старое название на новое.

```
[root@otus-task8 vagrant]# for i in /etc/fstab /etc/default/grub /boot/grub2/grub.cfg; do sed -ibak 's/VolGroup00/OtusRoot/g' "$i"; done
```

Перегружаем и проверяем

```
Last login: Tue Jan  9 08:50:54 2024 from 10.0.2.2
[vagrant@otus-task8 ~]$ sudo su
[root@otus-task8 vagrant]# vgs
  VG       #PV #LV #SN Attr   VSize   VFree  
  OtusRoot   1   3   0 wz--n- <38,97g <27,47g
  vg_var     2   1   0 wz--n-   1,99g 128,00m
````

## Задание 3: Добавить модуль в initrd
Создаем папку и файлы

```
root@otus-task8 vagrant]# mkdir /usr/lib/dracut/modules.d/01test
[root@otus-task8 vagrant]# cd /usr/lib/dracut/modules.d/01test
[root@otus-task8 01test]# cat > module_setup.sh
#!/bin/bash

check() {
    return 0
}

depends() {
    return 0
}

install() {
    inst_hook cleanup 00 "${moddir}/test.sh"
}
[root@otus-task8 01test]# cat > test.sh
#!/bin/bash

exec 0<>/dev/console 1<>/dev/console 2<>/dev/console
cat <<'msgend'
Hello! You are in dracut module!
 ___________________
< I'm dracut module >
 -------------------
   \
    \
        .--.
       |o_o |
       |:_/ |
      //   \ \
     (|     | )
    /'\_   _/`\
    \___)=(___/
msgend
sleep 10
echo " continuing...."
[root@otus-task8 01test]# mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
```
Перегружаем и проверяем:

![](https://github.com/Sunabak/Otus_8/blob/main/screens/%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B53.png)


