# Рассчетно-пояснительная записка

## 1. Тема и целевая аудитория

В качестве сервиса для проектирования была выбрана стриминговая площадка **Apple Music**, предоставляющая доступ к композициям из **iTunes**. [^1], [^2].

| Регион                                     | Аудитория (млн)        |
|--------------------------------------------|------------------------|
| **Северная Америка**                       | 33                     |
| **Индия**                                  | 10                     |
| **Великобритания**                         | 5                      |
| **Канада**                                 | 4                      |



### MVP функционал

- Аутентификация
- Поиск музыки
- Стриминг музыки
- Личная библиотека и плейлисты
- Оффлайн доступ

### Ключевые продуктовые решения

- Интеграция с iCloud Music Library
- ALAC/AAC
- Интеграция текстов треков с Genius

## 2. Рассчет нагрузки

### Продуктовые метрики

| Метрика                                          	        |                 Значение метрики 	|
|----------------------------------------------------------	|---------------------------------:	|
| Месячная аудитория                               	        |                          93 млн 	|
| Дневная аудитория                                	        |                     46.2 млн[^3] 	|

#### Средний размер харанилища на одного пользователя

| Хранимые данные|Размер на одного пользователя|
|----------------------------------------------------------	|---------------------------------:	|
| Аватар                               	        |                         2 МБ 	|
| Мета информация                                	        |                     1 МБ 	|

#### Среднее количество действий пользователя в день

|Действие пользователя |Количество / день|
| ------------- |-------------|
|Регистрация| 16.71 тыс|
|Авторизация| 1.54 млн|
|Прослушивание музыки| 140 минут|
|Создание плейлистов| 185.7 тыс|
|Добавление в плейлист| 742,8 тыс|
|Открытие страницы исполнителя| 15.4 млн|


- По статистике [^1] в 2021 году Apple music насчитывал 78.6 млн пользователей, а в 2022 году уже 84.7 млн. Вычитаем эти числа и получаем 84.7 - 78.6 = 6.1 мл прироста пользователей за год. Делим это число на количество дней и получаем среднее количество регистраций в день
  ```
  6.1/365 = 0.01671 млн = 16 тыс в день
  ```
  Среднее количество авторизаций в день можно примерно вычислить зная DAU, которое равно 46.2 млн пользователей в день. Учитывая, что auth token живет месяц, делим это число на 30 и получаем примерное кол-во авториъзаций в день.
  
  ```
  46.2/30 = 1.54 млн в день
  ```
- Так как не удалось найти информацию о том, сколько треков в день слушает пользователь Apple Music были использованы данные Spotify [^4]. На данной платформе пользователи в среднем в день слушают 40 треков, средняя длительность трека составляет 3.5 минут.
  ```
  40 * 3.5 = 140 минут в день
  ```
- Аналогично, используя статистику Spotify, можно узнать сколько в среднем плейлистов пользователи создают за день. Это количество равняется 1.3 млн в день [^5]. Учитывая что аудитория Spotify превосходит аудиторию Apple Music в 7 раз, то для того чтобы узнать сколько в среднем плейлистов за день создают пользователи Apple Music можно разделить 1.3 млн на 7.
  ```
  1.3 / 7 = 0.1857 млн = 185.7 тыс в день
  ```


### Технические метрики

#### Хранилище

- Для расчета обьема хранилища, выделяемого под пользователей, умножим средний размер хранилища на одного пользователя на кол-во пользователей
  ```
  3 * 93 = 279 ТБ
  ```
  В Apple Music треки хранятся в стандартном качестве AAC с битрейтом до 256 кбит/с, средний вес такого трека составляет 7 МБ. Также есть возможность слушать     музыку в Lossless качестве 16-бит/44.1 кГц (CD качество) и средний вес такого трека составляет 40 МБ. Соответственно примем, что треки хранятся в двух видах    качества и занимают в среднем 47 МБ. Исходя из того что библиотека Apple Music насчитывает около 100 млн треков [^6], можно рассчитать суммарный обьем          хранилища, выделяемого под музыку. 
  ```
  47 * 100 = 4700 ТБ
  ```

|Хранилище |Размер|
| ------------- |-------------|
|Пользователи| 279 ТБ|
|Музыка| 4700 ТБ |


#### Сетевой трафик и RPS

|Действие пользователя |RPD|RPS|Пиковое потребление|Суммарный суточный,Гбайт/сутки|
| ------------- |-------------|-------------|-------------|-------------|
|Регистрация| 16.71 тыс| 0.1932 | 7.293 Мбит/c | 48.90 |
|Авторизация| 1.54 млн| 18.18 | 0.457 Гбит/с | 4596.63 |
|Создание плейлистов|185.7 тыс| 2,1490 | 9.81 Мбит/c | 65.86 |
|Добавление в плейлист|742,8 тыс| 8.5930 | 6.6 Мбит/c | 44.25 |
|Открытие страницы исполнителя|15.4 млн| 171.1111 | 0,42045 Гбит/с | 4229 |


