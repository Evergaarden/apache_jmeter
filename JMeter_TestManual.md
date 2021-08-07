# Тестирование производительности с помощью Jmeter

Для начала добавим к нашему `Test Plan` необходимые компоненты, а именно:
`Test plan` -> `Add` -> `Threads (Users)` -> `Thread Group`. 

`Thread Group` - это группа компонентов, отвечающих за характеристики потоков (виртуальных пользователей) в ходе проведения тестирования.
`Thread Group` является компонентом первого уровня (не могут быть вложены в другие компоненты, не считая корневого `Test Plan`)

После того как мы создали первый набор потоков внесем следующие параметры (`Thread Properties`):

- `Number of Threads (users)` = 100 (это количество потоков или виртуальных пользователей),
- `Ramp-up period (seconds)` = 10 (в течение данного промежутка времени (в секундах) будут постепенно запускаться потоки, которые мы прописали в `Number of Threads (users)`) 
- `Loop Count` = 1 (сколько раз потоки выполнят сценарий) 

> > Входные данные выбраны случайным образом для иллюстрации

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/thread_group_config.png?raw=true)

Теперь создадим шаблон для наших последующих HTTP-сэмплеров, 

`Test plan` -> `Add` -> `Config Element` -> `HTTP Request Defaults`. В созданном шаблоне укажем следующие параметры:
- `Protocol [http]` : `http`
- `Server Name or IP` : `162.55.220.72`
- `Port Number` : `5005`

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_9A2tHRU5vD.png?raw=true)

Создадим HTTP-сэмплер 
`Test plan` -> `Add` -> `Sampler` -> `HTTP Request`.

Параметры:

- `HTTP Request` : `POST`
- `Path` : `/login`

Параметры `Web Server` мы заполнили в предыдущем шаблоне, поэтому здесь входные поля можно оставить пустыми

Также заполним `Parameters` для HTTP-сэмплера
          
        
           login : Bogdan
	   password : Qwerty123 
          
	  
![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_spHE8Hk9ZC.png?raw=true)

Далее добавим `Listener`

В JMeter `Listener` - это группа компонентов, собирающих различную информацию в ходе проведения тестирования. В свою очередь, это позволяют визуализировать данные, касающиеся прохождения теста в реальном времени.

Итак, воспользуемся `View Result Tree` (детальный результат выполнения запросов в виде списка) и `Summary Report` (таблица основных показателей с группировкой по имени сэмплера)

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_3obTyeEpGO.png?raw=true)


Теперь можно запустить потоки и посмотреть результаты прогона теста. Для этого нажимаем значок `Start` И далее смотрим в `View Result Tree` и `Summary Report`

Наиболее важные результаты для `Summary Report`: 

- `Samples` : количество виртуальных позвателей + их сценарий (количество запросов)
- `Average` : среднее время отлика от сервера каждого запроса (в миллисекундах)
- `Min` : минимальное зафиксированное время отлика от сервера (в миллисекундах)
- `Max` : максимальное зафиксированное время отлика от сервера (в миллисекундах)
- `Std. Dev` : стандартное отклонение
- `Error %` : зафиксированный процент ошибок
- `Throughput` : пропускная способность (количество запросов в секунду)

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_gcDlasKDPU.png?raw=true)


Результаты для `View Result Tree` менее информативны об общих сведениях деградации системы, если таковая имеется, но тем не менее можно увидеть следующие моменты:
- `Sampler result` : содержит информацию по каждому сэмплу (код ответа, количество циклов, тип данных и др) 

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/hmkFk2ZtgK.png?raw=true)

- `Request` : содержит краткую информацию о хедерах и теле запроса  

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_ZBnOxtyjbo.png?raw=true)

- `Response data` : содержит краткую информацию о хедерах и теле ответа

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_77701679sp.png?raw=true)


> > Хочется отметить, что использование того или иного `Listener` зависит от ситуации. Необходимо понять, для кого будет визуализирован график и какая цель преследуется. 
> > Нет никакого смысла добавлять множество графиков времени отклика приложения в отчет по каждой операции, если ваша цель — определить, есть ли утечки памяти, зафиксирована ли нестабильная работа во время теста надежности, или если вам нужно сравнить два релиза между собой в рамках регрессионного тестирования. 


