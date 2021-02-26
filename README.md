# Kubernetes サンプル

## Description

### Podとは
最小単位、同一環境で動作するDockerコンテナの集合
１つのPodには複数のコンテナを設定できる。

### ReplicaSetとは
Podの集合、Podをスケールできる。

## Demo

## VS.

## Requirement

## Usage

Kubernetes のインストールと起動
```
brew install minikube
minikube start
```

Hello World
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

リソースの作成・確認・削除
```
# リソースの作成
kubectl apply -f hello-world.yml
# リソースの確認
kubectl get all
# リソースの削除
kubectl delete -f hello-world.yml
```

Nginxとdebug用(CentOS7)のPodを作成
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

Podとホスト間のファイル転送
```
# ホストからPod側にファイルを転送
kubectl cp ./sample.txt debug:/var/tmp/
# Pod側からホストにファイルを転送
kubectl cp debug:/var/tmp/sample.txt ./
```

ホストとPod間のストレージを共有
```
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
```

# kompose のインストール （docker-composeみたいなもの）
curl -L https://github.com/kubernetes/kompose/releases/download/v1.22.0/kompose-darwin-amd64 -o kompose
chmod +x kompose
sudo mv ./kompose /usr/local/bin/kompose


サンプルのdockerイメージを作成
```
docker build -t debug .
docker run -it debug sh
----
mongo
exit
----
docker container prune
```

サンプルのpodを作成
```
kubectl apply -f debug-pod.yml
kubectl get pod
-----
NAME    READY   STATUS              RESTARTS   AGE
debug   0/1     ErrImageNeverPull   0          89s
-----
kubectl exec -it debug sh
```

## Install

## Contribution

### Kubernetes リファレンス
https://kubernetes.io/ja/docs/reference/

## Licence

[MIT](https://github.com/isystk/kubernetes-sample/LICENCE)

## Author

[isystk](https://github.com/isystk)