## 3. Глобальная балансировка нагрузки

### Расположение ЦОДов

![image](https://github.com/user-attachments/assets/1c05ea5e-5d63-4e5f-8b26-08753877cb1e)

- Мейден, Северная Каролина, США. Один из крупнейших ЦОДов площадью в 500 000 кв. футов. Частично работает на солнечной энергии. 
- Уоки, Айова, США. Строительство этого ЦОДа началось в 2022 году и на данный момент его площадь составляет 398 473 квадратных фута. В общей сложности Apple планирует построить в этом месте 6 центров обработки данных площадью около 2 миллионов квадратных футов.
- Меса, Аризона, США. Этот дата-центр площадью 1,3 миллиона квадратных футов служит ключевым логистическим и операционным центром, поддерживающим все центры обработки данных Apple по всему миру. Этот ЦОД был оборудован на основе фабрики одного из бывших поставщиков Apple, обьявившим о банкротстве. Открылся в 2017 году. 
- Прайнвиль, Орегон, США. ЦОД площадью 3 млн квадратных футов, чье строительство началось в 2014 году, а в 2021 началось значительное расширение.
- Рино, Невада, США. ЦОД площадью 1 млн квадратных футов.
- Выборг, Дания. Apple открыла свой дата-центр в Выборге, Дания, в 2020 году, построив помещение площадью 484 376 квадратных футов. В течение 2022 и 2023 Apple активно расширяла этот ЦОД, в частности подключила свой дата-центр в Выборге к системе централизованного теплоснабжения, чтобы использовать избыточную тепловую энергию.  В конечном счете Apple планирует расширить этот ЦОД до 1.8 млн квадратных футов. 
- Ньюарк, Нью-Джерси, США. Приобретен Apple в 2006 году, с площадью 107 тыс. кв. футов.
- Тоберро, Ирландия. Имеет площадь 324 тыс. кв. футов. Выкуплен Apple в 2014 году, планируется расширение до 1.8 млн. кв. футов.
- Гуйян и Уланкаб, Китай. Согласно китайскому законодательству, Apple обязана размещать данные своих китайских пользователей в местных центрах обработки данных. Таким образом, у Apple есть два действующих центра обработки данных в Китае, которые работают в партнерстве с Guizhou-Cloud Big Data Industry Development Co., Ltd. (GCBD), поставщик услуг в материковом Китае, контролируемый государством. Географически Apple располагает одним дата-центром в городе Гуйян, столице провинции Гуйчжоу, и еще одним в городе Уланкаб, входящем в состав региона Внутренняя Монголия.

### Схема DNS балансировки

Наиболее оптимальным решением для DNS балансировки будет Geo-based DNS. Все дело в том что DNS достаточно хорошо работает на уровне стран - континентов, т.к. айпи адреса хорошо локализованы по континентам и странам. Сервер выдаст адрес ближайшего к пользователю ДЦ. 

Однако при балансировки внутри страны могут быть проблемы, т.к. внутри страны айпи адреса географически сложно отличить. Поэтому между ДЦ механизм регулировки трафика будет другой.

### Механизм регулировки трафика между ДЦ

Даже когда мы уже получили айпи адрес из DNS, мы еще можем влиять куда он приземлится физически. Для этого используется технология BGP Anycast.

## 4. Локальная балансировка нагрузки
![gigaROOSTER drawio](https://github.com/user-attachments/assets/8def3858-9698-458e-9990-32382629d355)

На уровне ДЦ устанавливаем маршрутизатор, распределяющий трафик с помощью BGP на L3 балансировщик.

### L3 балансировка
Используем Linux Virtual Server via Direct Routing. При таким варианте реализации маршрутизатор отправляет запросы на виртуальные ip адреса, при этом на сетевом уровне айпи пакет остается неизменным, а в ethernet фрейме меняется мак адрес назначения на основе загрузки сервера, отправляя их на определенные L7 балансировщики. Ответы отправляются напрямую клиентам, минуя L3 балансировщик. Стоит учесть что такой вариант предполагает нахождение внутри одной физической сети.

Для управления балансировщиком и отслеживания состояния L7 балансировщика используем keepalived, который следит за состоянием кластера и посылает специальные запросы на хосты, проводит внутренний health check. 

### L7 балансировка
Используем HTTP Reverse Proxy в качестве которого выступает Nginx. Он будет распределять запросы от клиентов по нескольким кластерам и решать проблему медленных юзеров (когда воркер сразу отдает ответ Nginx а не ждет пока клиент его получит).

### Kubernetes
Используем Kubernetes для оркестрации контейнеров, он занимается автоматическим запуском подов, балансирует нагрузку между ними а также выполняет масштабирование с помощью auto-scaling.


## 5. Логическая схема БД

![image](https://github.com/user-attachments/assets/ad3f815e-1320-4fc8-b5fd-df0be25c9be5)


|Таблица |Описание |Требование к консистентности |Размер |
| ------------- |-------------| -------------|-------------|
|User|Хранит информацию о пользователях сервиса. Каждый пользователь имеет уникальный идентификатор id, email и username.| id - PK, email - UNIQUE, username - UNIQUE|4 + 20 + 30 + 8 + 50 + 20 = 132 байта |
|Session|Хранит информацию о текущих активных сеансах пользователей. Каждая сессия имеет уникальный идентификатор session_id а также хранит идентификатор пользователя user_id и время окончания действия сеанса expiration| session_id - PK |8 + 8 + 8 = 24 байта |
|Playlist|Хранит информацию о плейлистах пользователей. Каждый плейлист имеет уникальный id. Также хранится информация о его названии, его описание, картинка и внешний ключ на пользователя которому принадлежит плейлист.| playlist_id - PK |8 + 8 + 20 + 50 + 30 = 116 байт |
|Song|Хранит информацию о треках. Каждый трек имеет уникальный id. Также хранится информация о его названии, ссылка на файл в s3, количество прослушиваний и длительность трека. Также имеется внешний ключ на альбом.| id - PK |8 + 20 + 50 + 4 + 2 + 8 = 92 байтa |
|Playlist_Song|Хранит информацию о треках в плейлистах.| playlist_id, song_id - PK |8 + 8 = 16 байт |
|LikedSongs|Хранит информацию о треках, которые лайкнул пользователь.| song_id, user_id - PK |8 + 8 = 16 байт |
|Genre|Хранит информацию о жанрах. Каждый жанр имеет уникальный id и имя.| id - PK, name - UNIQUE |4 + 20 = 24 байтa |
|Genre_Song|Хранит информацию о жанрах песен.| genre_id, song_id - PK |4 + 8 = 12 байт |
|Album|Хранит информацию об альбомах. Каждый альбом имеет уникальный id, а также хранится информация о его названии, картинке, дате публикации и внешний ключ на автора.| id - PK |8 + 30 + 50 + 8 + 4 = 100 байт |
|Artist|Хранит информацию об исполнителях. Каждый исполнитель имеет уникальный id. Также хранится информация о его имени, количестве прослушиваний в месяц, описание, дата рождения и жанр.| id - PK |4 + 20 + 2 + 256 + 2 + 4 = 288 байт |

## 6. Физическая схема БД

### Разбиение по СУБД

|Таблица |СУБД |Индексы |
| ------------- |-------------|-------------|
|Session| Aerospike| session_id - поиск сессии по идентификатору |
|User| Cassandra | id - при поиске пользователя через плейлист,username - при поиске пользователем других пользователей, email - при входе |
|Song| Cassandra + S3 | id , name - при поиске пользователем трека по названию|
|Playlist| MongoDB | name - при поиске пользователем по названию, user_id - при просмотре плейлистов другого пользователя|
|Playlist_Song| MongoDB | playlist_id - поиск трека в конкретном плейлисте |
|LikedSongs| MongoDB | user_id - при просмотре добавленных треков |
|Genre| Cassandra | name - при поиске жанра по названию |
|Genre_Song| Cassandra | genre_id - при поиске песен по жанру |
|Album| Cassandra | id, name - при поиске альбома по названию, artist_id - при просмотре альбомов исполнителя |
|Artist| Cassandra | id - при поиске исполнителя альбома, name - при поиске исполнителя по имени |

### Шардирование

- User - горизонтальное шардирование по id
- Playlist - горизонтальное шардирование по user_id
- Album - горизонтальное шардирование по id
- Song - горизонтальное шардирование по album_id
- Artist - горизонтальное шардирование по id

### Резервное копирование 

- Cassandra - резервирование с помощью medusa + s3
- MongoDB - Managed Service будет обеспечивать автоматическое резервное копирование
- Aerospike - Aerospike Backup Service (ABS)

### Аналитика

Для аналитики данных наиболее удачным выбором будет Clickhouse из за своей большой скорости и возможности очень быстро читать запросы на больших обьемах данных.

Обработкой и передачей данных в clickhouse будет заниматься Kafka. 



## 7. Алгоритмы

### Коллаборативная фильтрация

Классическая реализация этого алгоритма основана на на принципе n ближайших соседей. Ищем для каждого пользователя n наиболее похожих на него пользователей и дополняем информацию о пользователе данными о его соседях. 
![image](https://github.com/user-attachments/assets/f9e25117-d624-40ca-98ab-92970e5e7218)
В матрице выше желтым цветом обозначен пользователь, для которого необходимо найти оценки по новому контенту (знаки вопроса). Синим цветом выделены три ближайших к нему соседа. 
У классической реализации данного алгоритма есть явный минус - он плохо применим на практике из за квадратичной сложности. 
Отчасти эта проблема решается мощностью железа но также можно внести некоторые корректировки в алгоритм:
- не пересчитывать матрицу расстояний полностью а обновлять ее инкрементально
- сделать выбор в пользу итеративных и приближенных алгоритмов

### Implicit ALS 

Явный фидбек от пользователей в сервисе прослушивания музыки недоступен, поэтому приходится ориентироваться на неявный (кол-во прослушиваний, лайков).
Допустим человек не слушал песню. Значит ли это что она ему не нравится? Или просто среди миллионов треков ему еще не попался эта конкретная песня? А если человек прослушал только часть песни, значит ли это что она ему не понравилась? Может самую запоминающуюся часть он все таки прослушал? 
Таким образом, рассуждения переходят из дихотомии понравилось/не понравилось в пространство степени уверенности: «мы не уверены, что полсекунды прослушивания означают, что ему понравилось, но гораздо сильнее уверены, что три минуты прослушивания означают, что ему понравилось»
Стандартный ALS метод преобразуется таким образом в новую функцию для оптимизации, с которой можно работать в рамках описанного выше алгоритма. 
![image](https://github.com/user-attachments/assets/89b43e4e-bdaa-425e-a40f-60f5757a4d93)
![image](https://github.com/user-attachments/assets/5ba72649-9e6c-49ce-b4d6-69be7afbe3a6)
![image](https://github.com/user-attachments/assets/a5457549-34c5-4778-a8a4-68156954b450)


## 8. Технологии
|Технология |Где применяется |Мотивация |
| ------------- |-------------|-------------|
|Golang |Бэкенд, бизнес логика приложения| Наилучший вариант для высоконагруженных распределенных систем благодаря своей эффективной параллельной обработке |
|Cassandra |Хранение данных, OLTP |Имеет ряд преимуществ по сравнению с реляционными БД, более высокая скорость, хорошо масштабируется |
|MongoDB|Хранение данных, OLTP|Используется в тех таблицах, где важна скорость массовых апдейтов, т.к. кассандра с этим плохо справляется|
|Aerospike | Хранение данных сессии |Имеет ряд преимуществ по сравнению с аналогами по типу Redis, ssd optimized. |
|k8s|Оркестрация контейнеров|Используем kubernetes для балансировки и автомасштабирования бекенда|
|S3|Обьектное хранилище, хранение файлов музыки|Надежность, масштабируемость|
|Clickhouse |Хранение данных для аналитики, OLAP | Огромная скорость, хранит данные очень компактно в виде одной большой таблицы |
|Kafka |стриминговая БД, сбор большого потока событий-данных | Предоставляет единообразную платформу с высокой пропускной способностью и низкой задержкой для обработки данных в режиме реального времени. |

## 9. Схема проекта
![sdfgs drawio (4)](https://github.com/user-attachments/assets/e1aa57b3-75d9-4159-9fc5-e4e2ba1d69c7)


## 10. Обеспечение надежности
### Cassandra
Использование Medusa for Apache Cassandra, которая представляет собой комплексный инструмент резервного копирования и восстановления как одной ноды, так и целого кластера. Есть возможность интеграции с S3 совместимыми хранилищами.

### Aerospike
Aerospike использует архитектуру без общего доступа (SN), в которой:
- Каждый узел в кластере идентичен.
- Все узлы являются одноранговыми.
- Единой точки отказа не существует.

Используя алгоритм Aerospike Smart Partitions, данные равномерно распределяются по всем узлам кластера.

### Nginx
Используем механизмы keepalived и health checks.

### Логгирование и Мониторинг:
Для того чтобы быстро отслеживать запросы будем использовать паттерн RequestId. Сами логи будут храниться в MongoDB в виде JSON.

## 11. Список серверов

Источники: 
[^1]: [Apple Music Statistics 2024](https://headphonesaddict.com/apple-music-statistics/)
[^2]: [Form 10-K](https://s2.q4cdn.com/470004039/files/doc_earnings/2023/q4/filing/_10-K-Q4-2023-As-Filed.pdf)
[^3]: [Hypestat](https://hypestat.com/info/music.apple.com)
[^4]: [Insights into Spotify Listening Statistics](https://wifitalents.com/statistic/spotify-listening/)
[^5]: [Exploring Eye-Opening Spotify Playlist Statistics: 4 Billion Playlists Created](https://gitnux.org/spotify-playlist-statistics/)
[^6]: [Celebrating 100 million songs](https://www.apple.com/newsroom/2022/10/celebrating-100-million-songs/)
[^7]: [IEM](https://www.internetexchangemap.com/)
[^8]: [Submarine Cable Map](https://www.submarinecablemap.com/)
