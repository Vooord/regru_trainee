Вашему вниманию предоставляется простой самописный (велосипедный) **HTTP-сервер**, выполненный на голых сокетах с блэк-джеком и *всем остальным*. 
Программа разработана в рамках обучения от компании *REG RU*, поэтому в некоторых моментах  я буду ссылаться на «ТЗ».

Функционал:
---

--
Порт и адрес конфигурируются с помощью argparse (умолчания предусмотрены). Это позволяет запустить программу примерно вот так (
для --host эквивалентно -H, а для --port соответственно -p):
```
python3 server.py --host 127.0.0.1 --port 8888 
```
так
```
python3 server.py --host 127.0.0.1
```
или даже так
```
python3 server.py
```

--
Если сервер получает __не http запрос__, он выбрасывает исключение работы с сетью и с соответствующей ошибкой завершает работу полностью (так было в ТЗ).

--
При обработке запроса сервер оформляет все его данные __в формате словаря__.

--
Если http метод __не GET/POST__, то клиенту отдается ответ с HTML документом, в котором написано, что программа принимает только GET/POST запросы.

--
Все запросы пишутся в __access.log__. Он бывает двух видов:
•	__полный__ (по умолчанию), когда запрос логируется целиком
•	__сокращенный__, когда логируется только http метод, url и куки.
Если пришел POST запрос /short_log/1, то все последующие запросы от данного клиента логируются в сокращенном виде.

--
Все ошибки логируются в __error.log__, а клиенту отдается ответ с нужным http кодом и HTML телом в котором написано, что-то внятное. Если для данного клиента включена опция show_errors, то в тело также передается оригинальное сообщение отловленной ошибки.
Запрос __/showerrors/1__ включает опцию show_errors
Запрос __/show_errors/0__ выключает эту опцию

--
Если пришел GET запрос __/div/<любозначение1>/to/<любозначение2>__, то программа делит то что будет передано в <любозначение1> на то что будет передано в <любое_значение2>

--
Если пришел запрос __/set_cookie/=/smth__, то программа добавляет соответствующие куки в ответ (примитивная валидация куков предусмотрена).
Если программа в запросах получает куки bg_color=green, то все HTML документы которые программа отдает клиенту имеют зеленый бэкграунд. Иначе — белый.

--
Запрос на __«/»__ отображает стартовую страницу, на которой есть форма для отправки остальных запросов.
Все __прочие GET/POST запросы__ на любые другие url отдают соответствующую HTTP ошибку.


Прочее
---

Программа разделена на **3** основных блока:
1.    принять запрос (*accept_request*)
2.    обработать запрос (*process_request*)
3.    отправить ответ (*process_response*)

Им помогают функции:
*    make_log (вызывается после отправки ответа клиенту и пишет лог в access_log)
*    make_default_answer_dict (создаёт шаблонный словарь answer по переданным параметрам)
*    fatal_error (вызывается в случае грубой ошибки в структуре http-запроса, пишет в error_log и роняет сервер)

В файле **client** лежат подсобные сущности для работы с клиентом.