## Part 1. Готовый докер

- **Взяли официальный докер образ с nginx и выкачали его при помощи `docker pull`**
![docker_pull](./screenshots/docker_pull.png)

- **Проверили наличие докер образа через `docker images`**
![docker_images](./screenshots/docker_images.png)

- **Запустили докер образ через `docker run -d [image_id|repository]`**

> **-d:** это флаг, который указывает Docker на запуск контейнера в фоновом режиме (detached mode). Это означает, что контейнер будет работать в фоновом режиме, и командная строка будет освобождена для дальнейшего использования.
>

![docker_run](./screenshots/docker_run.png)

- **Проверили, что образ запустился через `docker ps`**
![docker_ps](./screenshots/docker_ps.png)

>Команда `docker ps` используется для вывода списка запущенных контейнеров Docker.  
>При запуске команды `docker ps` без дополнительных флагов будут отображены только запущенные контейнеры в текущий момент времени.
>

- **Посмотрели информацию о контейнере через `docker inspect [container_id|container_name]`**
![docker_inspect](./screenshots/docker_inspect.png)


- размер контейнера  
![size2](./screenshots/size.png)

- список замапленных портов  
![ports](./screenshots/ports.png)

>Маппинг нужен для того, чтобы все запросы, проходящие через порт хоста, перенаправлялись в Docker-контейнер. Другими словами, сопоставление портов делает процессы внутри контейнера доступными извне.
>

- ip контейнера
![ip](./screenshots/ip.png)

- **Остановили докер образ через `docker stop [container_id|container_name]` и проверили, что образ остановился через `docker ps`**
![docker_stop_ps](./screenshots/docker_stop_ps.png)

- **Запустили докер с портами 80 и 443 в контейнере, замапленными на такие же порты на локальной машине, через команду `run`**
![80_443](./screenshots/80_443.png)

- **Проверили, что в браузере по адресу *localhost:80* доступна стартовая страница `nginx`**
![check](./screenshots/check.png)

- **Перезапустили докер контейнер через `docker restart [container_id|container_name]` и проверили, что контейнер запустился**
![docker_restart_ps](./screenshots/docker_restart_ps.png)

---

## Part 2. Операции с контейнером

- **Прочитали конфигурационный файл `nginx.conf` внутри докер контейнера через команду `exec`**
![nginx_conf](./screenshots/2.1.png)

- **Создали на локальной машине файл `nginx.conf`**
`vim nginx.conf`

- **Настроили в нем по пути `/status` отдачу страницы статуса сервера `nginx`**
![vim_nginx](./screenshots/2.2.png)

- **Скопировали созданный файл `nginx.conf` внутрь докер образа через команду `docker cp` и перезапустили `nginx` внутри докер образа через команду `exec`**
![docker_cp](./screenshots/2.3.png)

- **Проверили, что по адресу `localhost:80/status` отдается страничка со статусом сервера `nginx`**
![curl](./screenshots/2.4.png)
![curl_br](./screenshots/2.4.1.png)

- **Экспортировали контейнер в файл `container.tar` через команду `export`и остановили контейнер**
![export_stop](./screenshots/2.5.png)

- **Удалили образ через `docker rmi [image_id|repository]`, не удаляя перед этим контейнеры**
![rmi](./screenshots/2.6.png)

- **Удалили остановленный контейнер**
![rm](./screenshots/2.7.png)

- **Импортировали контейнер обратно через команду `import`**
![import](./screenshots/2.8.png)

- **Запустили импортированный контейнер**
![run_again](./screenshots/2.9.png)

- **Проверили, что по адресу `localhost:80/status` отдается страничка со статусом сервера `nginx`**
![new_status](./screenshots/2.10.png)

---

## Part 3. Мини веб-сервер

- **Написали мини сервер на C и FastCgi, который будет возвращать простейшую страничку с надписью Hello World!**
![server_c](./screenshots/3.1.png)


