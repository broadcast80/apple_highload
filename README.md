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
На уровне ДЦ устанавливаем маршрутизатор, распределяющий трафик с помощью BGP на L3 балансировщик.

### L3 балансировка
Используем Linux Virtual Server via Direct Routing. При таким варианте реализации маршрутизатор отправляет запросы на виртуальные ip адреса, при этом на сетевом уровне айпи пакет остается неизменным, а в ethernet фрейме меняется мак адрес назначения на основе загрузки сервера, отправляя их на определенные L7 балансировщики. Ответы отправляются напрямую клиентам, минуя L3 балансировщик. Стоит учесть что такой вариант предполагает нахождение внутри одной физической сети.

Для управления балансировщиком и отслеживания состояния L7 балансировщика используем keepalived, который следит за состоянием кластера и посылает специальные запросы на хосты, проводит внутренний health check. 

### L7 балансировка
Используем HTTP Reverse Proxy в качестве которого выступает Nginx. 


Источники: 
[^1]: [Apple Music Statistics 2024](https://headphonesaddict.com/apple-music-statistics/)
[^2]: [Form 10-K](https://s2.q4cdn.com/470004039/files/doc_earnings/2023/q4/filing/_10-K-Q4-2023-As-Filed.pdf)
[^3]: [Hypestat](https://hypestat.com/info/music.apple.com)
[^4]: [Insights into Spotify Listening Statistics](https://wifitalents.com/statistic/spotify-listening/)
[^5]: [Exploring Eye-Opening Spotify Playlist Statistics: 4 Billion Playlists Created](https://gitnux.org/spotify-playlist-statistics/)
[^6]: [Celebrating 100 million songs](https://www.apple.com/newsroom/2022/10/celebrating-100-million-songs/)
[^7]: [IEM](https://www.internetexchangemap.com/)
[^8]: [Submarine Cable Map](https://www.submarinecablemap.com/)
