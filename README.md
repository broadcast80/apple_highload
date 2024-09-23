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
- Уникальные персонализированные рекомендации
- Плейлисты от артистов
- Круглосуточные радиостанции
- Интеграция текстов треков с Genius
- Интеграция с Shazam

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
|Авторизация| 17.325 млн|
|Прослушивание музыки| 140 минут|
|Создание плейлистов| 185.7 тыс|


- По статистике [^1] в 2021 году Apple music насчитывал 78.6 млн пользователей, а в 2022 году уже 84.7 млн. Вычитаем эти числа и получаем 84.7 - 78.6 = 6.1 мл прироста пользователей за год. Делим это число на количество дней и получаем среднее количество регистраций в день
  ```
  6.1/365 = 0.01671 млн = 16 тыс в день
  ```
  Среднее количество авторизаций в день можно примерно вычислить зная DAU, которое равно 46.2 млн пользователей в день. Умножаем это число на среднее время
  которое пользователи проводят онлайн и получаем 46.2 * 9 = 415.8 млн часов в сутки. Делим получившееся число на кол-во часов в сутках и получаем среднее
  количество авторизаций в день
  
  ```
  415.8/24 = 17.325 млн в день
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

Для расчета обьема хранилища, выделяемого под пользователей, умножим средний размер хранилища на одного пользователя на кол-во пользователей
```
3 * 93 = **279 ТБ**
```
В Apple Music треки хранятся в стандартном качестве AAC с битрейтом до 256 кбит/с, средний вес такого трека составляет 7 МБ. Также есть возможность слушать музыку в Lossless качестве 16-бит/44.1 кГц (CD качество) и средний вес такого трека составляет 40 МБ. Соответственно примем, что треки хранятся в двух видах качества и занимают в среднем 47 МБ. Исходя из того что библиотека Apple Music насчитывает около 100 млн треков [^6], можно рассчитать суммарный обьем хранилища, выделяемого под музыку. 
```
47 * 100 = **4700 ТБ**
```

|Хранилище |Размер|
| ------------- |-------------|
|Пользователи| 279 ТБ|
|Музыка| 4700 ТБ |



Источники: 
[^1]: [Apple Music Statistics 2024](https://headphonesaddict.com/apple-music-statistics/)
[^2]: [Form 10-K](https://s2.q4cdn.com/470004039/files/doc_earnings/2023/q4/filing/_10-K-Q4-2023-As-Filed.pdf)
[^3]: [Hypestat](https://hypestat.com/info/music.apple.com)
[^4]: [Insights into Spotify Listening Statistics](https://wifitalents.com/statistic/spotify-listening/)
[^5]: [Exploring Eye-Opening Spotify Playlist Statistics: 4 Billion Playlists Created](https://gitnux.org/spotify-playlist-statistics/)
[^6]: [Celebrating 100 million songs
](https://www.apple.com/newsroom/2022/10/celebrating-100-million-songs/)
