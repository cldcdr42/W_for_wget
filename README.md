# Работает при условии, что config и join файлы содержат актуальную информацию

1) создать папку
2) скачать конфиг
3) поменять права доступа к конфигу (https://tinyurl.com/3h9zfbws)
4) выполнить джоин кластера (https://tinyurl.com/2puhekd2)
5) проверить статус своей и других нод

 ||      ||
 ||      ||
 \/      \/

```
mkdir -p $HOME/.kube 
wget -O $HOME/.kube/config https://tinyurl.com/3h9zfbws
sudo chown $(id -u):$(id -g) $HOME/.kube/config

wget -O $HOME/.kube/join https://tinyurl.com/2puhekd2
chmod +x $HOME/.kube/join
$HOME/.kube/join

 kubectl get no -o wide 
```

" kubeadm join 172.16.1.34:6443 --token 7dh1df.pdu55lsdnv1rq3o9 --discovery-token-ca-cert-hash sha256:3fc8f0fff6496648caadc41407340492dfcb5e55355181d946e22b3820295802"