Создадим новый `HTTP Request` (сэмплер) и назовем его `HTTP Request (2)`

В JMeter каждый новый созданый компонент появляется в конце списка и последовательность выполнения того или иного компонента зависит от его расположения в этом списке. Поэтому перетащим его выше 

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_5ERBLOps4p.png?raw=true)

В параметры сэмплера внесем:

- `HTTP Request` : `POST`
- `Path` : `/user_info`

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_g44IGRoWFY.png?raw=true)

Параметры `Web Server` по-прежнему оставляем пустыми, поскольку создан шаблон

Заполним `Body Data` и воспользуемся json форматом `{key:value}`:

    {"age": 25,
    "salary": 60000,
    "name": "Bogdan",
    "auth_token": "Здесь должен быть токен"}

Значение ключа `auth_token` можно заполнить вручную, для этого нужно скопировать из тела ответа сэмплера `HTTP Request` и вставить его значение `("/s34lfgbj/Bogdan/jjd909/99284kjkWpqc2787Qwerty12334752evny")` уже в текущий запрос, но лучше воспользуемся переменным окружением и автоматически достанем содержимое ответа `HTTP Request` и вставим его в тело запроса `HTTP Request (2)`

Для этого потребуется воспользоваться компонентом `Post Processors`, а именно `JSON Extractor`

`Post Processors` - группа компонентов, позволяющих выполнять определенные действия сразу после выполнения родительского компонента.
Обычно используются для внесения изменений в сэмплеры для обработки результатов (ответов)

В данном случае `JSON Extractor` позволит получить содержимое из ответа тестируемой системы формата JSON

`HTTP Request` -> `Add` -> `Post Processors` -> `JSON Extractor`

- `Names of created variable` (наименование переменной) : `token`
- `JSON Path expressions` (путь за содержимым) : `$.token`
- `Match No` : `1`

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_GXBYuOQ2Rp.png?raw=true)

Таким образом мы получили содержимое ответа сэмплера `HTTP Request`

Теперь нужно воспользоваться компонентом `Assertions`

`Assertions` - группа компонентов, позволяющих проверять результат выполнения запросов, сравнивая его с указанным утверждением

`HTTP Request` -> `Add` -> `Assertions` -> `JSON Extractor` -> `BeanShell Assertion`

С помощью `BeanShell Assertion` можно положить содержимое ответа `JSON Extractor` в окружение

Введем `${__setProperty(token,${token})}` в текстовой зоне

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_N8xIFJBRb0.png?raw=true)

> > Свойства (property) JMeter аналогичны переменным, но их зона видимости не ограничена конкретным потоком, а распространяется на весь Test Plan.
> > <br> Для обращения к свойствам используются соответствующие функции: 
> > <br> `${__setProperty('name', 'value')} — задать значение свойства`
> > <br> `${__property('name')} или ${__P('name')} — получить значение свойства`


Вернемся к параметрам сэмплера `HTTP Request (2)` и в `Body Data` дополним значение `${token}` к ключу `auth_token`

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_abzWjbNt53.png?raw=true)

Теперь при каждом запуске потоков, токен из ответа сэмплера `HTTP Request` будет автоматически подставляться сюда  

Также необходимо добавить еще один компонент для сэмплера `HTTP Request (2)`, а именно:

`HTTP Request (2)` -> `Add` -> `Config Element` -> `HTTP Header Manager`

И прописать вручную следующие свойства, используя кнопку `Add`: 

- `Content-type` : `application/json`

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_wugZec06nS.png?raw=true)

Шаблон `HTTP Header Manager` будет применен только для сэмплера `HTTP Request (2)` поскольку находится внутри родителя и не будет действовать на весь `Test Plan` как `HTTP Request Defaults`

`HTTP Header Manager` в данном случае нужен для того чтобы сервер смог принять запрос и ответить на него без ошибок, поскольку сервер ожидает по эндпойнту `/user_info` информацию о типе данных формата `application/json`, а наш сэмплер по умолчанию будет содержать информацию в хедере `text/plain`

Теперь запускаем наши потоки и смотрим результат

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_y5W2yyP8UJ.png?raw=true)

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/javaw_p33sLCJJqy.png?raw=true)










