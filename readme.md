# 🐧 DevOps Linux, Kubernetes, Helm & Docker Cheat Sheet

---

# 📌 SYSTEM INFORMATION

```bash
uname -a               # информация о ядре и архитектуре системы
hostnamectl            # версия ОС и hostname
uptime                 # время работы сервера и нагрузка
whoami                 # текущий пользователь
id                     # UID, GID и группы пользователя
date                   # текущая дата и время
timedatectl            # время и NTP синхронизация
lsb_release -a         # версия дистрибутива Linux
```

---

# 💾 MEMORY / CPU

```bash
free -h                # использование RAM
top                    # мониторинг процессов
htop                   # интерактивный мониторинг процессов
mpstat -P ALL 1        # нагрузка на все CPU
vmstat 1               # статистика процессов, памяти, IO
lscpu                  # информация о процессоре
sar -u 1 5             # нагрузка CPU за время
```

---

🔁 RSYNC

```bash
rsync -avh source/ destination/                    # базовая синхронизация
rsync -avzh /var/www user@server:/var/www         # копирование на удалённый сервер
rsync -avzh user@server:/backup /local/backup     # копирование с удалённого сервера
rsync -avh --delete /source/ /destination/        # зеркалирование (удаляет лишние файлы)
rsync -avh --dry-run --delete /data /backup       # dry-run (проверка перед запуском)
rsync -avh --exclude="*.log" app/ backup/         # исключить файлы
rsync -avh --bwlimit=1000 /data backup/           # ограничение скорости (KB/s)
rsync -avh -e "ssh -p 2222" /data user@server:/data  # через нестандартный SSH порт
rsync -a --delete /var/www /backup/www_$(date +%F)  # backup с датой

-a архивный режим
-v подробный вывод
-z сжатие при передаче
-h читаемые размеры

```

---


# 📤 SCP

```bash
scp file.txt user@server:/home/user/              # отправить файл
scp user@server:/home/user/file.txt .             # скачать файл
scp -r project/ user@server:/var/www/             # копировать директорию
scp -P 2222 file.txt user@server:/home/user/      # через нестандартный порт
scp -i ~/.ssh/id_rsa file.txt user@server:/home/user/  # указать SSH ключ
scp -l 1000 file.txt user@server:/home/user/      # ограничить скорость (Kbit/s)
```

---

🔗 LN (Links)

```bash
ln -s /var/log/app.log app.log        # создать symbolic link, в текущей папке появляется файл app.log, который ссылается на /var/log/app.log
ln file.txt file_hardlink.txt         # создать hard link, ориг. файл и file_hardlink.txt     hard link – ещё одно имя для того же файла
ln -sf /new/path config               # перезаписать существующий symlink
ln -s ../releases/2026-03-01 current  # относительная ссылка (удобно для deploy)
ls -li                                # показать inode (для проверки hard link)
rm app.log                            # удалить только symlink
```

---

# 💽 DISK & FILESYSTEM

```bash
df -h                  # использование диска
df -i                  # проверка inode
du -sh *               # размер директорий
du -ah / | sort -rh | head -20   # топ 20 больших файлов
lsblk                  # список дисков и разделов
blkid                  # UUID дисков
mount                  # смонтированные FS
mount -o remount,rw /  # перемонтировать FS в rw
stat -f /              # информация о файловой системе
```

---

# 📁 FILES & DIRECTORIES

```bash
ls -la                 # список файлов с правами и владельцами
ls -laht               # список файлов от новых к старым, в human виде
ls -lahtr              # список файлов от старых к новым, в human виде         
tree                   # структура директорий
stat file.txt          # метаданные файла
file file.txt          # тип файла
cp -r dir1 dir2        # копирование папки
mv old.txt new.txt     # переименование файла
rm -rf dir             # удалить папку
ln -s /var/log/app.log app.log  # создать symbolic link
head -n 20 file.log    # первые 20 строк
less file.log          # просмотр постранично
cat file.txt           # вывести файл полностью
split -b 100M largefile.tar.gz part_  # разделить большой файл
wc -l file.txt         # количество строк
diff file1.txt file2.txt  # сравнение файлов
cmp file1 file2        # побайтовое сравнение файлов
```

---


# 📚 TAR

