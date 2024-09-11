# S21_SimpleDocker

## Part1. Ready-made docker

* Возьмем официальный docker-образ с nginx и выкачаем его при помощи команды `docker pull` 
![1.docker_pull_nginx](img/1.docker_pull_nginx.png)

* Далее удостоверимся в наличии образа через команду `docker images` 
![2.docker_images](img/2.docker_images.png)

* Наконец, запустим docker-образ через команду `docker run -d [image_id|repository]` 
![3.docker_run_-d](img/3.docker_run_-d.png)
```
-d: это флаг, который указывает Docker на запуск контейнера в фоновом режиме (detached mode). Это означает, что контейнер будет работать в фоновом режиме, и командная строка будет освобождена для дальнейшего использования.
```

* Удостоверимся, что контейнер успешно запустился через команду `docker ps` 
![4.docker_ps](img/4.docker_ps.png)

* Теперь посмотрим информацию о контейнере через команду `docker inspect [container_id|container_name]` 
![5.docker_inspect](img/5.docker_inspect.png)

* Выведем размер контейнера  
![6.docker_inspect_size](img/6.docker_inspect_size.png)

* А теперь - список замапленных портов  
![8.docker_inspect_ports](img/8.docker_inspect_ports.png)

* И, наконец, IP контейнера  
![7.docker_inspect_ip](img/7.docker_inspect_ip.png)

* Остановим docker-образ командой `docker stop [container_id|container_name]` и проверим, что образ успешно остановился через уже знакомую команду `docker ps` 
![9.docker_stop_and_docker_ps](img/9.docker_stop_and_docker_ps.png)

* Теперь запустим docker-образ с портами 80:80 и 443:443 чере команду `docker run` 
![10.docker_run_80_443_and_ps](img/10.docker_run_80_443_and_ps.png)

* Удостоверимся, что все работает, открыв в браузере страницу по адресу `localhost` 
![11.browser_localhost](img/11.browser_localhost.png)

* Наконец, перезапустим контейнер через команду `docker restart [container_id|container_name]` и проверим, что контейнер снова запустился командой `docker ps` 
![12.docker_restart_and_ps](img/12.docker_restart_and_ps.png)

# Part2. Operations with container

* Для начала прочтем конфигурационный файл `nginx.conf` внутри docker-контейнера через команду `docker exec` 
![13.docker_exec_nginx](img/13.docker_exec_nginx.png)

* Теперь создадим локальный файл `nginx.conf` при помощи команды `touch nginx.conf` и настроем в нем выдачу страницы-статуса сервера по пути `/status` 
![14.nginx.conf](img/14.nginx.conf.png)

* Наконец, перенесем созданный файл внутрь docker-образа командой `docker cp` 
![15.docker_cp_nginx](img/15.docker_cp_nginx.png)

* И перезапустим nginx внутри docker-образа командой `docker exec [container_id|container_name] nginx -s reload` 
![16.docker_exec_nginx_reload](img/16docker_exec_nginx_reload.png)

* Убедимся, что все работает, проверив страницу по адресу `localhost/status` 
![17.browser_localhost_status](img/17.browser_localhost_status.png)

* Теперь экспортируем наш контейнер в файл `container.tar` командой `docker export` 
![18.docker_export](img/18.docker_export.png)

* Затем удалим образ командой `docker rmi -f [image_id|repository]`, не удаляя перед этим контейнеры. После чего удалим остановленный контейнер командой `docker rm [container_id|container_name]` 
![19.docker_stop_and_rm](img/19.docker_stop_and_rm.png)

* Теперь импортируем контейнер обратно командой `docker import` и запустим импортированный контейнер уже знакомой командой `dicker run` 
![20.docker_import_and_run](img/20.docker_import_and_run.png)

* Наконец проверим, что по адресу `localhost/status` выдается страничка со статусом сервера nginx 
![21.browser_localhost_status2](img/21.browser_localhost_status2.png)

## Part3. Mini web server

* Чтобы создать свой мини веб-сервер, необходимо создать .c файл, в котором будет описана логика сервера (в нашем случае - вывод сообщения `Hello World!`), а также конфиг `nginx.conf`, который будет проксировать все запросы с порта 81 на порт 127.0.0.1:8080 
![22.server](img/22.server.png) 
![23.nginx.conf2](img/23.nginx.conf2.png)

* Теперь выкачаем новый docker-образ и на его основе запустим новый контейнер 
![24.docker_run_81](img/24.docker_run_81.png)

* После перекинем конфиг и логику сервера в новый контейнер 
![25.docker_cp_server_and_nginx](img/25.docker_cp_server_and_nginx.png)

* Затем установим требуемые утилиты для запуска мини веб-сервера на FastCGI, в частности `spawn-fcgi` и `libfcgi-dev` 
![26.in_docker_updates](img/26.in_docker_updates.png)

