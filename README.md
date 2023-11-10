# Home Assistant docker compose
Home Assistant (https://www.home-assistant.io/) запущенный через docker-compose

### Адрес для доступа к консоли
*http://IP_ADDR_HOST:8123#*

### Команды
#### Пересоздать контейнер
```
docker-compose up --build -d --remove-orphans --force-recreate
```
#### Удалить контейнер
```
docker-compose down --remove-orphans
```
#### Обновить образ
```
docker pull homeassistant/home-assistant

