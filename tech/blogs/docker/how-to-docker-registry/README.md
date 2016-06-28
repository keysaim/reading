# Docker registry搭建

## 简介

## 搭建local的registry

## 搭建基于domain的registry
假定Docker registry采用的域名为`myregistrydomain.com`，且本地有名为`redis`的docker image, 通过配置基于domain的registry，希望将本地的`redis`上传到搭建好的docker registry。

### 基于自签名(Self-Signed)证书的registry

* 生成自签名的证书
    ```sh
    mkdir -p certs
    openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt
    ```
    ***请务必在提示输入CN(Common Name)的时候，输入正确的域名，这里是`myregistrydomain.com`***，否则，证书将是无效的。

* 采用TLS启动docker registry
    ```sh
    docker run -d -p 5000:5000 --restart=always --name registry \
        -v `pwd`/certs:/certs \
        -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
        -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
        registry:2
    ```

* 查看下是否有错
    ```
    # docker logs registry
    ```
    正常情况下，应该是没有错误信息的。

* 让Docker信任此自签名证书
    ```sh
    cp certs/domain.crt /etc/docker/certs.d/myregistrydomain.com:5000/ca.crt
    ```

* 重启Docker
    ```
    service restart docker
    ```

* 测试是否有效
    ```sh
    docker tag redis myregistrydomain.com:5000/redis
    docker push myregistrydomain.com:5000/redis
    ```
    正常情况下，应该可以看到image被上传了，否则则会提示出错信息。
    
