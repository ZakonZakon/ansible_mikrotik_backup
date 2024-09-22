# ansible_mikrotik backup script

Скрипт представляет собой Ansible-плейбук, который предназначен для выполнения следующих действий:

## 1. Получение имени устройства (device identity): 
Плейбук подключается к хостам, указанным в hosts: mi, и получает имя устройства, используя команду sshpass -p '{{ ansible_password }}' ssh -o StrictHostKeyChecking=no {{ ansible_user }}@{{ inventory_hostname }} /system identity print. Это имя сохраняется в переменную device_name.

## 2. Создание полного имени файла бэкапа:
На основе имени устройства, имени хоста и текущей даты формируется полное имя файла бэкапа, которое сохраняется в переменную host_name.

## 3. Проверка наличия существующего бэкапа:
Плейбук проверяет, существует ли уже бэкап с таким именем в директории, указанной в backup_dir. Если файл существует, выводится предупреждение.

## 4. Создание бэкапа на удаленном хосте: 
Если бэкап не существует, плейбук создает новый бэкап на удаленном хосте, используя команду sshpass -p '{{ ansible_password }}' ssh -o StrictHostKeyChecking=no {{ ansible_user }}@{{ inventory_hostname }} /system backup save name={{ host_name }} password=parol.

## 5. Загрузка бэкапа на локальный хост: 
После успешного создания бэкапа на удаленном хосте, плейбук загружает его на локальный хост, используя команду sshpass -p '{{ ansible_password }}' scp -o StrictHostKeyChecking=no {{ ansible_user }}@{{ inventory_hostname }}:{{ host_name }} {{ backup_dir }}.

## 6. Удаление бэкапа с удаленного хоста: 
Наконец, плейбук удаляет бэкап с удаленного хоста, используя команду sshpass -p '{{ ansible_password }}' ssh -o StrictHostKeyChecking=no {{ ansible_user }}@{{ inventory_hostname }} /file remove {{ host_name }}.

## 7. Очистка сессии

Таким образом, этот плейбук автоматизирует процесс создания, загрузки и удаления бэкапов на удаленных хостах, указанных в группе mi.
