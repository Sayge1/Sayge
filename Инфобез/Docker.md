
> [!NOTE] Основные команды
> sudo docker ps -q - вывести активные контейнеры
> sudo docker stop container_id - выключить контейнер с именем container_id

`docker pull имя образа` – скачивание образа
`docker ps` – используется для просмотра активных контейнеров
`docker run * -v /home/mount/data:/var/lib/mysql/data` – примонтировать директорию на хосте в docker; в Docker Compose сначала указывается docker путь, потом хост путь
`docker network ls` – показать сети docker
`docker network inspect [имя сети]` – посмотреть конфигурацию сети
`docker rm _[имя]_` – удалить контейнер  
`docker rmi _[имя]_` – удалить image
`docker-compose down` остановка docker-compose контейнеров  
`docker rmi $(docker images -q)` – удаление образов  
`docker stop $(docker ps -a -q)`– остановка контейнеров  
`docker rm $(docker ps -a -q)`  – удаление остановленных контейнеров  
`docker container prune` – новая функция удаления контейнеров  
`docker network prune`  – удаление сетей  
`docker system prune` – полный clean up контейнеров