* Наконец скомпилируем и запустим наш мини веб-сервер через команду `spawn-fcgi` на порту 8080 
![27.in_docker_zapusk_server](img/27.in_docker_zapusk_server.png)

* Чтобы удостовериться, что все работает корректно, проверим, что в браузере по адресу `localhost:81` отдается написанная нами страница 
![28.browser_localhost_81](img/28.browser_localhost_81.png)

## Part4. Your own docker

* Напишем свой docker-образ, который собирает исходники 3-й части, запускает на порту `80`, после копирует внутрь написанный нами `nginx.conf` и, наконец, запускает `nginx` (ниже приведены файлы `run.sh` и `Dockerfile`, файлы `nginx.conf` и `server.c` остаются с 3-й части)

![30.part4_runsh](img/30.part4_runsh.png)   
![29.part4_dockerfile](img/29.part4_dockerfile.png)  

* Соберем написанный docker-образ через команду `docker build`, при этом указав имя и тэг нашего контейнера  
![31.part4_docker_build](img/31.part4_docker_build.png)  

* Теперь удостоверимся, что все собралось, проверив наличие соответствующего образа командой `docker images`  
![32.part4_docker_images](img/32.part4_docker_images.png)  

* После запустим собранный docker-образ с мапингом порта `81` на порт `80` локальной машины, а также мапингом папки `./nginx` внутрь контейнера по адресу конфигурационных файлов nginx'а, и проверим, что страничка написанного сервера по адресу 
![33.part4_docker_run_8081](img/33.part4_docker_run_8081.png)

* Теперь добавим в файл `nginx.conf` проксирование странички `/status`, по которой необходимо отдавать статус сервера `nginx  
![35.part4_nginx](img/35.part4_nginx.png)

* Теперь перезапустим `nginx` в своем docker-образе командой `nginx -s reload`  
![36.part4_nginx_reload](img/36.part4_nginx_reload.png)

* Наконец, проверим, что по адресу `localhost/status` выдается страничка со тсатусом сервера `nginx`  
![37.part4_browser_localhost_status](img/37.part4_browser_localhost_status.png)


## Part5. Dockle

```
!!Примечание!!
Перед выполнением данного шага необходимо установить утилиту [dockle], инструкция по установке [https://github.com/goodwithtech/dockle], если машина не видит утилиту [https://github.com/aquasecurity/trivy/issues/2432], также рекомендую добавить своего пользователя в группу [docker]
```

* Просканируем docker-образ из предыдущего задания на предмет наличия ошибок командой `dockle [image_id|repository]`  
![38.part5_dockle](img/38.part5_dockle.png)

* Исправим образ так, чтобы при проверке через dockle не было ошибок и предупреждений.

 - CIS-DI-0010 - свидетельствует о том, что не нужно хранить какие-либо "секреты" в докерфайле
 - DKL-DI-0005 - предупреждение говорит о том, что должны очистить кэши apt-get после установки пакетов
 - CIS-DI-0001 - предупреждение говорит о том, что должны создать пользователя для контейнера и не использовать пользователя root.
 - CIS-DI-0005 - предупреждение говорит о том, что вы должны включить доверие к содержимому для Docker.
 - CIS-DI-0006 - то предупреждение говорит о том, что должны добавить инструкцию HEALTHCHECK в ваш Dockerfile. Это позволяет Docker проверять, работает ли приложение корректно
 - CIS-DI-0008 - предупреждение указывает на то, что в Docker образе могут быть файлы с установленными разрешениями setuid или setgid

* Далее исправим конфигурационные файлы docker-образа так, чтобы при проверке через утилиту `dockle` не возникало ошибок и предупреждений (для Part5 я создал отдельный контейнер с тэгом `part5`, куда подгрузил измененные конфиги)
![39.part5_dockerfile](img/39.part5_dockerfile.png)  
![40.part5_dockle_new](img/40.part5_dockle_new.png)

## Part6. Basic Docker Compose

```
!!Примечание!!
Перед выполнением данного шага необходимо установить утилиту [docker-compose], инструкция по установке [https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04]
```

* Для начала остановим все запущенные контейнеры командой `docker stop`  
* Затем изменим конфигурационные файлы (их можно найти в папке `src/part6`)

* Теперь сбилдим контейнер командой `docker-compose build`
![part6_build](img/part6_build.png)  

* После необходимо поднять сбилженный контейнер командой `docker compose up`
![part6_up](img/part6_up.png)  

* В завершение насладимся плодами своей усердной работы, удостоверившись, что по адресу `localhost` отдается страничка с надписью `Hello World!`
![part6_localhost](img/43.part6_localhost.png)  
