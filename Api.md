  API Documentation.md 

Документация по API
===================

Документ описывает порядок взаимодействия с API агента, предназначенного для сбора данных о безопасности, состоянии хоста и зарегистрированных событиях. API обеспечивает доступ к информации о событиях безопасности, таких как изменения файлов, подключение устройств или попытки несанкционированного доступа.

Описание API
------------

API представляет собой интерфейс для взаимодействия сервера с агентом — программным обеспечением, установленным на устройстве (хосте). Агент осуществляет мониторинг и сбор данных о состоянии устройства. API функционирует по протоколу HTTPS и доступен по адресу `https://{agent-host}:65272`, где `{agent-host}` — адрес устройства с установленным агентом (например, `node01.apk.vendor.org`).

Функциональность API включает:

*   Запрос списка событий безопасности с поддержкой ограничения количества возвращаемых событий.

Основной endpoint
-----------------

### Получение событий (`/v1.0/events?limit=50`)

**Назначение**: Возвращает список событий безопасности, зарегистрированных агентом. Поддерживается ограничение количества возвращаемых событий (максимум 10,000).

**Метод**: GET-запрос на `/v1.0/events`.

**Параметры**:

*   `limit` (необязательный): Количество событий в ответе (по умолчанию 10, минимум 1, максимум 10,000).

**Пример ответа**:

    {
      "events_remaining": 900,
      "events": [
        {
          "dt": "2025-05-14T00:12:00Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 1,
          "description": "file_integrity: path=/etc/ssh/sshd_config change_type=modified action=changed"
        },
        {
          "dt": "2025-05-14T00:13:00Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 5,
          "description": "storage: name=sda model=Samsung_SSD_870_EVO serial=S123456789 action=added"
        }
      ]
    }
    

**Описание полей**:

*   `events_remaining`: Количество событий, оставшихся в базе агента (например, 900).
*   `events`: Список событий, где каждое событие включает:
    *   `dt`: Время создания события в формате ISO 8601 (например, `2025-05-14T00:12:00Z`).
    *   `alpha`: Идентификатор юридического лица.
    *   `omega`: Идентификатор сорма.
    *   `event_id`: Идентификатор категории события (число от 1 до 21, см. таблицу категорий).
    *   `description`: Строковое описание события в формате `категория: ключ=значение ключ=значение ...`.

**Возможные ошибки**:

*   `400`: Некорректное значение параметра `limit` (меньше 1 или больше 10,000).
*   `401`: Проблема с клиентским сертификатом.
*   `404`: Метод не найден.
*   `500`: Внутренняя ошибка агента.

Категории событий
-----------------

Вот таблица в формате Markdown (MD):

| ID  | Категория                | Описание                                      |
|-----|--------------------------|-----------------------------------------------|
| 1   | file_integrity          | Изменения файлов                              |
| 2   | configuration            | Изменения конфигураций                        |
| 3   | executable               | Запуск программ                               |
| 4   | device_connection        | Подключение устройств                         |
| 5   | storage                  | События, связанные с дисками                  |
| 6   | command_execution        | Выполнение команд                             |
| 7   | shell                    | Действия в командной строке                   |
| 8   | package_management       | Установка или удаление пакетов                |
| 9   | account_management       | Управление учетными записями                  |
| 10  | server_state             | Перезагрузки и изменения состояния сервера    |
| 11  | logging                  | Записи в системных логах                      |
| 12  | service_state            | Состояние служб                               |
| 13  | firewall                 | Изменения правил межсетевого экрана           |
| 14  | malware                  | Обнаружение вредоносного ПО                   |
| 15  | authentication           | Попытки аутентификации                        |
| 16  | physical_security        | Физическое вмешательство (например, вскрытие корпуса) |
| 17  | usb                      | Подключение USB-устройств                     |
| 18  | audit                    | Аудит системы                                 |
| 19  | network_activity         | Сетевая активность                            |
| 20  | interface                | Изменения сетевых интерфейсов                 |
| 21  | session                  | Сессии (например, SSH)                       |

**Примечание:** В вашем исходном списке для пункта 21 (session) не было описания. Я добавил наиболее логичное и распространённое в контексте безопасности — «Управление сессиями пользователей». Если требуется иное описание, его легко можно заменить.

Примеры событий
---------------

1.  **Изменение файла** (`event_id: 1`):
    
        {
          "dt": "2025-05-14T00:12:00Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 1,
          "description": "file_integrity: path=/etc/ssh/sshd_config change_type=modified action=changed"
        }
        
    
2.  **Изменение конфигурации** (`event_id: 2`):
    
        {
          "dt": "2025-05-14T00:12:01Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 2,
          "description": "configuration: path=/etc/nginx/nginx.conf change_type=modified action=changed line='# user www-data;'"
        }
        
    
3.  **Запуск программы** (`event_id: 3`):
    
        {
          "dt": "2025-05-14T00:12:02Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 3,
          "description": "executable: path=/usr/bin/python3 action=executed"
        }
        
    