- **Написали свой nginx.conf, который будет проксировать все запросы с 81 порта на 127.0.0.1:8080**
![nginx_81](./screenshots/3.2.png)

	- Скопировали созданный nginx.conf и мини сервер в контейнер и зашли в него. 
	![cp_it](./screenshots/3.3.png)
    ![cp_it](./screenshots/3.3.1.png)
	
	> **-y** опция команды `apt-get` указывает на автоматическое подтверждение установки пакетов без запроса подтверждения от пользователя. Это полезно при автоматической установке пакетов в скриптах или в среде контейнеров, где интерактивное взаимодействие с пользователем невозможно.
	>

	- Установили требуемые ПО
	![update](./screenshots/3.4.png)
	![install](./screenshots/3.4.1.png)
	
	- Скомпилировали и **запустили написанный мини сервер через spawn-fcgi на порту 8080**
	![compilation](./screenshots/3.4.2.png)
	
- **Проверили, что в браузере по localhost:81 отдается написанная вами страничка**
![81](./screenshots/3.5.png)	

- **Положили файл nginx.conf по пути ./nginx/nginx.conf**
`docker cp nginx.conf beautiful_taussig:/etc/nginx/nginx.conf`
![81](./screenshots/3.6.png)

---

## Part 4. Свой докер

- **Написали свой докер образ, который:**

	- собирает исходники мини сервера на FastCgi из Части 3
	- запускает его на 8080 порту
	- копирует внутрь образа написанный ./nginx/nginx.conf
	- запускает nginx.
	![dockerfile](./screenshots/4.1.png)
	![run](./screenshots/4.2.png)	

- **Собрали написанный докер образ через docker build при этом указав имя и тег**
	![build](./screenshots/4.3.png)	
	
- **Проверили через docker images, что все собралось корректно**
	![images](./screenshots/4.4.png)	
	
- **Запустили собранный докер образ с маппингом 81 порта на 80 на локальной машине и маппингом папки ./nginx внутрь контейнера по адресу, где лежат конфигурационные файлы nginx'а (см. Часть 2)**
	![81_80](./screenshots/4.5.png)
    ![81_80](./screenshots/4.6.png)
	
	> - **-p 81:80** - это опция для маппинга портов. Она указывает, что порт 80 на локальной машине будет проксироваться на порт 81 внутри контейнера.
	> - **-v** - это опция для маппинга папки. Она указывает, что текущая папка `/Users/tanyakireeva/Desktop/DO5_SimpleDocker-1/src/04/nginx.conf` на локальной машине будет монтироваться в путь `/etc/nginx/nginx.conf` внутри контейнера.
	>

    - **Проверили, что по localhost:80 доступна страничка написанного мини сервера**
	![localhost](./screenshots/4.7.png)
	
- **Дописали в ./nginx/nginx.conf проксирование странички /status, по которой надо отдавать статус сервера nginx**
	![root_nginx_conf](./screenshots/4.8.png)

- **Перезапустили докер образ**
	![reload_nginx](./screenshots/4.9.png)

- **Проверили, что теперь по localhost:80/status отдается страничка со статусом nginx**
	![curl_status](./screenshots/4.10.png)
	
---

## Part 5. Dockle

- **Просканировали образ из предыдущего задания через `dockle [image_id|repository]`**
	![dockle](./screenshots/5.1.png)

- **Исправили образ так, чтобы при проверке через dockle не было ошибок и предупреждений**
	![new_dockle](./screenshots/5.2.png)
	
---

## Part 6. Базовый Docker Compose

- **Написали файл docker-compose.yml, с помощью которого:**

	- Подняли докер контейнер из Части 5 (он должен работать в локальной сети, т.е. не нужно использовать инструкцию EXPOSE и мапить порты на локальную машину)
		
	- Подняли докер контейнер с nginx, который будет проксировать все запросы с 8080 порта на 81 порт первого контейнера
	![last_nginx](./screenshots/6.1.png)
	![yml](./screenshots/6.2.png)
	
		- Изменили скрипт для запуска и перезагрузки nginx. Сделали так, чтобы контейнер пребывал в запущенном состоянии. 
	![last_run](./screenshots/6.3.png)

- **Остановили все запущенные контейнеры**
	`docker-compose down`
	![stop_all](./screenshots/6.4.1.png)

- **Собрали и запустили проект с помощью команд `docker-compose build` и `docker-compose up`**
	![compose_build](./screenshots/6.4.png)

- **Проверили, что в браузере по *localhost:80* отдается написанная вами страничка, как и ранее**
	![browser_80](./screenshots/6.5.png)
	![term_80](./screenshots/6.6.png)