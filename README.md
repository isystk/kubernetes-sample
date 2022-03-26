

## Kubernetes を利用する為の環境構築
```
# 必要なコマンドをインストールする
$ brew install minikube kompose docker
# Dockerを利用してminikubeを起動します。
$ minikube start --vm-driver=docker
# Minikubeの中にSSHします。（以降この中でKubernetesが動くようになります。）
$ minikube ssh
exit
# IPを確認する
$ minikube ip
# ダッシュボードを表示します。
$ minikube dashboard
# Minikubeを停止する
$ minikube stop
# Minikubeを削除する
$ minikube delete
```


## サンプルアプリケーションの作成

```shell
# minikube VM上のdockerを使うよう設定
$ eval $(minikube docker-env)
# docker にログイン
$ docker login
# docker-composeで起動確認
$ docker-compose -f docker/docker-compose.yml up -d
$ minikube ssh
curl http://localhost:9000
Hello World!
exit
# docker-composeで停止
$ docker-compose -f docker/docker-compose.yml down
# 作成されたdocker イメージを確認
$ docker images | grep k8s-sample

# docker-compose.yml を k8s で利用できる形に変換する
$ cd docker
$ kompose convert
$ mv *.yaml ..
$ cd ..

# リソースの作成
$ kubectl apply -f .
# リソースの確認
$ kubectl get pod
NAME                       READY   STATUS    RESTARTS     AGE
backend-6dcf857cbf-hpqrx   1/1     Running   1 (2s ago)   13s
mysql-5b57df4c9b-ldgmt     1/1     Running   0            13s
# サービスの確認
$ kubectl get service
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
backend      ClusterIP   10.104.138.21   <none>        9000/TCP   4m22s
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP    20m
mysql        ClusterIP   10.98.17.227    <none>        3306/TCP   4m22s
# クラスタの外からアクセスできるようにポートフォワード
$ kubectl port-forward backend-6dcf857cbf-hpqrx 9000:9000
$ curl http://localhost:9000
Hello World!
# リソースの削除
$ kubectl delete -f .

```


## 参照

- [Kubernetes リファレンス](https://kubernetes.io/ja/docs/reference/)

## Licence

[MIT](https://github.com/isystk/kubernetes-sample/LICENCE)

## Author

[isystk](https://github.com/isystk)
