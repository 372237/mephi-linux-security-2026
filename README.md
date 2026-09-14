README
Практическая работа: механизмы привилегий в Linux (РЕД ОС)


Задание 1. Создание пользователя user1
--------------------------------------

1. Создание группы students
   sudo groupadd students

2. Создание пользователя с UID 1234
   sudo useradd -u 1234 -G students user1

3. Установка пароля
   sudo passwd user1

4. Срок действия пароля 90 дней
   sudo chage -M 90 user1

5. Проверка. скриншот 1_5.png
   id user1

6. Проверка срока пароля. скриншот 1_6.png
   sudo chage -l user1


Задание 2.1. Поиск файлов с битом set-UID
-----------------------------------------

1. Поиск файлов с SUID. результат созранить в файл set-uid.txt
   sudo find / -type f -perm -4000 -exec ls -l {} \; 2>/dev/null > /tmp/set-uid.txt



Задание 2.2. Поиск процессов с EUID=0 и RUID не равен 0
-------------------------------------------------------

1. Терминал 1: под user1 запускаем sudo -i
   sudo -i

2. Терминал 2: поиск процессов и сохранение вывода в euid0__processes.txt
   ps -eo pid,ruid,euid,comm | awk '$3==0 && $2!=0' > /tmp/euid0__processes.txt



Задание 3. Изучение механизма set-UID
-------------------------------------

1. Выбор утилиты
   /bin/cat

2. Привилегированная операция
   cat /etc/shadow

3. Проверка отказа. скриншот 3_2_1.png
   cat /etc/shadow (от user1)

4. Копирование
   cp /bin/cat /home/user1/mycat

5. Смена владельца
   sudo chown root:root /home/user1/mycat

6. Установка SUID
   sudo chmod u+s /home/user1/mycat

7. Проверка прав
   ls -l /home/user1/mycat

8. Выполнение. скриншот 3_2_2.png
   /home/user1/mycat /etc/shadow


Задание 4. Изучение механизма capabilities
------------------------------------------

1. Выбор утилиты
   /bin/chown

2. Привилегированная операция
   chown root:root testfile

3. Создание файла под user1
   touch /home/user1/testfile

4. Проверка отказа. скриншот 4_1.png
   chown root:root testfile

5. Копирование
   cp /bin/chown /home/user1/mychown

6. Назначение capability
   sudo setcap cap_chown=ep /home/user1/mychown

7. Проверка. скриншот 4_2.png
   getcap /home/user1/mychown

8. Выполнение
   /home/user1/mychown root:root testfile

9. Проверка результата. скриншот 4_3.png
   ls -l /home/user1/testfile



Задание 5. Изучение механизма sudo
----------------------------------

1. Выбор операции
   Изменение системного времени

2. Редактирование sudoers
   sudo visudo

3. Добавление правила, сохранение и выход из vi
   user1 ALL=(ALL) /usr/bin/date
	Esc
	:wq

4. Проверка прав. скриншот 4_4.png
   sudo -l (от user1)

5. Выполнение. скриншот 4_5.png
   sudo date -s "10:00"


Приложение. Артефакты и подтверждения
-------------------------------------

| Файл | Раздел | Что проверяется |
| --- | --- | --- |
| mephi-screenshot.png | 6 | Визуальное подтверждение |
| history.out | Все | Выполненные команды |
| stat.out | 1,3,4 | Установка прав доступа на домашнюю директорию пользователя и утилиты |
| getcap.out | 4 | Настройка привилегий |
| /etc/passwd.png | 1 | Управление пользователями |
| /etc/shadow.png | 1 | Управление пользователями |
| /etc/group.png | 1 | Управление группами |
| /etc/sudoers.png | 5 | Настройка sudo |
| set-uid.txt | 2 | Поиск файлов |
| euid0__processes.txt | 2 | Мониторинг процессов |
