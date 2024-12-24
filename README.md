# Работает при условии, что config и join файлы содержат актуальную информацию

# Join Ноды
1) создать папку
2) скачать конфиг
3) поменять права доступа к конфигу (https://tinyurl.com/3h9zfbws)
4) выполнить джоин кластера (https://tinyurl.com/2puhekd2)
5) проверить статус своей и других нод

 ||      ||  
 ||      ||    
Отключиться и удалить конфиг

```
sudo kubeadm reset
rm $HOME/.kube/config


sudo apt install ipvsadm
sudo ipvsadm --clear

sudo rm -rf /etc/cni/net.d/*flannel*
sudo ip link delete flannel.1
```

Скачать новый конфиг и подключиться
```
mkdir -p $HOME/.kube 
wget -O $HOME/.kube/config https://tinyurl.com/3h9zfbws
sudo chown $(id -u):$(id -g) $HOME/.kube/config

wget -O $HOME/.kube/join https://tinyurl.com/2puhekd2
chmod +x $HOME/.kube/join
$HOME/.kube/join

kubectl get no -o wide 
```
Создать свой неймспейс (00 заменить на нужный номер)

```
kubectl create namespace ns-00
```

# Поднять бд postgresql
БД поднялась самостоятельно и без ошибок
```
cd ~
mkdir app
cd app
wget -O ./postgresql-deployment.yml https://raw.githubusercontent.com/cldcdr42/W_for_wget/refs/heads/main/dep
wget -O ./postgresql-service.yml https://raw.githubusercontent.com/cldcdr42/W_for_wget/refs/heads/main/ser

nano postgresql-deployment.yml
```
ПОМЕНЯТЬ "user" "password" "mydb"  
- name: POSTGRES_USER  
value: "user"  
- name: POSTGRES_PASSWORD  
value: "password"  
- name: POSTGRES_DB  
value: "mydb"  

ПОМЕНЯТЬ НА НУЖНУЮ ВЕРСИЮ  
image: postgres:17  
   ИЛИ  
image: postgres:13  
   ИЛИ  
ту, которую использовали вы  

-----------------

 NAMESPACE ЗАМЕНИТЬ НА СВОЙ  


```
kubectl apply -f postgresql-deployment.yml -n ns-00
kubectl apply -f postgresql-service.yml -n ns-00
kubectl get pods -n ns-00
```
Проверка работы (ns-00, <pod-name> <username> <database> заменить на свои)
```
kubectl get pods -n ns-00
kubectl port-forward pod/<pod-name> 5432:5432 -n ns-00 &

# sudo apt install postgresql-client -y
# if needed
#

psql -h localhost -p 5432 -U <username> -d <database>
```


# Additional info
which node runs my deploy
```
kubectl get pods -n ns-00 -l app=postgres -o wide
```

kill port forward
```
ps aux | grep "kubectl port-forward"
# find line with port-forward and 5432
kill <PID>
```

Get nodes, status, ip
```
kubectl get nodes -o wide
```

Logs about a node
```
kubectl get pod -n ns-00

# less info
kubectl logs <pod-name> -n ns-00

# more info
kubectl describe pod <pod-name> -n ns-00
```

Delete pod
```
kubectl delete -f file.yml -n ns-00
```
or
```
kubectl get pods -n ns-00
kubectl delete pod <name_of_the_pod> -n ns-00
```

# When the app is running
```
kubectl port-forward svc/python-app 5000:5000 -n ns-32 &
curl -X POST -H "Content-Type: application/json" -d '{"number": 1}' http://localhost:5000/process
```

access bash from a container
```
kubectl exec -it <pod-name> -n ns-32 -- bash
```

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

3) ОБЯЗАТЕЛЬНО заменить неймспейсы на свои  
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
