# 1. Frontend

## 1.1 Grafana dashboard
![dashboard](./resources/Screenshot%20from%202026-01-02%2016-51-47.png)

- Grafana
- Standard docker image
- Port: 3000
- Server: EC2

# 2. Backend
## 2.1 Zabbix

- Zabbix
- Standard docker image
- Port: 8000
- Server: EC2
- DB: PostgreSQL

### 2.1.1 Switchbot
- Connect via Zabbix
- Script called by zabbix for fetch switchbot standard APIs
  - The script is copied  at Zabbix server docker image
  - (Reference) https://qiita.com/d_yan/items/805f50a6f1139fa94215
- Switchbot APIS https://github.com/OpenWonderLabs/SwitchBotAPI

#### 2.1.1.1 Devices
- Living
  - Meter Plus
      - Device name: 温湿度計1
- Study
  - Meter Plus
      - Device name: 温湿度計2
- Bedroom
  - Meter Plus
      - Device name: 温湿度計3
  - Plug Mini
      - Device name: オイルヒーター

## 2.2 Open Weather Map
- Connect via Zabbix standard template
- https://openweathermap.org/api

## 2.3 Tepco billing
- Connect via aws Athena
- Manually uploaded data files are ETL'd by glue and queriy via Athena

# 3. Server 
- aws EC2
- micro
- OS: Amazon Linux




# 4. Diagram
```
+-------------------------------+
|        AWS EC2 (micro)        |
|                               |
|    Zabbix API calls/polls     |
|             |                 |
|             |                 |
|             v                 |
|     +---------------+         |
|     | Zabbix         |        |
|     | (Docker)       |        | 
|     | Port: 8000     |        |
|     +-------+--------+        |
|             |                 |
| 　　　  　　　|                 |
|             v                 |
|     +----------------+        |
|     | Grafana        |        |
|     | (Docker)       |        |
|     | Port: 3000     |        |
|     +-------+--------+        |
|             |                 |
|  　　  　    |                 |
|             v                 |
|        User Browser           |
+-------------------------------+

External systems (outside EC2)
+----------------------+     +-------------------------+     +----------------------------+
| SwitchBot Devices    | --> | SwitchBot Cloud API     | --> | Zabbix (on EC2)            |
| (BLE meters / plugs) |     | (SwitchBot REST API)    |     | (script polls -> metrics)  |
+----------------------+     +-------------------------+     +----------------------------+

+-------------------------+     +----------------------------+
| OpenWeatherMap API      | --> | Zabbix (on EC2)            |
| (weather HTTP API)      |     | (standard template pulls)  |
+-------------------------+     +----------------------------+

+---------------------------------------------------------------+
| Tepco billing pipeline (AWS)                                  |
| Manual CSV upload --> AWS S3 --> AWS Glue (ETL) --> Athena    |
|                                       |                       |
|                                       v                       |
|                           Athena queried by Zabbix / Grafana  |
+---------------------------------------------------------------+

Connections (summary):
- SwitchBot Devices --> SwitchBot Cloud API --> Zabbix (script polls) --> queried by Grafana
- OpenWeatherMap API --> Zabbix (standard template) --> queried by Grafana
- Manual Tepco files --> S3 --> Glue ETL --> Athena --> queried by Grafana
- User Browser --> HTTP:3000 --> Grafana (on EC2)
```
