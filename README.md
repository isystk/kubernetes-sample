
## サンプルアプリケーションの作成

```shell
# docker-composeで起動確認
$ docker-compose -f docker/docker-compose.yml up -d
$ curl http://localhost:9000
# docker-composeで停止
$ docker-compose -f docker/docker-compose.yml down



# Dockerイメージを作成する
$ docker build -t debug .
# 作成したDockerコンテナにログインする
$ docker run -it debug sh
exit
# Podを作成する
$ kubectl apply -f debug-pod.yml
# 作成したPodを確認する
$ kubectl get pod
NAME    READY   STATUS              RESTARTS        AGE
debug   0/1     ErrImageNeverPull   0               69s
# Dockerコンテナにログインする


```



## 参照

- [Kubernetes リファレンス](https://kubernetes.io/ja/docs/reference/)

## Licence

[MIT](https://github.com/isystk/kubernetes-sample/LICENCE)

## Author

[isystk](https://github.com/isystk)
