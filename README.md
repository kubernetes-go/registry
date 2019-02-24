# registry

## 1. generate basic auth

```sh
$ mkdir auth
$ docker run --entrypoint htpasswd registry:2 -Bbn admin admin@123 > auth/htpasswd
$ sudo cp -a auth/ /mnt/registry_data/
```

## 2. generate domain self-siged certificate and private key

```sh
# please replace 'registry.myhost.io' by your customize local domain.
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout registry-domain.key -out registry-domain.crt -subj "/CN=registry.myhost.home/O=registry.myhost.home"
```

## 3. add the cert to Kubernetes

```sh
$ kubectl create secret tls registry-tls-secret --key registry-domain.key --cert registry-domain.crt
```

## 4. depoy service and config ingress

### IMPORTANT

Please install [kubernetes-go/nginx-ingress](https://github.com/kubernetes-go/nginx-ingress) before the next steps.

### 4.1 git clone this repo and run at root folder

```sh
$ kubectl apply -f registry.oneclick.yaml
```

### 4.2 install from raw

```sh
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-go/registry/master/registry.oneclick.yaml
```


## 5. access from local

### Ubuntu

```sh
# copy to crt folder
$ sudo cp registry-domain.crt /usr/local/share/ca-certificates/registry.myhost.home.crt
# install crt and focre freshed
$ sudo update-ca-certificates --fresh
# restart docker daemon
$ sudo systemctl restart docker

$ docker login 
# if here is an error: Error saving credentials: error storing credentials - err: exit status 1, out: `Cannot autolaunch D-Bus without X11 $DISPLAY`
# please run the following bash to remove  golang-docker-credential-helpers
# $ sudo apt-get remove golang-docker-credential-helpers
# and login agagin 
$ docker login
# if you neew docker compose ,please reinstall it.
# sudo apt-get install docker-compose
```


## 6. Test it in Kubernetes

### 6.1 docker build a sample image

```sh
$ cd sample-asp-net-core-webapi
$ docker build . -t registry.myhost.home/sample-asp-net-core-webapi:1.0
$ docker push registry.myhost.home/sample-asp-net-core-webapi:1.0
```

### 6.2 try to deploy the image to Kubernetes

#### set docker registry auth for Kubernetes


```sh
$ kubectl create secret docker-registry regcred --docker-server=registry.myhost.home --docker-username=admin --docker-password=admin@123 --docker-email=admin@myhost.home
```

#### kubectl apply

```sh
$ kubectl apply -f test-deployment.yaml
```