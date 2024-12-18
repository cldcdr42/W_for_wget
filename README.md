# Работает при условии, что config и join файлы содержат актуальную информацию

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
#        ПОМЕНЯТЬ "user" "password" "mydb"
#             - name: POSTGRES_USER
#              value: "user"
#            - name: POSTGRES_PASSWORD
#              value: "password"
#            - name: POSTGRES_DB
#              value: "mydb"
#          ПОМЕНЯТЬ НА НУЖНУЮ ВЕРСИЮ
#              image: postgres:17
#                 ИЛИ
#               image: postgres:13
#                 ИЛИ
#               ту, которую использовали вы

kubectl apply -f postgresql-deployment.yml -n ns-32
kubectl apply -f postgresql-service.yml -n ns-32
kubectl get pods -n ns-32
```
Проверка работы (<pod-name> <username> <database> заменить на свои)
```
kubectl get pods -n ns-32
kubectl port-forward pod/<pod-name> 5432:5432 -n ns-32 &
psql -h localhost -p 5432 -U <username> -d <database>
```
