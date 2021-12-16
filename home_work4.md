## Task 1: Users and groups

Используйте команды: groupadd, useradd, passwd, chage и другие.
Создайте группу sales с GID 4000 и пользователей bob, alice, eve c основной группой sales. 
Измените пользователям пароли.
Все новые аккаунты должны обязательно менять свои пароли каждый 30 дней.
Новые аккаунты группы sales должны истечь по окончанию 90 дней срока, а bob должен изменять его пароль каждые 15 дней.

Дополнительно:
Заставьте пользователей сменить пароль после первого логина.
```bash
chage –d 0 bob
chage –d 0 alice
chage –d 0 eve
```
Предварительный шаг:
Исследуйте файл /etc/login.defs.
Исследуйте, как работает команда date и как её использовать совместно с chage.
```bash
[root@localhost ~]# groupadd -g 4000 sales
[root@localhost ~]# cat /etc/group
```
Создайте группу sales с GID 4000 и пользователей bob, alice,
eve c основной группой sales.
```bash
[root@localhost ~]# useradd bob
[root@localhost ~]# useradd alice
[root@localhost ~]# useradd eve
[root@localhost ~]# usermod -g sales bob
[root@localhost ~]# usermod -g sales alice
[root@localhost ~]# usermod -g sales eve
```
Измените пользователям пароли.
```bash
passwd bob
passwd alice
passwd eve
```
Все новые аккаунты должны обязательно менять свои пароли
каждый 30 дней.
```bash
sed -i 's/PASS_MAX_DAYS   99999/PASS_MAX_DAYS   30/' /etc/login.defs
```
Новые аккаунты группы sales должны истечь по окончанию 90
дней срока, а bob должен изменять его пароль каждые 15
дней.
```bash
chage -E $(date -d +90days +%Y-%m-%d) bob
chage -E $(date -d +90days +%Y-%m-%d) alice
chage -E $(date -d +90days +%Y-%m-%d) eve

chage –M 15 bob
```




## Task 2: Controlling access to files with Linux file system permissions

Используйте команды: su, mkdir, chown, chmod и другие.
Создайте трёх пользователей glen, antony, lesly.
```bash
useradd glen
useradd antony
useradd lesly
```
У вас должна быть директория /home/students, где эти три пользователя могут работать совместно с файлами.
```bash
mkdir /home/students
```
Должен быть возможен только пользовательский и групповой доступ, создание и удаление файлов в /home/students. 
Файлы, созданные в этой директории, должны автоматически присваиваться группе студентов students.
```bash
groupadd students
usermod –G students glen
usermod –G students antony
usermod –G students lesly
chown -R :students /home/students
chmod g+s /home/students
```
Предварительный шаг:
Исследуйте, для чего нужны файлы .bashrc и .profile.




## Task3: ACL

Детективное агентство Бейкер Стрит создает коллекцию совместного доступа для хранения файлов дел, в которых члены группы bakerstreet будут иметь права на чтение и запись.
Ведущий детектив, Шерлок Холмс, решил, что члены группы scotlandyard также должны иметь возможность читать и писать в общую директорию. Тем не менее, Холмс считает, что инспектор Джонс является достаточно растерянным, и поэтому он должен иметь доступ только для чтения. 
Миссис Хадсон только начала осваивать Linux и смогла создать общую директорию и скопировать туда несколько файлов. Но сейчас время чаепития, и она попросила вас закончить работу.

Ваша задача - завершить настройку директории общего доступа. 
Директория и всё её содержимое должно принадлежать группе bakerstreet, при этом файлы должны обновляться для чтения и записи для владельца и группы (bakerstreet). У других пользователей не должно быть никаких разрешений. 
Вам также необходимо предоставить доступы на чтение и запись для группы scotlandyard, за исключением Jones, который может только читать документы.
Убедитесь, что ваша настройка применима к существующим и будущим файлам. После установки всех разрешений в директории проверьте от каждого пользователя все его возможные доступы.

Используйте команды: touch, mkdir, chgrp, chmod, getfacl, setfacl и другие. a
Создайте общую директорию /share/cases.
Создайте группу bakerstreet с пользователями holmes, watson.
Создайте группу scotlandyard с пользователями lestrade, gregson, jones.
Задайте всем пользователям безопасные пароли.

Предварительный шаг:
От суперпользователя создайте папку /share/cases и создайте внутри 2 файла murders.txt и moriarty.txt.
```bash
[root@localhost linux]# mkdir -p /share/cases
[root@localhost linux]# cd /share/cases
[root@localhost cases]# touch murders.txt moriarty.txt
[root@localhost cases]# groupadd bakerstreet
[root@localhost cases]# groupadd scotlandyard
[root@localhost cases]# useradd holmes
[root@localhost cases]# useradd watson
[root@localhost cases]# useradd lestrade
[root@localhost cases]# useradd gregson
[root@localhost cases]# useradd jones
[root@localhost cases]# passwd holmes
Changing password for user holmes.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
[root@localhost cases]# passwd watson
Changing password for user watson.
New password:
Retype new password:
Sorry, passwords do not match.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
[root@localhost cases]# passwd lestrade
Changing password for user lestrade.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
[root@localhost cases]# passwd gregson
Changing password for user gregson.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
[root@localhost cases]# passwd jones
Changing password for user jones.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
[root@localhost cases]# usermod -aG bakerstreet holmes
[root@localhost cases]# usermod -aG bakerstreet watson
[root@localhost cases]# usermod -aG scotlandyard lestrade
[root@localhost cases]# usermod -aG scotlandyard gregson
[root@localhost cases]# usermod -aG scotlandyard jones
```
Директория и всё её содержимое должно принадлежать группе bakerstreet
```bash
chown -R :bakerstreet /share/cases
chmod  -R ug+rw /share/cases
```
других пользователей не должно быть никаких разрешений. 
```bash
chmod -R o-rwx /share/cases
```
доступы на чтение и запись для группы scotlandyard 
```bash
setfacl -R -m d:g:scotlandyard:rw,g:scotlandyard:rw /share/cases
setfacl -m d:g:scotlandyard:r-x,g:scotlandyard:r-x /share/cases
```
исключением Jones только читать документы
```bash
setfacl -R -m u:jones:r-x /share/cases
```
