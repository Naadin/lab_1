# lab_1

**ОС:** Windows 11 |
**Терминал:** Windows PowerShell |
**Версия Docker:** 28.0.1

В качестве базового образа был выбран `nginx:alpine`.

## Задания

### 1) Версии и теги

* **Определим версии клиента и демона Docker.**
  
```bash
docker version
```

![]()

* **Запустим выбранный образ в конкретном теге(`nginx:alpine`)**

```bash
docker run -d --name test-nginx nginx:alpine
```

![]()

[Запуск конкретного тега](https://github.com/tiranousor/os-modern-docker-course/blob/main/4-course/docker-and-containers/lectures/02_commands.md)

### 2) Первый запуск сервиса (detached)

* Запускаем контейнер в фоновом режиме на порту 8080, потом перезапуск на порту 1111

```bash
#запуск на порту 8080
docker run -d --name lab-web-naadin -p 8080:80 nginx:alpine
```
![]()

```bash
#запуск на порту 1111
docker run -d --name lab-web-naadin -p 1111:80 nginx:alpine
```
![]()

* Работающие контейнеры
![]()

[Проброс портов](https://github.com/tiranousor/os-modern-docker-course/blob/main/4-course/docker-and-containers/lectures/03_docker_run.md#3-%D0%BF%D1%80%D0%BE%D0%B1%D1%80%D0%BE%D1%81-%D0%BF%D0%BE%D1%80%D1%82%D0%BE%D0%B2--p), [именование контейнеров](https://github.com/tiranousor/os-modern-docker-course/blob/main/4-course/docker-and-containers/lectures/03_docker_run.md#4-%D0%B8%D0%BC%D0%B5%D0%BD%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D0%BA%D0%BE%D0%BD%D1%82%D0%B5%D0%B9%D0%BD%D0%B5%D1%80%D0%BE%D0%B2---name)

### 3) Том (bind‑mount): сайт/данные из папки хоста
* Создание папки на хосте с тестовым файлом
  
```bash
mkdir site

"naadin" | Set-Content .\site\index.html
```

* Перезапуск `lab-web-naadin`, смонтировав папку как `read‑only`
```bash
#монтирование папки через :ro
docker run -d --name lab-web-naadin -p 8080:80 -v "${PWD}\site:/usr/share/nginx/html:ro" nginx:alpine 
```

![]()

* Редактирование текста
```bash
"anin" | Set-Content .\site\index.html
```

![]()

* Почему данные переживут удаление контейнера?

  Потому, что `site` находится на хосте, а не внутри контейнера, поэтому данные переживут удаление контейнера.

[Монтирование тома](https://github.com/tiranousor/os-modern-docker-course/blob/main/4-course/docker-and-containers/lectures/03_docker_run.md#5-%D1%82%D0%BE%D0%BC-%D0%B4%D0%BB%D1%8F-%D1%85%D1%80%D0%B0%D0%BD%D0%B5%D0%BD%D0%B8%D1%8F-%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85--v)

### 4) Интерактив / exec

* Вход внутрь работающего контейнера lab-web-naadin интерактивно
```bash
docker exec -it lab-web-naadin /bin/sh

#переходим в каталог со статикой
cd /usr/share/nginx/html

#просмотр листинга каталога
ls -la
```
![]()

* Создание файла при :ro

```bash
touch anin.txt
```

![]()
* Удаление :ro 
  
![]()

* Файл на хосте после режима RW

![]()

[Интерактивный режим](https://github.com/tiranousor/os-modern-docker-course/blob/main/4-course/docker-and-containers/lectures/02_commands.md#%D1%88%D0%B0%D0%B3-3-%D0%B2%D1%8B%D0%BF%D0%BE%D0%BB%D0%BD%D0%B5%D0%BD%D0%B8%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-%D0%B2-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%D1%8E%D1%89%D0%B5%D0%BC-%D0%BA%D0%BE%D0%BD%D1%82%D0%B5%D0%B9%D0%BD%D0%B5%D1%80%D0%B5-docker-exec)

### 5) Логи и attach

```bash
#Журнал контейнера
docker logs --tail 10 lab-web-naadin
```

![]()

```bash
#прикрепление к основному процессу контейнера с помощью attach
docker attach lab-web-naadin
```

`Ctrl + P` + `Ctrl + Q` - отключиться от контейнера, не останавливая его. 
`Ctrl + C` - остановит контейнер.

[Получение логов](https://github.com/tiranousor/os-modern-docker-course/blob/main/4-course/docker-and-containers/lectures/03_docker_run.md#8-%D0%BB%D0%BE%D0%B3%D0%B8-%D0%BA%D0%BE%D0%BD%D1%82%D0%B5%D0%B9%D0%BD%D0%B5%D1%80%D0%B0-docker-logs), [прикрепление и открепление от контейнера](https://github.com/tiranousor/os-modern-docker-course/blob/main/4-course/docker-and-containers/lectures/02_commands.md#10-docker-attach-%D0%B8--d-%D0%BD%D0%B0-%D0%BF%D1%80%D0%B8%D0%BC%D0%B5%D1%80%D0%B5-%D0%B0%D0%BD%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0-%D1%82%D0%B5%D0%BA%D1%81%D1%82%D0%B0)

### 6) Краткоживущий процесс

```bash
#запуск контейнера с текстом "pervaya"
docker run --name naadin nginx:alpine echo
