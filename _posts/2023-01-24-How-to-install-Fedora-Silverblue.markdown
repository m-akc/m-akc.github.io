---
layout: post
title:  "Инструкция: как установить Fedora Silverblue"
date:   2023-01-24 2:45:45 +0100
categories:
---

# Вступление
В наше время сложно найти дистрибутив, который будет достаточно стабильным для того, чтобы на нем можно было работать... Если вы хотите еще и свежий софт, то выбор у нас будет совсем маленький. Один из самых лучших вариантов для опытного пользователя - это Fedora Silverblue.

# Что это такое?
Fedora Silverblue - Это неизменяемая операционная система, которая полностью бесплатна. Что значит неизменяемая? Это значит, что операции из под рута очень сильно ограничены. Просто так поставить софт или что-то изменить не выйдет. Кроме того, этот дистрибутив поддерживает снэпшоты из коробки. Если коротко - при возникновении проблем при обновлении можно будет легко откатиться назад на ту версию ОС, которая у нас была до начала обновления дистрибутива. Так как же тут устанавливать софт? Ответ прост: Flatpak + Контейнеры.
Еще одна фича Silverblue - Двже если вы обновите не всю систему, а только ее часть, то потребуется перезагрузка. Но к этому довольно быстро привыкаешь. На Windows же никто не жалуется?

# Установка
Приступим. Хоть я и люблю Linux, но без Windows Обойтись тоже сложно =)
Поэтому будем делать Dualboot.
Не рекомендую ставить Silverblue на один SSD. Желательно иметь Два разных диска и для каждой ОС - отдельный EFI.
Итак, начнем.
Мы записали на флешку OC, очистили диск...
И первое, что мы можем заметить - что Silverblue не предлагает Live окружения. Пощупать данный дистрибутив до установки невозможно.
Далее - нужно выбрать ручную разметку диска: Конфигурация устройств хранения - Дополнительно(Blivet-GUI)
Структура в итоге делаем такую:
/boot - файловая система ext4, 1024 mb
/boot/efi - Файловая система EFI System partition, 512 mb
Том BTRFS - имя BTRFS
После этого выбираем устройство BTRFS и добавляем 2 подтома:
1: @home - имя, /home - точка монтирования.
2: @ - имя, / - точка монтирования.

# После установки
Первое, что нам надо сделать - это обновить систему до актуального состояния.
{% highlight bash %}
rpm-ostree upgrade
{% endhighlight %}
Второе - Установить Flatpak.
{% highlight bash %}
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
{% endhighlight %}
Дальше ставим RPM Fusion:
{% highlight bash %}
sudo rpm-ostree install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
{% endhighlight %}
Ставим Драйвера. У меня Nvidia, поэтому:
{% highlight bash %}
sudo rpm-ostree install akmod-nvidia xorg-x11-drv-nvidia
sudo rpm-ostree install akmod-nvidia xorg-x11-drv-nvidia-cuda #optional if using nvidia-smi or cuda
rpm-ostree install nvidia-vaapi-driver
sudo rpm-ostree kargs --append=rd.driver.blacklist=nouveau --append=modprobe.blacklist=nouveau --append=nvidia-drm.modeset=1
{% endhighlight %}

Последнее, что осталось - убрать баг при котором дублируются снимки системы в Grub во время загрузки.
Если у вас этот баг есть - то (В противном случае - этот шаг пропускаем):
{% highlight bash %}
sudo touch /boot/grub2/.grub2-blscfg-supported 
sudo grub2-mkconfig -o /boot/loader/grub.cfg
{% endhighlight %}

# Выводы
Fedora Silverblue - Уникальный дистрибутив, который сочетает в себе стабильность системы и самый свежий софт. Баги, хоть и встречаются, не особо критичные. 
Если вы любите Linux - Систему ставить однозначно.