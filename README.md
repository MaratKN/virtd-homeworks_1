
# Домашнее задание к занятию 4 «Оркестрация группой Docker контейнеров на примере Docker Compose»

### Инструкция к выполению

1. Для выполнения заданий обязательно ознакомьтесь с [инструкцией](https://github.com/netology-code/devops-materials/blob/master/cloudwork.MD) по экономии облачных ресурсов. Это нужно, чтобы не расходовать средства, полученные в результате использования промокода.
2. Практические задачи выполняйте на личной рабочей станции или созданной вами ранее ВМ в облаке.
3. Своё решение к задачам оформите в вашем GitHub репозитории в формате markdown!!!
4. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.

## Задача 1

Сценарий выполнения задачи:
- Установите docker и docker compose plugin на свою linux рабочую станцию или ВМ.
- Если dockerhub недоступен создайте файл /etc/docker/daemon.json с содержимым: ```{"registry-mirrors": ["https://mirror.gcr.io", "https://daocloud.io", "https://c.163.com/", "https://registry.docker-cn.com"]}```
- Зарегистрируйтесь и создайте публичный репозиторий  с именем "custom-nginx" на https://hub.docker.com (ТОЛЬКО ЕСЛИ У ВАС ЕСТЬ ДОСТУП);
- скачайте образ nginx:1.21.1;
- Создайте Dockerfile и реализуйте в нем замену дефолтной индекс-страницы(/usr/share/nginx/html/index.html), на файл index.html с содержимым:
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I will be DevOps Engineer!</h1>
</body>
</html>
```
- Соберите и отправьте созданный образ в свой dockerhub-репозитории c tag 1.0.0 (ТОЛЬКО ЕСЛИ ЕСТЬ ДОСТУП). 
- Предоставьте ответ в виде ссылки на https://hub.docker.com/<username_repo>/custom-nginx/general .

```
sudo docker pull nginx:1.21.1
sudo docker build -t custom-nginx:1.0.0 .
sudo docker tag custom-nginx:1.0.0 maratkn/custom-nginx:1.0.0
sudo docker image ls
sudo docker login
sudo docker push maratkn/custom-nginx:1.0.0
```

![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/1.png)
https://hub.docker.com/repository/docker/maratkn/custom-nginx/general



## Задача 2
1. Запустите ваш образ custom-nginx:1.0.0 командой docker run в соответвии с требованиями:
- имя контейнера "ФИО-custom-nginx-t2"
- контейнер работает в фоне
- контейнер опубликован на порту хост системы 127.0.0.1:8080
2. Не удаляя, переименуйте контейнер в "custom-nginx-t2"
3. Выполните команду ```date +"%d-%m-%Y %T.%N %Z" ; sleep 0.150 ; docker ps ; ss -tlpn | grep 127.0.0.1:8080  ; docker logs custom-nginx-t2 -n1 ; docker exec -it custom-nginx-t2 base64 /usr/share/nginx/html/index.html```
4. Убедитесь с помощью curl или веб браузера, что индекс-страница доступна.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

```
sudo docker run --name KMN-custom-nginx-t2 -p 8080:80 -d maratkn/custom-nginx:1.0.0
sudo docker rename KMN-custom-nginx-t2 custom-nginx-t2
date +"%d-%m-%Y %T.%N %Z" ; sleep 0.150 ; sudo docker ps ; ss -tlpn | grep 127.0.0.1:8080  ; sudo docker logs custom-nginx-t2 -n1 ; sudo docker exec -it custom-nginx-t2 base64 /usr/share/nginx/html/index.html
curl localhost:8080
```
![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/2.png)




## Задача 3
1. Воспользуйтесь docker help или google, чтобы узнать как подключиться к стандартному потоку ввода/вывода/ошибок контейнера "custom-nginx-t2".
2. Подключитесь к контейнеру и нажмите комбинацию Ctrl-C.
3. Выполните ```docker ps -a``` и объясните своими словами почему контейнер остановился.
4. Перезапустите контейнер
5. Зайдите в интерактивный терминал контейнера "custom-nginx-t2" с оболочкой bash.
6. Установите любимый текстовый редактор(vim, nano итд) с помощью apt-get.
7. Отредактируйте файл "/etc/nginx/conf.d/default.conf", заменив порт "listen 80" на "listen 81".
8. Запомните(!) и выполните команду ```nginx -s reload```, а затем внутри контейнера ```curl http://127.0.0.1:80 ; curl http://127.0.0.1:81```.
9. Выйдите из контейнера, набрав в консоли  ```exit``` или Ctrl-D.
10. Проверьте вывод команд: ```ss -tlpn | grep 127.0.0.1:8080``` , ```docker port custom-nginx-t2```, ```curl http://127.0.0.1:8080```. Кратко объясните суть возникшей проблемы.
11. * Это дополнительное, необязательное задание. Попробуйте самостоятельно исправить конфигурацию контейнера, используя доступные источники в интернете. Не изменяйте конфигурацию nginx и не удаляйте контейнер. Останавливать контейнер можно. [пример источника](https://www.baeldung.com/linux/assign-port-docker-container)
12. Удалите запущенный контейнер "custom-nginx-t2", не останавливая его.(воспользуйтесь --help или google)

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

Контейнер в пункте 3 остановился из-за того, что ctrl+c - это стандарный сигнал завершения процесса.
Проблема в пункте 10 в том, что мы изменили порт приложения, которое работает в контейнере, но port-forvarding у нас всё ещё = 80.

```
marat@debian1:~/docker$ sudo docker run -it --name custom-nginx-t2 -p 8080:80 -d maratkn/custom-nginx:1.0.0
marat@debian1:~/docker$ sudo docker exec -it custom-nginx-t2 bash
root@45004a6a5b79:/# apt update && apt install nano
root@45004a6a5b79:/# nano /etc/nginx/conf.d/default.conf
root@45004a6a5b79:/# nginx -s reload
root@45004a6a5b79:/# curl http://127.0.0.1:80
root@45004a6a5b79:/# curl http://127.0.0.1:81
root@45004a6a5b79:/# exit
marat@debian1:~/docker$ ss -tlpn | grep 127.0.0.1:8080
marat@debian1:~/docker$ sudo docker port custom-nginx-t2
marat@debian1:~/docker$ curl http://127.0.0.1:8080
marat@debian1:~/docker$ sudo docker rm custom-nginx-t2 -f
marat@debian1:~/docker$ sudo docker ps -a
```

![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/3.png)
![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/4.png)
![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/5.png)
![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/6.png)


## Задача 4


- Запустите первый контейнер из образа ***centos*** c любым тегом в фоновом режиме, подключив папку  текущий рабочий каталог ```$(pwd)``` на хостовой машине в ```/data``` контейнера, используя ключ -v.
- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив текущий рабочий каталог ```$(pwd)``` в ```/data``` контейнера. 
- Подключитесь к первому контейнеру с помощью ```docker exec``` и создайте текстовый файл любого содержания в ```/data```.
- Добавьте ещё один файл в текущий каталог ```$(pwd)``` на хостовой машине.
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в ```/data``` контейнера.


В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

```
marat@debian1:~/docker$ sudo docker run -it -v $(pwd):/data -d centos &
[1] 16813
marat@debian1:~/docker$ 3be7b33446f8cdd640c64ec16508415b0fde5f5222f1d4aad1c3f3590ee9ce40
[1]+  Done                    sudo docker run -it -v $(pwd):/data -d centos
marat@debian1:~/docker$ sudo docker run -it -v $(pwd):/data -d debian &
[1] 16900
marat@debian1:~/docker$ 78702dc2bf10f75423c74b73b07f0d5e0d8b60a948b4026ecaf39520b7c57fa4
[1]+  Done                    sudo docker run -it -v $(pwd):/data -d debian
marat@debian1:~/docker$ sudo docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS                      PORTS                 NAMES
78702dc2bf10   debian      "bash"                   5 seconds ago    Up 4 seconds                                      busy_rosalind
3be7b33446f8   centos      "/bin/bash"              11 seconds ago   Up 10 seconds                                     boring_franklin
a7eac7ea64ce   mysql:8.0   "docker-entrypoint.s…"   2 months ago     Exited (255) 2 months ago   3306/tcp, 33060/tcp   mysql-instance2
f5b380a98124   mysql:8.0   "docker-entrypoint.s…"   2 months ago     Exited (255) 2 months ago   3306/tcp, 33060/tcp   mysql-instance1
07e8bd40593c   mysql:8.0   "docker-entrypoint.s…"   2 months ago     Exited (1) 2 months ago                           mysql-instance
marat@debian1:~/docker$ sudo docker exec -it 3be7b33446f8 bash -c "echo 'Hello1' > /data/file1.txt"
marat@debian1:~/docker$ echo 'Hello2' > file2.txt
marat@debian1:~/docker$ sudo docker exec -it 78702dc2bf10 bash -c "cat /data/file1.txt" 
Hello1
marat@debian1:~/docker$ sudo docker exec -it 78702dc2bf10 bash -c "cat /data/file2.txt" 
Hello2
```

![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/7.png)




## Задача 5

1. Создайте отдельную директорию(например /tmp/netology/docker/task5) и 2 файла внутри него.
"compose.yaml" с содержимым:
```
version: "3"
services:
  portainer:
    image: portainer/portainer-ce:latest
    network_mode: host
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```
"docker-compose.yaml" с содержимым:
```
version: "3"
services:
  registry:
    image: registry:2
    network_mode: host
    ports:
    - "5000:5000"
```

И выполните команду "docker compose up -d". Какой из файлов был запущен и почему? (подсказка: https://docs.docker.com/compose/compose-application-model/#the-compose-file )

2. Отредактируйте файл compose.yaml так, чтобы были запущенны оба файла. (подсказка: https://docs.docker.com/compose/compose-file/14-include/)

3. Выполните в консоли вашей хостовой ОС необходимые команды чтобы залить образ custom-nginx как custom-nginx:latest в запущенное вами, локальное registry. Дополнительная документация: https://distribution.github.io/distribution/about/deploying/
4. Откройте страницу "https://127.0.0.1:9000" и произведите начальную настройку portainer.(логин и пароль адмнистратора)
5. Откройте страницу "http://127.0.0.1:9000/#!/home", выберите ваше local  окружение. Перейдите на вкладку "stacks" и в "web editor" задеплойте следующий компоуз:

```
version: '3'

services:
  nginx:
    image: 127.0.0.1:5000/custom-nginx
    ports:
      - "9090:80"
```
6. Перейдите на страницу "http://127.0.0.1:9000/#!/2/docker/containers", выберите контейнер с nginx и нажмите на кнопку "inspect". В представлении <> Tree разверните поле "Config" и сделайте скриншот от поля "AppArmorProfile" до "Driver".

7. Удалите любой из манифестов компоуза(например compose.yaml).  Выполните команду "docker compose up -d". Прочитайте warning, объясните суть предупреждения и выполните предложенное действие. Погасите compose-проект ОДНОЙ(обязательно!!) командой.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод, файл compose.yaml , скриншот portainer c задеплоенным компоузом.

---


В последней версии DC приоритет отдаётся compose.yaml

![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/8.png)

Отредактировал compose.yaml
```
version: "3"

include:

  - docker-compose.yaml

services:

  portainer:

    image: portainer/portainer-ce:latest

    network_mode: host

    ports:

      - "9000:9000"

    volumes:

      - /var/run/docker.sock:/var/run/docker.sock
```


![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/9.png)
![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/10.png)
![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/11.png)
![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/12.png)
![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/13.png)
![alt text](https://github.com/MaratKN/virtd-homeworks_1/blob/main/14.png)

После удаления манифеста мы получили предупреждение: сервисы, запущенные через docker compose, отличаются от тех, что находятся в текущем манифесте. Нам предлагают или исправить проблему с файлом или удалить лишние контейнеры, которые отсутствуют в текущем манифесте.

```
 docker compose down --remove-orphans
```

### Правила приема

Домашнее задание выполните в файле readme.md в GitHub-репозитории. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.