```bash
tar -cvf archive.tar folder/                 # создать tar архив (без сжатия), c=create, v=verbose, f=файл архива
tar -czvf archive.tar.gz folder/             # создать tar.gz архив (gzip), z=gzip
tar -cjvf archive.tar.bz2 folder/            # создать tar.bz2 архив (bzip2), j=bzip2
tar -xvf archive.tar                          # распаковать tar архив (без сжатия)
tar -xzvf archive.tar.gz                       # распаковать tar.gz архив
tar -xjvf archive.tar.bz2                      # распаковать tar.bz2 архив
tar -tvf archive.tar.gz                         # просмотр содержимого архива без распаковки
tar -rvf archive.tar newfile.txt               # добавить файл в существующий архив (только uncompressed)
tar -xvf archive.tar path/to/file.txt          # извлечь конкретный файл из архива
tar -czvf archive.tar.gz folder/ --checkpoint=.1000 --totals  # создание архива с прогрессом
tar -czvf archive.tar.gz folder/ --exclude='*.log' --exclude='tmp/'  # создание архива с исключениями
tar -czvf backup.tar.gz folder1/ folder2/ folder3/  # архивирование нескольких папок

```

---

# 🔐 PERMISSIONS

```bash
chmod 755 script.sh           # rwxr-xr-x
chmod +x script.sh            # сделать исполняемым
chown user:group file.txt     # сменить владельца
chown -R www-data:www-data /var/www  # сменить рекурсивно
umask                          # показать default маску прав
getfacl file.txt              # показать ACL прав
setfacl -m u:user:rw file.txt  # добавить ACL права
```

---

# 🔍 FIND / SEARCH

```bash
find / -name config.yml                     # найти файл по имени
find /var/log -type f -mtime -1            # файлы изменённые за сутки
find . -type f -size +100M                 # файлы больше 100MB
find /tmp -type f -mtime +7 -delete        # удалить старые файлы
find . -name "*.log" -exec rm -f {} \;   # найти и удалить .log
locate nginx.conf                           # быстрый поиск
grep -r 'TODO' ./                           # рекурсивный поиск по тексту
```

---

# 🔎 GREP / TEXT

```bash
grep ERROR app.log                          # найти строку ERROR
grep -i error app.log                       # без учета регистра
grep -r password /etc                       # рекурсивный поиск
awk '{print $1}' file.txt                   # вывести первую колонку
sed 's/old/new/g' file.txt                  # заменить текст
cut -d':' -f1 /etc/passwd                   # первая колонка
sort file.txt                                # сортировка строк
uniq file.txt                                # удалить дублирующиеся строки
```

---

# ⚙️ PROCESS MANAGEMENT

```bash
ps aux                          # список процессов
ps aux | grep nginx             # найти процесс nginx
ps aux --sort=-%mem | head      # топ по памяти
kill -9 PID                     # убить процесс
pkill nginx                      # убить процессы по имени
nice -n 10 command               # запустить с пониженным приоритетом
top -H                            # мониторинг потоков процессов
```

---

# 🌐 NETWORK

```bash
ip a                            # IP адреса
ip route                        # маршруты
ss -tulnp                        # открытые порты
curl -I https://google.com      # проверить HTTP заголовки
curl -v http://localhost:8080   # подробный HTTP запрос
nc -zv 127.0.0.1 5432          # проверить порт
dig google.com                   # DNS запрос
tcpdump -i eth0 port 80          # перехват трафика
ping 8.8.8.8                     # проверить соединение
traceroute google.com             # маршрут до хоста
nslookup google.com               # DNS lookup
iptables -L -v -n                 # правила firewall
```

---

# 🧾 CERT

```bash

openssl s_client -connect km164.ru:443 -servername km164.ru                                          # сертификат сайта 
openssl s_client -connect km164.ru:443 -servername km164.ru 2>/dev/null | openssl x509 -noout -dates # даты действия сертификата 
openssl x509 -in certificate.crt -text -noout                                                        # локальный сертификат crt/pem
openssl x509 -in certificate.crt -noout -dates                                                       # даты действия локального сертификата crt/pem
openssl rsa -in private.key -check                                                                   # просмотр приватного ключа


```
``

# 📜 LOGS

```bash
tail -f /var/log/syslog                   # смотреть лог в реальном времени
tail -100 app.log                         # последние 100 строк
journalctl -xe                            # системные ошибки
journalctl -u nginx -f                    # логи конкретного сервиса в реальном времени
journalctl -p err                         # только ошибки (err)
journalctl --since "2026-03-01 10:00"     # логи с определённого времени
journalctl --disk-usage                   # проверить сколько места занимают логи
dmesg | tail -50                          # последние сообщения ядра
```

