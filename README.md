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
