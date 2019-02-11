# registry


## 1. set basic auth 

```sh
$ mkdir auth
$ docker run --entrypoint htpasswd registry:2 -Bbn regadmin regpwd@123 > auth/htpasswd
$ sudo cp -a auth/ /mnt/registry_data/
```

```sh
kubectl create secret docker-registry myregistrykey --docker-server=registry.myhost.io --docker-username=regadmin --docker-password=regpwd@
123 --docker-email=reg@myhost.io
```