---

# 🔄 SYSTEMCTL

```bash
systemctl status nginx          # статус сервиса
systemctl status <PID>          # статус сервиса по номеру PID
systemctl restart nginx         # перезапуск
systemctl stop nginx            # остановить
systemctl enable nginx          # автозапуск
systemctl list-timers           # список systemd таймеров
at 23:00 -f script.sh           # запустить скрипт один раз
journalctl -u SERVICE           # логи конкретного systemd сервиса
```

---

# 📦 HELM

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami  # добавить репозиторий
helm repo update                                           # обновить репозитории
helm search repo nginx                                     # поиск чарта
helm install my-nginx bitnami/nginx                        # установить чарт
helm install my-nginx bitnami/nginx -n prod               # установить в namespace prod
helm upgrade my-nginx bitnami/nginx -f values.yaml        # обновление с values
helm history my-nginx                                      # история релизов
helm rollback my-nginx 1                                   # откат к ревизии
helm uninstall my-nginx                                     # удалить релиз
helm template my-nginx bitnami/nginx                        # вывести YAML без установки
helm lint .                                                # проверка чарта
helm install test bitnami/nginx --dry-run --debug          # dry-run установка
helm get values my-nginx -n NAMESPACE                     # получить values релиза
helm get manifest my-nginx -n NAMESPACE                   # получить manifest релиза
helm upgrade --install my-nginx bitnami/nginx -f values.yaml -n NAMESPACE  # upgrade или install
helm pull bitnami/nginx --untar                            # спулить и распаковать образ
helm pull oci://registry-1.docker.io/bitnamicharts/redis --untar --untardir . # спулить и распаковать образ из кастомного registry в текущую директорию 
```

---

# 📦 KREW

```bash
kubectl get pod nginx -o yaml | kubectl neat           # показ yaml без метаданных и аннотаций
kubectl tree deployment nginx                          # показ дерева deployment
kubectl triage pod -n NAMESPACE                        # показ причин завершения пода и 50 последних и предыдущих логов 

