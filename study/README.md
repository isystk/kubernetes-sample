
### Hello World
```
# hello-worldイメージの作成と実行
$ docker run hello-world
# hello-worldイメージからpodの作成
$ kubectl run hello-world --image hello-world --restart=Never
# 作成状況の確認
$ kubectl get pod -w
----
NAME          READY   STATUS              RESTARTS   AGE
hello-world   0/1     ContainerCreating   0          5s
hello-world   0/1     Completed           0          15s
----
# 実行ログを確認
$ kubectl logs pod/hello-world
# 作成したpodの削除
$ kubectl delete pod/hello-world
```

### リソースの作成・確認・削除
```
# リソースの作成
$ kubectl apply -f hello-world.yml
# リソースの確認
$ kubectl get all
# リソースの削除
$ kubectl delete -f hello-world.yml
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
Pod はリソースの最小単位
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
ReplicaSet は Pod を管理するリソース。 Pod が停止すると自動的に再起動したりする。
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
Deployment は、 Pod に含まれるコンテナをバージョンアップする際に、ローリングアップデートしたり、履歴の保持やロールバックなどの管理をする。
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

## PV・PVCの動作確認
```
# リソースの作成
kubectl apply -f storage.yml
# リソースの確認
kubectl get pvc,pv
# リソースの削除
kubectl delete -f storage.yml
```
