# Тестирование производительности с помощью Jmeter

Для начала добавим к нашему `Test Plan` необходимые компоненты, а именно:
`Test plan` -> `Add` -> `Threads (Users)` -> `Thread Group` 

После того как мы создали первый набор потоков внесем следующие параметры (`Thread Properties`):

- `Number of Threads (users)` - 100 (это количество потоков или виртуальных пользователей),
- `Ramp-up period (seconds)` - 10 (в течение данного промежутка времени (в секундах) будут постепенно запускаться потоки, которые мы прописали в `Number of Threads (users)`) 
- `Loop Count` - 1 (сколько раз потоки выполнят сценарий) 

> > Входные данные выбраны случайным образом для иллюстрации

![](https://github.com/Evergaarden/apache_jmeter/blob/main/img/thread_group_config.png?raw=true)

Теперь создадим шаблон для наших последующих HTTP-сэмплеров, 

`Test plan` -> `Add` -> `Config Element` -> `HTTP Request Defaults`. В созданном шаблоне укажем следующие параметры:
- `Protocol [http]`: `http`
- `Server Name or IP`: `162.55.220.72`
- `Port Number`: `5005`
