# Инструкция по развертке докер-контейнеров
## Этап №1: Первоначальная настройка
1. Проверить наличие Docker на целевой машине, установить в случае отсутвия.
2. Клонировать файлы с репозитория: `git clone -b master https://github.com/YanaStepanova/AOS_LAB/`
3. Создать сеть контейнеров: `docker network create -d bridge my_network`
  
Дополнительные команды:  
- `docker images` – выводит список всех созданных образов  
- `docker ps` – проверка запущенных контейнеров

## Этап №2: Настройка FTP-сервера
1. Переходим в папку с docker-файлом для ftp с помощью команды `cd`
2. Создаём образ для будущего контейнера: `docker build -t ftp_image .`
3. Создаём контейнер _my_ftp_ на основе имиджа _ftp_image_:  
`docker run -d -v /ftp/:/home/vsftpd -p 22 -p 20:20 -p 21:21 -p 47400-47470:47400-47470 -e FTP_USER=test -e FTP_PASS=test -e PASV_ADDRESS=192.168.0.3 --name my_ftp --net=my_network ftp_image`
5. Чтобы проверить работу контейнера, в адресной строке браузера необходимо ввести ip адрес машины и указать порт, на который проброшен ftp-сервер  

## Этап №2: Настройка nginx
1. Переходим в папку с docker-файлом для web с помощью команды cd
2. Создаём образ для контейнера командой: `docker build -t web_image .`
3. Создаём контейнер _my_nginx_ на основе имиджа _web_image_:
`docker run -d -p 80:80 -p 22 --name my_nginx --net=my_network web_image`
4. Чтобы проверить работу контейнера, в адресной строке браузера достаточно ввести ip адрес машины
## Этап №3: Настройка контейнера с нагрузкой на ftp
1. Переходим в нужный каталог 
2. Создаём имидж для контейнера: `docker build -t ftp-load_image .`
3. Создаём контейнер  _my_ftp-load_ на основе имиджа _ftp-load_image_:
`docker run -d -p 8099:8089 --name my_ftp-load --net=my_network -v /root/test/AOS_LAB/ftp-load/:/mnt/locust ftp-load_image -f /mnt/locust/locustfile.py`
4. Чтобы проверить работу контейнера, в адресной строке браузера необходимо ввести ip адрес машины и указать порт, на который проброшена нагрузка на ftp
## Этап №4: Настройка контейнера с нагрузкой на web
1. Переходим в нужный каталог 
2. Создаём имидж для контейнера: `docker build -t web-load_image .`
3. Создаём контейнер  _my_web-load_ на основе имиджа _web-load_image_
`docker run -d -p 8089:8089 --name my_web-load --net=my_network -v/root/test/AOS_LAB/web-load/:/mnt/locust web-load_image -f /mnt/locust/locustfile.py`
4. Чтобы проверить работу контейнера, в адресной строке браузера необходимо ввести ip адрес машины и указать порт, на который проброшена нагрузка на web
