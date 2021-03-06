#Задача:
	
Необходимо реализовать веб-приложение, которое выгружает из внешнего сервиса данные о случайно сгенерированном человеке, производит с ними небольшой расчет и отображает полученный результат на сайте.

Микросервисы с одной общей базой MSSQL (DemoSite), в которой будут храниться данные.
Два сценария взаимодействия модулей друг с другом посредством протокола http
(опционально: Коммуникация по шине сообщений)

Каждый модуль можно оформить как проект из шаблона WebAPI / MVC.

Приложение должно состоять из модулей: “Web Site”, “People”, “Poems”, “Reports”
(Каждый "модуль" - микросервис в отдельном *.proj файле)
	
Описание:

Cайт на ASP.Net MVC с одним представлением. 
В этом представлении отображается список выгруженных данных
(Поля: gender, firstName, lastName, "[city] [street]", email, picture, poem, distance)

Так же на этой форме отрисованы две кнопки: 
"Generate one" - загрузить информацию о случайно сгенерированном человеке
"Download report" - скачать список выведенных на экран данных о людях в формате excel или xml (на выбор).

Сценарий "Get one":

1. Происходит вызов модуля "People" (метод getInfo без параметров) 
Он делает два вызова внешних API:
Вызов по ссылке (https://randomuser.me/api/) и сохраняет в базу поля:      gender, firstName, lastName, city, street, email, picture medium
Вызов по ссылке (https://geek-jokes.sameerkumar.website/api) и дополняет полученные данные полем quote 

2. После этого модуль "People" вызывает модуль "Poems" (метод getPoem, передавая ему идентификатор скачанного профиля)

3. Модуль "Poems" делает:
Вызов внешнего API по ссылке (https://www.poemist.com/api/v1/randompoems) и сохраняет полученную поэму в базу
После получения поэмы происходит ее оценка: каждое следующее предложение сравнивается с предыдущим по алгоритму Jaro–Winkler distance (реализацию можно легко нагуглить). 
Посчитанное значение сохраняется в поле distance
 
Сценарий "Download report":	

1. происходит вызов модуля "Reports" (getReport)
2. модуль "Reports" делает обращение к модулям "People", "Poems" за необходимыми ему данными
3. формирует отчет в требуемом формате и возвращает его модулю "Web Site"
4. модуль "Web Site" после получения сформированного отчета отдает его странице сайта на скачивание.
