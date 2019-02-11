# registry


## 1. install cert-manager

Please check out the link [Installing cert-manager](https://docs.cert-manager.io/en/latest/getting-started/install.html)

## 2. https://robertbrem.github.io/Microservices_with_Kubernetes/03_Docker_registry/01_Setup_a_docker_registry/

## 3. munal 


```sh
$ mkdir -p registry/{images,certs,auth}
```

```sh
$ cd registry
$ openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt
```