# Домашнее задание к занятию "`Система мониторинга Zabbix`" - `Поздникина Евгения`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---
### Задание 1

![AdminkaZabbix](https://github.com/PozdnikinEugene/hw-02/blob/main/img/1-1.png)

#### Использованые команды

Установите PostgreSQL
```
sudo apt install postgresql 
```
a. Установите репозиторий Zabbix
```
# wget https://repo.zabbix.com/zabbix/6.0/debian/pool/main/z/zabbix-release/zabbix-release_latest_6.0+debian11_all.deb
# dpkg -i zabbix-release_latest_6.0+debian11_all.deb
# apt update
```
b. Установите Zabbix сервер, веб-интерфейс и агент
```
# apt install zabbix-server-pgsql zabbix-frontend-php php7.4-pgsql zabbix-apache-conf zabbix-sql-scripts zabbix-agent
```
c. Создайте базу данных
Установите и запустите сервер базы данных.
```
# sudo -u postgres createuser --pwprompt zabbix
# sudo -u postgres createdb -O zabbix zabbix

```
На хосте Zabbix сервера импортируйте начальную схему и данные. Вам будет предложено ввести недавно созданный пароль.
```
# zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix
```
d. Настройте базу данных для Zabbix сервера
Отредактируйте файл /etc/zabbix/zabbix_server.conf
```
DBPassword=password
```
Я воспользуюсь sed
```
# sed -i 's/# DBPassword=/DBPassword=password/g' /etc/zabbix/zabbix_server.conf
```

e. Запустите процессы Zabbix сервера и агента
Запустите процессы Zabbix сервера и агента и настройте их запуск при загрузке ОС.
```
# systemctl restart zabbix-server zabbix-agent apache2
# systemctl enable zabbix-server zabbix-agent apache2
```
f. Open Zabbix UI web page
The default URL for Zabbix UI when using Apache web server is http://host/zabbix
```
http://176.108.241.192/zabbix
```



---
### Задание 2
1.Cкриншот раздела Configuration > Hosts, где видно, что агенты подключены к серверу
![Config_Host](https://github.com/PozdnikinEugene/hw-02/blob/main/img/2-1.png)

2.Cкриншот лога zabbix agent, где видно, что он работает с сервером
В нашем случае отсутсвие ошибок признак успеха так как в лог записываются только ошибки.
![Logs](https://github.com/PozdnikinEugene/hw-02/blob/main/img/2-2.png)

3.Скриншот раздела Monitoring > Latest data для обоих хостов, где видны поступающие от агентов данные.
![Mon_latest](https://github.com/PozdnikinEugene/hw-02/blob/main/img/2-3.png)

#### Использованые команды
##### В качестве клиентов выступает Ubuntu 22.02

a. Become root user
Start new shell session with root privileges.
```
$ sudo -s
```
b. Установите репозиторий Zabbix
```
# wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_6.0+ubuntu22.04_all.deb
# dpkg -i zabbix-release_latest_6.0+ubuntu22.04_all.deb
# apt update
```
c. Установите Zabbix агент
```
# apt install zabbix-agent
```
d. Запустите процесс Zabbix агента
Запустите процесс Zabbix агента и настройте его запуск при загрузке ОС.
```
# systemctl restart zabbix-agent
# systemctl enable zabbix-agent
```

Для разрешение подключения нужно указать наш сервер
```
sed -i 's/Server=127.0.0.1/Server=176.108.241.192/g' /etc/zabbix/zabbix_agentd.conf
systemctl restart zabbix-agent
```
Для Active Checks так же нужно указать наш сервер 
```
sed -i 's/ServerActive=127.0.0.1/ServerActive=176.108.241.192/g' /etc/zabbix/zabbix_agentd.conf
systemctl restart zabbix-agent
```
---


### Задание 3 *
Cкриншот раздела Latest Data, где видно свободное место на диске C:
![C](https://github.com/PozdnikinEugene/hw-02/blob/main/img/3-1.png)

#### Использованые команды


Данных нет, создайтю новый Элемент данных (Item) на узле сети
```
-Имя: Свободное место на диске C:
-Тип: Zabbix агент (или Zabbix агент (активный))
-Ключ: vfs.fs.size[C:,free] — для объема в байтах.
--Или vfs.fs.size[C:,pfree] — для свободного места в процентах.
-Тип информации: Числовой (целое положительное) (для байт) или Числовой (плавающий) (для процентов).
```
---