4.  **Подключение устройства** (`event_id: 4`):
    
        {
          "dt": "2025-05-14T00:12:03Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 4,
          "description": "device_connection: name=HID_Mouse type=usb action=added"
        }
        
    
5.  **Событие с диском** (`event_id: 5`):
    
        {
          "dt": "2025-05-14T00:13:00Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 5,
          "description": "storage: name=sda model=Samsung_SSD_870_EVO serial=S123456789 action=added"
        }
        
    
6.  **Выполнение команды** (`event_id: 6`):
    
        {
          "dt": "2025-05-14T00:15:00Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 6,
          "description": "command_execution: text=dpkg_-l interpreter=/bin/bash action=executed"
        }
        
    
7.  **Действие в командной строке** (`event_id: 7`):
    
        {
          "dt": "2025-05-14T00:15:01Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 7,
          "description": "shell: command=whoami action=executed"
        }
        
    
8.  **Управление пакетами** (`event_id: 8`):
    
        {
          "dt": "2025-05-14T00:15:02Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 8,
          "description": "package_management: package=nginx version=1.18.0 action=installed"
        }
        
    
9.  **Управление учетными записями** (`event_id: 9`):
    
        {
          "dt": "2025-05-14T00:16:00Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 9,
          "description": "account_management: username=admin uid=1000 gid=1000 home_dir=/home/admin shell=/bin/zsh action=added"
        }
        
    
10.  **Состояние сервера** (`event_id: 10`):
    
        {
          "dt": "2025-05-14T00:17:00Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 10,
          "description": "server_state: previous_time=2025-05-13T10:00:00Z current_time=2025-05-14T00:00:00Z action=detected"
        }
        
    
11.  **Логи** (`event_id: 11`):
    
        {
          "dt": "2025-05-14T00:18:00Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 11,
          "description": "logging: file=/var/log/syslog service=rsyslog event_type=TEST last_modified=2025-05-14T00:11:00Z action=detected msg='This is a log message'"
        }
        
    
12.  **Состояние службы** (`event_id: 12`):
    
        {
          "dt": "2025-05-14T00:18:01Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 12,
          "description": "service_state: service=nginx state=running action=detected"
        }
        
    
13.  **Межсетевой экран** (`event_id: 13`):
    
        {
          "dt": "2025-05-14T00:18:02Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 13,
          "description": "firewall: rule='-A INPUT -p tcp --dport 80 -j ACCEPT' action=added"
        }
        
    
14.  **Вредоносное ПО** (`event_id: 14`):
    
        {
          "dt": "2025-05-14T00:18:03Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 14,
          "description": "malware: file=/tmp/malware.bin type=hash action=detected"
        }
        
    
15.  **Аутентификация** (`event_id: 15`):
    
        {
          "dt": "2025-05-14T00:20:00Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 15,
          "description": "authentication: local_ip=192.168.1.100 local_port=12345 remote_ip=192.168.1.1 remote_port=22 action=failed"
        }
        
    
16.  **Физическая безопасность** (`event_id: 16`):
    
        {
          "dt": "2025-05-14T00:14:00Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 16,
          "description": "physical_security: interface=SPI state=open action=detected"
        }
        
    
17.  **Подключение USB** (`event_id: 17`):
    
        {
          "dt": "2025-05-14T00:13:01Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 17,
          "description": "usb: bus=001 device=002 id=1234:5678 description=SanDisk_Ultra_USB_3.0 action=added"
        }
        
    
18.  **Аудит системы** (`event_id: 18`):
    
        {
          "dt": "2025-05-14T00:18:04Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 18,
          "description": "audit: type=configuration_check result=passed action=detected"
        }
        
    
19.  **Сетевая активность** (`event_id: 19`):
    
        {
          "dt": "2025-05-14T00:19:00Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 19,
          "description": "network_activity: local_ip=192.168.1.100 local_port=80 remote_ip=10.0.0.1 remote_port=443 interface=eth0 protocol=tcp state=ESTABLISHED action=detected"
        }
        
    
20.  **Изменение сетевого интерфейса** (`event_id: 20`):
    
        {
          "dt": "2025-05-14T00:18:05Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 20,
          "description": "interface: interface=eth0 state=up action=detected"
        }
        
    
21.  **Сессия** (`event_id: 21`):
    
        {
          "dt": "2025-05-14T00:18:06Z",
          "alpha": 1,
          "omega": 1,
          "event_id": 21,
          "description": "session: user=admin state=started action=detected"
        }
        
    

Принципы работы
---------------

1.  **Агент**: Программное обеспечение, установленное на хосте, осуществляющее мониторинг безопасности и сбор данных о системе, файлах, устройствах и сетевой активности.
2.  **API**: Обеспечивает доступ к данным агента, включая запрос событий.
3.  **Сертификаты**: Для доступа к API требуется действующий клиентский сертификат. При его отсутствии возвращается ошибка 401.
4.  **Формат данных**: Все ответы предоставляются в формате JSON, что обеспечивает удобство обработки, а описание структуризированно.