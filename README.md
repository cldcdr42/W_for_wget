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
