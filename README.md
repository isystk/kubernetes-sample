# Kubernetes のお勉強


## リソースの種類

- Podとは
・・・ 最小単位、同一環境で動作するDockerコンテナの集合
１つのPodには複数のコンテナを設定できる。

- ReplicaSetとは
・・・Podの集合、Podをスケールできる。

- Deploymentとは
・・・ReplicaSetの集合、ReplicaSetの世代管理ができる

- Service
・・・外部公開、名前解決、L4ロードバランサー

- ConfigMapとは
・・・Kubernetes上で利用する設定情報

- Secretとは
・・・Kubernetes上で利用する機微情報

- PersistentVolume（PV）・PersistentVolumeCliam（PVC）とは
・・・PVが永続データの実体、PVCが永続データの要求

- StatefulSetとは
・・・Podの集合、Podをスケールする際の名前が一定

- Ingressとは
・・・外部公開、L7ロードバランサー


## Kubernetes のインストールと起動
```
brew install minikube
# HyperKitを利用してminikubeを起動します。
minikube start --vm-driver=hyperkit
# HyperKitの中にSSHします。（以降この中でKubernetesが動くようになります。）
minikube ssh
# HyperKitのIPを確認する
minikube ip
# ダッシュボードを表示します。
minikube dashboard
```

### Hello World
```
# hello-worldイメージの作成と実行
docker run hello-world
# hello-worldイメージからpodの作成
kubectl run hello-world --image hello-world --restart=Never
# 作成状況の確認
kubectl get pod -w
----
NAME          READY   STATUS              RESTARTS   AGE
hello-world   0/1     ContainerCreating   0          5s
hello-world   0/1     Completed           0          15s
----
# 実行ログを確認
kubectl logs pod/hello-world
# 作成したpodの削除
kubectl delete pod/hello-world
```

### リソースの作成・確認・削除
```
# リソースの作成
kubectl apply -f hello-world.yml
# リソースの確認
kubectl get all
# リソースの削除
kubectl delete -f hello-world.yml
```

### Nginxとdebug用(CentOS7)のPodを作成
```
# リソースの作成
kubectl apply -f nginx.yml
# リソースの確認
kubectl get pod -w
# ステータスがRunningにならない場合は状況を確認
kubectl describe pods
# リソースとIPアドレスの確認
kubectl get pod -o wide
# debug PODの中にログイン
kubectl exec -it debug sh
----
# curlでnginxのPODにアクセス
curl 172.17.0.3 
----
# nginxのアクセスログを確認
kubectl logs pod/nginx
# リソースの削除
kubectl delete -f nginx.yml
```

### Podとホスト間のファイル転送
```
# ホストからPod側にファイルを転送
kubectl cp ./sample.txt debug:/var/tmp/
# Pod側からホストにファイルを転送
kubectl cp debug:/var/tmp/sample.txt ./
```

## Podの動作確認
```
# hyperkit にSSH
minikube ssh
---
mkdir storage
echo "hoge" > ./storage/test
---
# リソースの作成
kubectl apply -f sample.yml
# リソースの確認
kubectl get pod -w
# sample PODの中にログイン
kubectl exec -it sample sh
----
# ディレクトリがマウントされていることを確認する
ls /home/nginx
----
# リソースの削除
kubectl delete -f sample.yml
```

## ReplicaSetの動作確認
```
# リソースの作成
kubectl apply -f replicaset.yml
# リソースの確認
kubectl get pod -w
----
NAME          READY   STATUS    RESTARTS   AGE
nginx-797hj   1/1     Running   0          13s
nginx-glt55   1/1     Running   0          13s
----
vi replicaset.yml
----
  replicas: 2 → 3
-----
kubectl apply -f replicaset.yml
# リソースの確認
kubectl get pod -w
----
NAME          READY   STATUS    RESTARTS   AGE
nginx-797hj   1/1     Running   0          115s
nginx-glt55   1/1     Running   0          115s
nginx-x4dq8   1/1     Running   0          3s
----
# リソースの削除
kubectl delete -f replicaset.yml
```


## Deploymentの動作確認
```
# リソースの作成
kubectl apply -f deployment.yml
# リソースの確認
kubectl get all
# デプロイの履歴を確認
kubectl rollout history deploy/nginx
----
REVISION  CHANGE-CAUSE
1         First release.
----
vi deployment.yml
----
  kubernetes.io/change-cause: "Second release."
-----
# 変更を反映
kubectl apply -f deployment.yml
# デプロイの履歴を確認
kubectl rollout history deploy/nginx
----
REVISION  CHANGE-CAUSE
1         First release.
2         Second release.
----
# 変更をロールバック
kubectl rollout undo deploy/nginx
# デプロイの履歴を確認
kubectl rollout history deploy/nginx
----
REVISION  CHANGE-CAUSE
2         Second release.
3         First release.
----
# リソースの削除
kubectl delete -f deployment.yml
```


## Serviceの動作確認
```
# リソースの作成
kubectl apply -f service.yml
# リソースの確認
kubectl get pod -w
# HyperKitのIPを確認する
minikube ip
# ホストからNginxにアクセスする
curl http://192.168.64.2:30000/
# リソースの削除
kubectl delete -f service.yml
```


## 参照

### Kubernetes リファレンス
https://kubernetes.io/ja/docs/reference/

## Licence

[MIT](https://github.com/isystk/kubernetes-sample/LICENCE)

## Author

[isystk](https://github.com/isystk)
