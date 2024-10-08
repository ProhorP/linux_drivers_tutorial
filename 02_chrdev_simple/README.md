Задание:  
Создать символный драйвер, который будет иметь в ядре область памяти задаваемую при загрузке.
В которым можно будет писать и из которого можно читать
```
make
sudo insmod chrdev_simple.ko size=10000
```
На этом этапе при загрузке драйвера было выполнено:
```
1. Регистрация символьного устройства:

Вызов alloc_chrdev_region(&dev, scull_minor, count, "chrdev_simple") регистрирует символьное устройство и резервирует диапазон номеров устройств, начиная с scull_major и scull_minor.
Успешная регистрация возвращает старший (major) и младший (minor) номера устройства, которые можно использовать для работы с устройством.

2. Инициализация и добавление устройства:

После успешного выделения памяти с помощью kmalloc, инициализируется структура cdev и происходит вызов cdev_add(&cdev, dev, 1) для добавления вашего символьного устройства в систему.
```

Далее берем нужную инфу из логов
```
sudo journalctl -r
```
Там выведется например

```
chrdev_simple: scull_minor=0
chrdev_simple: scull_major=245
```
Теперь нужно выполнить 3-ю часть на стороне пользователя:

```
3. Создание файлового устройства:

Обычно после этого необходимо создать файл устройства в директории /dev, который будет ассоциирован с вашим символьным устройством.
Это можно сделать вручную через команду mknod в консоли или автоматически через утилиту udev, которая обычно создает файлы устройств автоматически при регистрации устройства в системе.
```
Делаем это так:
```
sudo mknod /dev/chrdev_simple c <major_number> <minor_number> # это пример шаблона

sudo mknod /dev/chrdev_simple c 245 0 # это реальный пример
```
Все теперь тестируем:
```
su
echo "Hello, World!" > /dev/chrdev_simple #тут запишется в наш буфер
cat /dev/chrdev_simple #тут выведется на консоль то, что ранее записали
rm /dev/chrdev_simple #удаляем файловое устройство
exit
```

Далее удаляем драйвер устройства
```
sudo rmmod chrdev_simple.ko
make clean
```
