# Курс Highload. Проект веб-сервиса

### Выбор темы

Сервис-мессенджер (Telegram)

Функционал MVP: отправка и получение текстовых сообщений, которые сохраняются на сервере в открытом виде. 

#### Целевая аудитория

* Возраст: 18-40 лет

* География: РФ

* Аудитория: 7 млн человек

### Расчет нагрузки

* Кол-во пользователей: 7 миллионов человек ([аудитория Facebook Messenger в 2020 году](https://www.dp.ru/a/2020/10/17/Auditorija_Telegram_v_Ross))

* Среднее кол-во сообщений в день на одного пользователя: [150](https://bloggingx.com/telegram-statistics/#:~:text=Telegram%20user%20and%20usage%20statistics&text=It%20is%20estimated%20that%20around,sent%20daily%20per%20Telegram%20user.)

* Средняя длительность сессии: [12 минут](https://www.likeni.ru/events/whatsapp-samyy-populyarnyy-messendzher-u-abonentov-bilayna/) 

* Максимальная длина одного сообщения: 4096 символов ([макс. размер сообщения в Telegram](https://www.vamtlgrm.com/limity-sushhestvuyushhie-v-telegram/#:~:text=%D0%94%D0%BB%D0%B8%D0%BD%D0%B0%20%D0%BE%D0%B4%D0%BD%D0%BE%D0%B3%D0%BE%20%D1%81%D0%BE%D0%BE%D0%B1%D1%89%D0%B5%D0%BD%D0%B8%D1%8F%20%D0%B2%20%D0%BB%D0%B8%D1%87%D0%BD%D1%8B%D1%85,%D0%B8%20%D0%BA%D0%B0%D0%BD%D0%B0%D0%BB%D0%B0%D1%85%20%E2%80%94%20%D0%B4%D0%BE%201024%20%D1%81%D0%B8%D0%BC%D0%B2%D0%BE%D0%BB%D0%BE%D0%B2.))

* Среднее кол-во диалогов пользователя: 150 ([число Данбара](https://ru.wikipedia.org/wiki/%D0%A7%D0%B8%D1%81%D0%BB%D0%BE_%D0%94%D0%B0%D0%BD%D0%B1%D0%B0%D1%80%D0%B0))

* Размер одного сообщения: 9 Кб (сообщ. макс. длины на кириллице, закодированное в UTF-8 + 1 кб метаданных)

* Среднее кол-во одновременных сессий: 25 * 7  * 10^6 / 24 / 60 * 12 = 1'456'333

* Общее кол-во запросов в сутки:
  
  * 150 отправленных сообщений 
  
  * 25 запросов на получение дельты чатов ([статистика](https://www.tadviser.ru/index.php/%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D1%8F:%D0%9C%D0%B5%D1%81%D1%81%D0%B5%D0%BD%D0%B4%D0%B6%D0%B5%D1%80%D1%8B_(Instant_Messenger,_IM)#:~:text=%D0%9A%D0%B0%D0%BA%20%D0%B2%D1%8B%D1%8F%D1%81%D0%BD%D0%B8%D0%BB%D0%BE%D1%81%D1%8C%2C%20%D0%B2%20%D1%81%D1%80%D0%B5%D0%B4%D0%BD%D0%B5%D0%BC%20%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D1%8C,%D0%B8%D0%B4%D1%83%D1%82%20Viber%2C%20Telegram%20%D0%B8%20Skype.) использования мессенджеров)
  
  * 300 запросов на получение дельты диалога (пользователь открывает диалог при получении и при отправке сообщений)

* Avg Requests Per Day
  
  * 7 * 10^6 * (150+25+300) = 3.3 * 10^9 rpd

* Avg Requests Per Second:
  
  * 3.3 * 10^9 / 24 / 60 / 60 = 56'750 rps

* Read requests:
  
  * 7 * 10^6 * (25+300) = 2.2 * 10^9 rpd 
  
  * 2.2 * 10^9 / 24 / 60 / 60 = 44'600 rps

* Write requests:
  
  - 7 * 10^6 * (150) = 1.05 * 10^9 rpd
  
  - 1.05 * 10^9 / 24 / 60 / 60 = 12'150 rps

* RPS по типам:
  
  * Отправка сообщения: 7 * 10^6 * (150) /  24 / 60 / 60 = 12150 RPS
  
  * Получение дельты списка чатов: 7 * 10^6 * 25 / 24 / 60 / 60 = 2050 RPS
  
  * Просмотр диалога: 7 * 10^6 * 300 / 24 / 60 / 60 = 24'300 RPS

* Входящий трафик за сутки:  
  
  * 7 * 10^6 * (150 * 9216 + (25 + 300) * [512](http://dev.chromium.org/spdy/spdy-whitepaper)) = 10.5 ТБ

* Исходящий трафик за сутки:
  
  * 7 * 10^6 * ((150) * 9216  + (25 * 150) * 512 + 300 * (150/25) * 512)= 204.8 ТБ

Примем, что пиковые нагрузки превышают средние в 3 раза (согласно [статистике](https://www.protarif.info/news/new?id=1449) мобильных операторов)

* Peak PRS: 115'250
  
  * Read RPS: 79'000
  
  * Write RPS: 36'450
    
    По типам:
    
    - Отправка сообщения: 36450 RPS
    
    - Получение дельты списка чатов:  6150 RPS
    
    - Просмотр диалога: 72'900 RPS

* Пиковый входящий трафик (в секунду):
  
  * 10.5 ТБ * 3 / 24 /60 / 60 = 3 Гбит/Сек

* Пиковый исходящий трафик (в секунду):
  
  - 204.8 ТБ * 3 / 24 /60 / 60 = 58.3 Гбит/Сек

### Логическая схема БД

![](logic_db.png)

### Физическая схема БД

Для хранения основных данных (диалогов, сообщений и пользователей) используется база данных PostgreSQL, так как она является одной из наиболее функциональных, производительных и широко распространённых реляционных БД.

Для хранения информации о сессиях используется база данных Redis по причине того, что она осуществляет хранение данных in-memory, имеет поддержку  неблокирующей репликации master-slave и возможность организации кластера Redis cluster.

Для хранения очереди непрочитанных сообщений используется Redis. Для каждого user_id сохраняется список, содержащий информацию о сообщениях, ожидающих получения. При запросе дельты сообщений список очищается. При отправке сообщения пользователю, не находящемуся в сети, оно добавляется в список. 

![](real_db.png)

#### Расчет требуемого количества памяти

Пользователи: (8 + 64 + 32 + 16) * 7 * 10^6 = 800МБ

Диалоги: (8+8+8) * 7 * 10^6 * 150 / 2 = 11.7 ГБ

Для расчета объема хранимых сообщений используем промежуток в 1 год

В качестве средней длины хранимого сообщения примем [100](https://vc.ru/flood/3673-ideal-length-of-everything-online) символов кириллицы (200 байт в UTF8).

Сообщения: (8 + 8 + 8 + 200 + 8) * 7 * 10^6 * 150 * 365 = 81 ТБ / Год

Очередь сообщений: (150/25) * 256 * 7 * 10^6 = 10 ГБ

Сессии: [220](https://lucasmagnum.medium.com/redistip-estimate-the-memory-usage-for-repeated-keys-in-redis-2dc3f163fdab) * 7 * 10^6 = 1.4 ГБ

#### Шардирование и репликация

Шардирование данных в PostgreSQL о пользователях и диалогах осуществляется по полю id, о сообщениях - по полю dialogue_id.

Для повышения быстродействия системы хранилище сообщений возможно разбить на большое количество партиций. При создании 2048 партиций [PostgreSQL 12: Partitioning is now faster - 2ndQuadrant | PostgreSQL](https://www.2ndquadrant.com/en/blog/postgresql-12-partitioning/)) средний размер таблицы составит 40.5 ГБ.  

Для каждого master'a (используется для записи) в Redis и PostgreSQL имеются два slave'a (используются для чтения).

### Выбор технологий

Язык программирования для backend: golang, так как он является компилируемым, обладает встроенными механизмами организации многопоточности goroutines и статической типизацией, поддерживает разнообразные библиотеки для работы с сетью.

Язык программирования для frontend: TypeScript, так как встроенная типизацию снижает вероятность возникновения ошибок относительно JavaScript. 

Frontend Framework: Vue.js, так как он поддерживает TypeScript, имеет более производительную реализацию VirtualDOM, чем React. TypeScript позволяет осуществлять простую миграцию версий и имеет подробную документацию.

Формат приложения: PWA для мобильных устройств

Для отдачи статических файлов и балансировки нагрузки используется nginx, так как он позволяет эффективно распределять нагрузку на сервера. Для балансирования нагрузки на nginx можно использовать DNS. 

Создать redundancy group для серверов DNS и nginx возможно при помощи протокола CARP.  

#### Оборудование

**Nginx**

Машина с 16 CPU позволяет обрабатывать около 383'000 RPS при размере пакета в 10 КБ через HTTPS ([Testing the Performance of NGINX and NGINX Plus Web Servers - NGINX](https://www.nginx.com/blog/testing-the-performance-of-nginx-and-nginx-plus-web-servers/)). Пропускная способность составлет 48 Гбит/сек. Кол-во соединений: 50000. Для обработки пиковой нагрузки потребуется 30 серверов. Для обеспечения отказоустойчивости оправдано увеличение количества используемых серверов до 46-и.

**Backend сервера**

Один процессор позволяет обрабатывать около 3'500 RPS на запросах, не требующих значительных вычислений ([Бенчмарк](https://github.com/smallnest/go-web-framework-benchmark)). Машина с 16-ю процессорами  и 32 ГБ ОЗУ способна поддерживать [10^6](https://www.freecodecamp.org/news/million-websockets-and-go-cc58418460bb/) подключений. В случае машины с 16 ядрами потребуется 3 машины. Для обеспечения отказоустойчивости оправдано увеличение количества используемых серверов до 5-и.

**Сервера базы данных** **PostgreSQL**

Приняв производительность сервера PostgreSQL с 32 ядрами равной [4'500QPS](https://www.ashnik.com/fine-tuning-postgres-to-achieve-5000-queries-per-second/),

потребуется 30300÷4500=7 master'ов (запись) и 133800÷4500=30 slave'ов (чтение). Так как для каждого master узла требуется 2 зависимых, необходимо 15 master + 30 slave = 45 машин. 

Для повышения надежности при повышенных нагрузках оправдано увеличение количества используемых серверов до 23+46=69 машин.

для хранения сообщений на каждом сервере используется 2048/23=89 таблиц средним размером 81*1024/2048=40.5 ГБ

Для хранения данных на каждом из серверов (без учета архива для сообщений давностью более 1 года) потребуется 82÷23= 3.5 ТБ постоянной памяти.  

Обеспечить сохранность данных возможно при помощи организации массивов RAID50. Данная схема обладает высокой скоростью записи данных и повышенной стойностью при отказах. Для этого потребуется 6 SSD дисков по 1ТБ (в группе 6 носителей, доступная ёмкость: 4 ТБ).

Для обеспечения постоянной памятью всех шардов потребуется 23*6=138 дисков по 1 ТБ.

**Сервера Redis**

При величине актуальности авторизационной сессии в 10 дней, количество авторизационных запросов составит  7 * 10^6 / 30 / 24 /60 / 60 = 2.7 RPS.

Общий объем данных сессий: 1.4 ГБ

Количество запросов на добавление сообщения в очередь: 7 * 10^6 * 150 / 24 /60 / 60 = 12'150 RPS; 

Чтение сообщений: 7 * 10^6 * 25 / 24 /60 / 60 = 2'025 RPS 

Очистка очереди сообщений: 2'025 RPS

Redis способен обрабатывать 100'000 RPS на строках в 4КБ ([How fast is Redis? – Redis](https://redis.io/topics/benchmarks)), что превышaает возможные пиковые нагрузки (12150+2025+2025) * 3 = 48'600 RPS

Средний объем данных очереди сообщений: 10 ГБ

Для хранения данных в Redis будет достаточно 3-х (master + 2 slaves) узлов с 32 ГБ ОЗУ.

| Назначение | Кол-во CPU | ОЗУ   | SSD    | Количество |
| ---------- | ---------- | ----- | ------ | ---------- |
| DNS        | 4          | 8 GB  | 128 GB | 2          |
| NGINX      | 16         | 16 GB | 256 GB | 46         |
| Backend    | 16         | 32 GB | 128 GB | 5          |
| PostgreSQL | 32         | 32 GB | 6 ТБ   | 69         |
| Redis      | 4          | 32 GB | 128 GB | 3          |

#### Расположение серверов

Так как наиболее значительная часть пользователей проживает в Европейской части России, наилучшим выбором являются дата-центры в Москве или Московской области. Для обеспечения отказоустойчивости предпочтительно размещение ресурсов у различных провайдеров. 

#### Отказоустойчивость

* DNS сервер и сервера NGINX дублируются путём объединения серверов через CARP.   Virtual IP каждой пары NGINX отдаётся DNS сервером по алгоритму round-robin.  При отказе одного из серверов запросы обрабатывает его пара.

* Бэкенд сервера находятся в ротации на NGINX по алгоритму round-robin. При отказе бэкенд сервера он исключается из ротации. Имеются два резервных сервера для снижения риска возникновения запредельных нагрузок при отказе одного или нескольких серверов. 

* Сервера БД PostgreSQL и Redis объединены в группы master + 2x slave. При отказе master его роль принимает один из slave'ов, при отказе slave'а чтение продолжается с другого slave'а.

### Схема проекта

![](diagram.png)
