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
```
#### mini-graph-card
https://github.com/kalkih/mini-graph-card  
Добавление  
```wget https://github.com/kalkih/mini-graph-card/releases/download/v0.11.0/mini-graph-card-bundle.js```  
```mv mini-graph-card-bundle.js /home/DOCKER_DATA/HomeAssistant/hass-config/www/mini-graph-card-bundle.js```  

##### Столбчатый график  
Часовой расход газа  
```
aggregate_func: max
entities:
  - color: var(--primary-color)
    entity: sensor.gas_hourly_single
    group_by: hour
hours_to_show: 48
hour24: true
name: Часовой расход газа(48 часов)
points_per_hour: 1
show:
  graph: bar
  state: true
  extrema: true
  labels: true
type: custom:mini-graph-card
```
##### Дневной расход газа(7 дней)  
```
aggregate_func: max
entities:
  - color: var(--primary-color)
    entity: sensor.gas_daily_single
    group_by: date
    show_state: false
    show_graph: true
hours_to_show: 168
action: none
hour24: true
name: Дневной расход газа(7 дней)
points_per_hour: 0.04167
show:
  graph: bar
  state: true
  extrema: true
  labels: true
type: custom:mini-graph-card

```