```

---

# 🐳 DOCKER / CONTAINERS

## 🔹 Управление контейнерами

```bash
docker ps                        # показать запущенные контейнеры
docker ps -a                     # показать все контейнеры
docker start CONTAINER           # запустить остановленный контейнер
docker stop CONTAINER            # остановить контейнер
docker restart CONTAINER         # перезапустить контейнер
docker kill CONTAINER            # принудительно убить контейнер
docker rm CONTAINER              # удалить контейнер
docker exec -it CONTAINER bash  # войти в контейнер
docker logs CONTAINER            # логи контейнера
docker logs -f CONTAINER         # логи в реальном времени
docker inspect CONTAINER         # подробная информация контейнера
```

## 🔹 Образы

```bash
docker images                    # список локальных образов
docker pull IMAGE                # скачать образ
docker push IMAGE                # отправить образ на Docker Hub
docker rmi IMAGE                 # удалить локальный образ
docker build -t myapp .          # собрать образ
docker history IMAGE             # история образа
docker inspect IMAGE             # информация об образе
```

## 🔹 Сети и порты

```bash
docker network ls                # список docker сетей
docker network inspect NETWORK   # подробности сети
docker network create mynet      # создать сеть
docker network connect mynet CONTAINER # подключить контейнер к сети
docker port CONTAINER            # показать проброшенные порты
```

## 🔹 Тома / Volumes

```bash
docker volume ls                 # список томов
docker volume inspect VOLUME     # информация о томе
docker volume create myvolume    # создать том
docker volume rm VOLUME          # удалить том
docker run -v myvolume:/data ... # подключить том к контейнеру
```

## 🔹 Мониторинг и статистика

```bash
docker stats                     # CPU, память, IO контейнеров
docker top CONTAINER             # процессы внутри контейнера
docker inspect CONTAINER         # подробная информация
docker system df                 # обзор ресурсов
docker system prune -af          # очистка неиспользуемых ресурсов
```

## 🔹 Docker Compose

```bash
docker-compose up -d             # поднять сервисы
docker-compose down              # остановить сервисы
docker-compose logs -f           # смотреть логи
docker-compose ps                # статус сервисов
docker-compose build             # пересобрать образы
docker-compose restart SERVICE   # перезапуск сервиса
docker-compose exec SERVICE bash # зайти внутрь сервиса
```

---

# 🐳 KUBERNETES / kubectl

```bash
kubectl get pods -A                                   # список всех pod'ов
kubectl get nodes                                     # список нод
kubectl get services -A                               # сервисы
kubectl describe pod NAME --namespace=NAME            # подробности pod
kubectl logs NAME                                     # логи контейнера
kubectl logs -f NAME                                  # логи в интерактивном режиме
kubectl exec -ti NAME bash                            # интерактивный bash в pod
kubectl scale deployment/nginx --replicas=10          # масштабирование deployment
kubectl delete pod NAME --grace-period=0 --force      # удалить зависший pod
kubectl get pods -n NAMESPACE | grep Terminating | awk '{print $1}' | xargs kubectl delete pod -n NAMESPACE --grace-period=0 --force
kubectl get configmap NAME -n NAMESPACE              # получить configmap
kubectl create configmap NAME --from-file=file.txt   # создать configmap
kubectl get secrets                                   # список secrets
kubectl create secret generic db-user-pass --from-file=username.txt --from-file=password.txt
kubectl top pod -n NAMESPACE                          # CPU/Memory pod
kubectl top node                                      # CPU/Memory node
kubectl rollout status deployment/nginx -n NAMESPACE  # статус rollout
kubectl rollout history deployment/nginx -n NAMESPACE # посмотреть историю ревизий
kubectl describe deployment nginx -n NAMESPACE        # описание deployment
kubectl rollout undo deployment/nginx --to-revision=2  # откат deployment
kubectl get events -n NAMESPACE                       # последние события в namespace
kubectl get ingress -n NAMESPACE                      # список ingress
kubectl get pvc -n NAMESPACE                           # список PVC
kubectl get pv                                         # список PV
kubectl apply -f file.yaml                             # применить конфигурацию YAML
kubectl get pod nginx -o yaml                         # текущие значения пода в YAML
```

---

# 🔥 DEVOPS ONE-LINERS

```bash
lsof -i :8080                           # кто занимает порт
lsof -i -P -N                           # -P показ порта, -n отключает DNS 
watch -n 2 "df -h"                       # мониторинг диска
ps aux --sort=-%cpu | head               # топ процессов по CPU
docker rm $(docker ps -aq)               # удалить все контейнеры
kubectl get pods --all-namespaces | grep Evicted | awk '{print $2}' | xargs kubectl delete pod -n NAMESPACE
grep -r ERROR /var/log/                  # поиск ошибок во всех логах
tar -czvf backup_$(date +%F).tar.gz /var/www  # создать архив сайта
```

---

# 🌿 GIT

## 🔹 Основные команды

```bash
git init                          # инициализировать репозиторий
git clone REPO_URL                # клонировать репозиторий
git status                        # статус изменений
git add file.txt                  # добавить файл в stage
git add .                         # добавить все изменения
git commit -m "message"           # создать коммит
git log                           # история коммитов
git log --oneline --graph --all   # красивая история с ветками
git diff                          # показать изменения
git show COMMIT                   # показать конкретный коммит
```

## 🔹 Ветки

```bash
git branch                        # список веток
git branch new-branch             # создать ветку
git checkout new-branch           # переключиться на ветку
git checkout -b new-branch        # создать и переключиться
git switch main                   # переключиться на ветку
git merge feature                 # слить feature в текущую ветку
git rebase main                   # перебазировать ветку
git branch -d branch-name         # удалить ветку
```

## 🔹 Remote

```bash
git remote -v                     # список remote
git fetch                         # получить изменения
git pull                          # получить и смержить
git push                          # отправить изменения
git push -u origin branch         # установить upstream
git remote add origin URL         # добавить remote
```

## 🔹 Полезное для DevOps

```bash
git stash                         # сохранить изменения временно
git stash pop                     # вернуть stash
git tag v1.0                      # создать тег
git tag                           # список тегов
git reset --hard HEAD~1           # откатить последний коммит
git revert COMMIT                 # безопасный откат коммита
git blame file.txt                # кто менял строки
```

---

# 📊 NCDU (Disk Usage Analyzer)

```bash
ncdu /                            # анализ всего диска
ncdu /var                         # анализ конкретной директории
```

## 🔹 Горячие клавиши внутри ncdu

- ↑ ↓ — перемещение по списку
- → / Enter — зайти в директорию
- ← — выйти на уровень выше
- d — удалить файл или папку
- r — пересканировать директорию
- g — перейти к конкретному пути
- s — сортировка (по размеру / имени)
- c — показать количество элементов
- q — выход

---

# 📈 HTOP (Process Viewer)

```bash
htop                              # запустить интерактивный мониторинг
```

## 🔹 Горячие клавиши внутри htop

- F2 — настройки
- F3 — поиск процесса
- F4 — фильтр
- F5 — tree view
- F6 — сортировка (CPU, MEM и т.д.)
- F9 — kill процесс
- F10 — выход
- u — фильтр по пользователю
- t — включить/выключить дерево процессов
- M — сортировка по памяти
- P — сортировка по CPU

---

# 🐚 BASH SCRIPTING

## 🔹 Базовая структура скрипта

```bash
#!/bin/bash

