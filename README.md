
## Kubernetes とは？
Kubernetes (クーバーネイティス)とは、Dockerなどのコンテナ仮想化ソフトウェアを管理、および自動化するためのコンテナオーケストレーション
コンテナを水平スケールさせたり、ローリングアップデートやロールバックが可能となる

## 主なリソース

#### ワークロード
- Podとは
・・・ 最小単位、同一環境で動作するDockerコンテナの集合
１つのPodには複数のコンテナを設定できる。

- ReplicaSetとは
・・・Podの集合、Podをスケールできる。

- Deploymentとは
・・・ReplicaSetの集合、ReplicaSetの世代管理ができる

- StatefulSetとは
  ・・・Podの集合、Podをスケールする際の名前が一定

#### サービス
- Service
・・・外部公開、名前解決、L4ロードバランサー

- Ingressとは
  ・・・外部公開、L7ロードバランサー

#### 設定
- ConfigMapとは
・・・Kubernetes上で利用する設定情報

- Secretとは
・・・Kubernetes上で利用する機微情報

#### ストレージ
- PersistentVolume（PV）とは
  ・・・永続データの実態

- PersistentVolumeCliam（PVC）とは
・・・永続データの要求




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
NAME                       READY   STATUS    RESTARTS      AGE
backend-6dcf857cbf-mvnvh   1/1     Running   1 (15s ago)   23s
frontend-78b6f79b-vxh69    1/1     Running   0             23s
mysql-5b57df4c9b-mmcxn     1/1     Running   0             23s
# サービスの確認
$ kubectl get service
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
backend      ClusterIP   10.101.207.133   <none>        9000/TCP   2m1s
frontend     ClusterIP   10.103.119.123   <none>        3000/TCP   2m1s
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP    12h
mysql        ClusterIP   10.99.160.7      <none>        3306/TCP   2m1s
# クラスタの外からアクセスできるようにポートフォワード
$ kubectl port-forward frontend-78b6f79b-vxh69 3000:3000
$ kubectl port-forward backend-6dcf857cbf-mvnvh 9000:9000
$ open http://localhost:3000
# リソースの削除
$ kubectl delete -f .

```


## 参照

- [Kubernetes リファレンス](https://kubernetes.io/ja/docs/reference/)

## Licence

[MIT](https://github.com/isystk/kubernetes-sample/LICENCE)

## Author

[isystk](https://github.com/isystk)
