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
### Датчик присутствия на основе BLE
Работает на устройствах имеющих модуль bluetooh, например KNOT.  
По [мотивам](https://help.mikrotik.com/docs/display/ROS/Bluetooth+tag-tracking+using+MQTT+and+ThingsBoard#BluetoothtagtrackingusingMQTTandThingsBoard-Script).
```
# Требуется  пакет: iot

################################ Configuration ################################
# Имя трекера MQTT (должен быть настроен) для публикации
:local broker "mqtt.broker"

# MQTT тема, под которой будут публковаться сообщения
#:local topic "v1/gateway/telemetry"
:local topic "stat/IoT-KNOT/KEY"

# POSIX regex для фильтрации рекламы Bluetooth по адресу. Например "^BC:33:AC"
# что означает только три октета с начала.
# Для отключения фильтра укажите ""
:local addressRegex "D1:00:01:02:03:04"
#:local addressRegex ""

# POSIX regex для фильтрации рекламы Bluetooth на основе данных based on their data. Использование 
# то же самое как 'addressRegex'.
:local advertisingDataRegex ""

# Фильтр по уровню сигнала. Например -40 означает фильтровать рекламу Bluetooth
# уровень сигнала выше чем -40dBm.
# Для отключения этого фильтра установите ""
:local rssiThreshold ""

# Наименование KNOT.
:local gwName "KNOT"

################################## Bluetooth ##################################

:global makeRecord do={
    :local jsonStr "{\"ts\":$ts,\"values\":{\"reporter\":\"$gwName\",\"rssi\":$rssi}}"
    :return $jsonStr
}   

# array of record strings collected for each advertising MAC address
:global macRecords [:toarray ""]

# process advertisements and update macRecords
:local advertisements [/iot bluetooth scanners advertisements print detail as-value where \
address ~ $addressRegex and \
data ~ $advertisingDataRegex and \
rssi > $rssiThreshold]

:if ([:len ($advertisements)] != 0) do={
  :put ("stat/IoT-O142-KNOT/KEYTAICY ON")
:log info "  Send mqtt message stat/IoT-O142-KNOT/KEY ON";
  /iot mqtt publish broker="$broker" topic="$topic" message=ON}
  /iot/bluetooth/scanners/advertisements clear
}
```
В Home Assistant добавляем   
```
  binary_sensor:
    name: "KEY"
    unique_id: "KEY"
    state_topic: "stat/IoT-O142-KNOT/KEY"
    off_delay: 300
```
что означает при появлении сообщения установить сенсор в состояние ON и через 5 минут (300 с.) перевести в OFF.  