set -e          # остановить выполнение при ошибке
set -u          # ошибка при использовании неинициализированной переменной
set -o pipefail # ошибка если пайп завершился с ошибкой

NAME="DevOps"
echo "Hello $NAME"
```

## 🔹 Переменные и ввод

```bash
read -p "Enter name: " USER   # ввод пользователя
export ENV=prod               # переменная окружения
echo ${VAR:-default}          # значение по умолчанию
```

## 🔹 Условия

```bash
if [ -f file.txt ]; then
  echo "File exists"
else
  echo "File not found"
fi

if [[ $ENV == "prod" ]]; then
  echo "Production mode"
fi
```

## 🔹 Циклы

```bash
for i in {1..5}; do
  echo $i
done

for file in *.log; do
  echo "Processing $file"
done

while read line; do
  echo $line
done < file.txt
```

## 🔹 Функции

```bash
backup() {
  tar -czf backup_$(date +%F).tar.gz $1
}

backup /var/www
```

## 🔹 Работа с кодами возврата

```bash
command
if [ $? -ne 0 ]; then
  echo "Command failed"
fi

command || echo "Error"
```

## 🔹 Trap и обработка сигналов

```bash
trap "echo 'Script interrupted'; exit 1" SIGINT SIGTERM
```

---

# 🚨 PRODUCTION TROUBLESHOOTING CHECKLIST

## 🔹 1. Проверка доступности

```bash
ping HOST
curl -I http://HOST
nc -zv HOST 80
ss -tulnp
```

✔ Проверить DNS
✔ Проверить firewall / security groups
✔ Проверить открытые порты

---

## 🔹 2. Проверка ресурсов сервера

```bash
top
htop
free -h
df -h
df -i
uptime
```

✔ Нет ли OOM?
✔ Не заполнен ли диск?
✔ Нет ли 100% CPU?

---

## 🔹 3. Проверка логов

```bash
tail -f /var/log/syslog
journalctl -xe
journalctl -u SERVICE -f
grep -i error /var/log/*
```

✔ Ошибки приложения
✔ Ошибки systemd
✔ Ошибки ядра (dmesg)

---

## 🔹 4. Docker проверка

```bash
docker ps
docker logs CONTAINER
docker stats
docker inspect CONTAINER
```

✔ Контейнер не перезапускается?
✔ Есть ли OOMKilled?
✔ Healthcheck проходит?

---

## 🔹 5. Kubernetes проверка

```bash
kubectl get pods -A
kubectl describe pod NAME -n NAMESPACE
kubectl logs NAME -n NAMESPACE
kubectl get events -n NAMESPACE
kubectl top pod -n NAMESPACE
```

✔ CrashLoopBackOff?
✔ ImagePullBackOff?
✔ Pending из‑за ресурсов?
✔ Проблемы с PVC?

---

## 🔹 6. Rollback стратегия

```bash
helm history RELEASE -n NAMESPACE
helm rollback RELEASE REVISION -n NAMESPACE
kubectl rollout undo deployment/NAME -n NAMESPACE
```

✔ Быстро откатить релиз
✔ Проверить изменения конфигурации
✔ Проверить CI/CD изменения

---

# ✅ GOLDEN RULES

- Сначала собрать данные, потом менять
- Всегда смотреть логи
- Проверять ресурсы до перезапуска
- Делать rollback вместо hotfix в проде
- Документировать инцидент
