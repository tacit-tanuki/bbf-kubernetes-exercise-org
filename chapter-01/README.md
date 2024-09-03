# Chapter 01

## 1.2.5 準備：Docker環境をつくる
- [Install Docker Engine](https://docs.docker.com/engine/install/)を参照して、Dockerをインストールする

## 1.2.6 コンテナを起動してみる
- nginxコンテナを実行する
```
❯ docker run --rm --detach --publish 8080:80 --name web nginx:1.25.3
Unable to find image 'nginx:1.25.3' locally
1.25.3: Pulling from library/nginx
e1caac4eb9d2: Pull complete
504c1e01744e: Pull complete
a1330b43d726: Pull complete
5e8995dba715: Pull complete
d5181593591e: Pull complete
74a4f808141d: Pull complete
330fd09f4306: Pull complete
Digest: sha256:c7a6ad68be85142c7fe1089e48faa1e7c7166a194caa9180ddea66345876b9d2
Status: Downloaded newer image for nginx:1.25.3
325f1d0642861c53b2552b1a6ada67682e1d0c2b4871d55ab6258a46e5c32c73
```

- nginxコンテナにアクセスする
```
❯ curl http://localhost:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

## 1.2.9 Dockerイメージをビルドする
- カスタマイズしたnginxイメージをビルドする
```
❯ docker build ./chapter-01/custom-nginx --tag nginx-custom:1.0.0
[+] Building 1.0s (7/7) FINISHED                                                                  docker:default
 => [internal] load build definition from Dockerfile                                                        0.1s
 => => transferring dockerfile: 151B                                                                        0.0s
 => [internal] load metadata for docker.io/library/nginx:1.25.3                                             0.0s
 => [internal] load .dockerignore                                                                           0.1s
 => => transferring context: 2B                                                                             0.0s
 => [internal] load build context                                                                           0.2s
 => => transferring context: 117B                                                                           0.0s
 => [1/2] FROM docker.io/library/nginx:1.25.3                                                               0.4s
 => [2/2] COPY index.html /usr/share/nginx/html                                                             0.1s
 => exporting to image                                                                                      0.1s
 => => exporting layers                                                                                     0.1s
 => => writing image sha256:cc6bf65fbe5ae7cfb8e803be08896ef8d8b3960bcb57d3ddf5c7b771478ce72c                0.0s
 => => naming to docker.io/library/nginx-custom:1.0.0                                                       0.0s
```

- カスタマイズしたnginxイメージを確認する
```
❯ docker images
REPOSITORY     TAG       IMAGE ID       CREATED         SIZE
nginx-custom   1.0.0     cc6bf65fbe5a   2 minutes ago   187MB
nginx          1.25.3    247f7abff9f7   10 months ago   187MB
```

## 1.2.10 自作のDockerイメージからコンテナを起動する
- 起動中のnginxコンテナを停止する
```
❯ docker images
REPOSITORY     TAG       IMAGE ID       CREATED         SIZE
nginx-custom   1.0.0     cc6bf65fbe5a   2 minutes ago   187MB
nginx          1.25.3    247f7abff9f7   10 months ago   187MB

❯ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                  NAMES
325f1d064286   nginx:1.25.3   "/docker-entrypoint.…"   20 minutes ago   Up 20 minutes   0.0.0.0:8080->80/tcp   web

❯ docker stop 325f1d064286
325f1d064286

❯ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

- カスタムnginxコンテナを起動する
```
❯ docker run --rm --detach --publish 8080:80 --name web nginx-custom:1.0.0
4742a4a2e4b6588d455e187b396e971bfff6008745cf8fe0bc1333671f48b266

❯ docker ps
CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS          PORTS                  NAMES
4742a4a2e4b6   nginx-custom:1.0.0   "/docker-entrypoint.…"   35 seconds ago   Up 34 seconds   0.0.0.0:8080->80/tcp   web

❯ curl http://localhost:8080
<h1>Hello World!</h1>

❯ docker stop web
web

❯ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

## 1.3 つくる 自作http serverコンテナを起動する
```
❯ docker build chapter-01/hello-server --tag hello-server:1.0
[+] Building 135.5s (10/10) FINISHED                                                              docker:default
 => [internal] load build definition from Dockerfile                                                        0.1s
 => => transferring dockerfile: 256B                                                                        0.0s
 => [internal] load metadata for docker.io/library/golang:1.21                                              2.7s
 => [internal] load .dockerignore                                                                           0.1s
 => => transferring context: 2B                                                                             0.0s
 => [builder 1/4] FROM docker.io/library/golang:1.21@sha256:4746d26432a9117a5f58e95cb9f954ddf0de128e9d58  109.1s
 => => resolve docker.io/library/golang:1.21@sha256:4746d26432a9117a5f58e95cb9f954ddf0de128e9d581688651419  0.0s
 => => sha256:e6ba96dde4af9f4e06caba475fa65e94e4c54fe4aa3e9f4f504c02178eb8934e 2.32kB / 2.32kB              0.0s
 => => sha256:246ea1ed9cdb1164bb5cb7e1f45d7914b98c6d9418c8e1cc443105e820bbd9d1 2.82kB / 2.82kB              0.0s
 => => sha256:4746d26432a9117a5f58e95cb9f954ddf0de128e9d5816886514199316e4a2fb 9.75kB / 9.75kB              0.0s
 => => sha256:903681d87777d28dc56866a07a2774c3fd5bf65fd734b24c9d0ecd9a13c9f636 49.55MB / 49.55MB           26.1s
 => => sha256:3cbbe86a28c2f6b3c3e0e8c6dcfba369e1ea656cf8daf69be789e0fe2105982b 24.05MB / 24.05MB           10.8s
 => => sha256:6ed93aa58a52c9abc1ee472f1ac74b73d3adcccc2c30744498fd5f14f3f5d22c 64.14MB / 64.14MB           22.6s
 => => sha256:1f46bd02dde39f0741d70614fc607bf03c1a0cd60d52e7dd06c44f8fb9358709 92.23MB / 92.23MB           42.0s
 => => sha256:54bf7053e2d96c2c7f4637ad7580bd64345b3c9fabb163e1fdb8894aea8a9af0 67.01MB / 67.01MB           43.4s
 => => sha256:532d43a0bc41875119c835fd75616e87d5df2a3714ddea3a0b5a68c7c5982649 126B / 126B                 26.4s
 => => sha256:4f4fb700ef54461cfa02571ae0db9a0dc1e0cdb5577484a6d75e68dc38e8acc1 32B / 32B                   26.8s
 => => extracting sha256:903681d87777d28dc56866a07a2774c3fd5bf65fd734b24c9d0ecd9a13c9f636                  21.0s
 => => extracting sha256:3cbbe86a28c2f6b3c3e0e8c6dcfba369e1ea656cf8daf69be789e0fe2105982b                   3.1s
 => => extracting sha256:6ed93aa58a52c9abc1ee472f1ac74b73d3adcccc2c30744498fd5f14f3f5d22c                  14.6s
 => => extracting sha256:1f46bd02dde39f0741d70614fc607bf03c1a0cd60d52e7dd06c44f8fb9358709                  12.2s
 => => extracting sha256:54bf7053e2d96c2c7f4637ad7580bd64345b3c9fabb163e1fdb8894aea8a9af0                  28.1s
 => => extracting sha256:532d43a0bc41875119c835fd75616e87d5df2a3714ddea3a0b5a68c7c5982649                   0.0s
 => => extracting sha256:4f4fb700ef54461cfa02571ae0db9a0dc1e0cdb5577484a6d75e68dc38e8acc1                   0.0s
 => [internal] load build context                                                                           0.1s
 => => transferring context: 788B                                                                           0.0s
 => [builder 2/4] WORKDIR /app                                                                              2.8s
 => [builder 3/4] COPY . .                                                                                  0.1s
 => [builder 4/4] RUN go build -o hello .                                                                  20.0s
 => [stage-1 1/1] COPY --from=builder /app/hello /hello                                                     0.2s
 => exporting to image                                                                                      0.2s
 => => exporting layers                                                                                     0.1s
 => => writing image sha256:5cdc29a3373711aaaecb8a600b14e2fe8639b4a2a69b54499c6385377fa36ec2                0.0s
 => => naming to docker.io/library/hello-server:1.0                                                         0.0s

❯ docker images hello-server
REPOSITORY     TAG       IMAGE ID       CREATED              SIZE
hello-server   1.0       5cdc29a33737   About a minute ago   6.72MB
```

```
❯ docker run --rm --detach --publish 8080:8080 --name hello-server hello-server:1.0
9c365bc3f5589fa96501aa913f6f1d411f12a849b27c88b9a453edc4f84a1ca8

❯ curl localhost:8080
hello-server

❯ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```