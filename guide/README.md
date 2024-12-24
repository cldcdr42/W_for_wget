 # После Hello-world
 На основе моего хода решения:

1) Создадим папки, куда будем хранить файлы
 
  ```
  cd ~ && mkdir app && cd app
  ```

2) скачать конфигурации базы данных и приложения для запуска в кластере
  
  ```
  wget -O postgres.yml https://shorturl.at/oHfxZ
  wget -O python.yml https://shorturl.at/iqxAS
  ```

3) #ОБЯЗАТЕЛЬНО заменить неймспейсы на свои  
   В двух местах в файле postgres.yml  
   ```
   nano postgres.yml
   ```
   Найти строчки namespace: ns-32  
   Заменить на свои  
   Когда закончили менять, нажмите CTRL+X, затем y (от англиского yes) и Enter    
  
   В двух местах в файле python.yml  
   ```
   nano python.yml
   ```
   Найти строчки namespace: ns-32   
   Заменить на свои  
   Когда закончили менять, нажмите CTRL+X, затем y (от англиского yes) и Enter  

4) Выполнить деплой данных конфигураций. Вместе ns-<xx> указать свой неймспей. Например, ns-57
   ```
   kubectl apply -f postgres.yml -n ns-<xx>
   kubectl apply -f python.yml -n ns-<xx>
   ```
   Рекомендую сначала дождаться запуска (Running) БД, затем - начинать команду запуска приложения

5) Дождаться момента, когда все поды получат статус Running (ns-<xx> заменить на свой)
   ```
   kubectl get pods -n ns-<xx>
   ```

6) Когда все готово, можно демонстрировать работу программы. Для этого
   Прокидываем порты с локальной машины (nx-<xx> заменить на свой)
   ```
   kubectl port-forward svc/python-app-svc 5000:5000 -n ns-<xx> &
   ```
   И выполняем запрос. В скобках '{"number": 1}' вместо 1 можно выбрать любое натуральное число от 1 до 100 [1;99]
   ```
   curl -X POST -H "Content-Type: application/json" -d '{"number": 1}' http://localhost:5000/process
   ```
   В случае успехом должно появиться сообщение (Если ввели число X)
   ```
   "Response: {X + 1}. Number {X} has been added to the database."
   ```
   Если до демонстрации уже добавляли числа, возможны два других ответа:
   ```
   Number {X} is already in the database (case 1)
   ```
   ИЛИ
   ```
   Number {num + 1} is already in the database (case 2)
   ```
   Если ответа нет в принципе, то все плохо
   Этого достаточно для получения галочек ENV, Docker, REST, kube, db (5 штук)

