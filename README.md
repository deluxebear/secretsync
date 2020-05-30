# Secret Sync 功能简介
secret sync 可以用来将 k8s 中的 secret 同步到不同的 namespace（命名空间）。
例如使用 cert-manager 申请的 Let's Encrypt 泛域名证书希望在不同的 namespace 中复用，则可以使用这个方案。（因为 Let's Encrypt 对证书申请是有数量和时间限制的，如果在多个 namespace 中申请泛域名可能会失败）
此方案参考自：https://github.com/IBM-Cloud/kube-samples/tree/master/secret-sync-operator

# 安装方法：
1、部署 deploy 里面的 all-in-one.yaml
```bash
kubectl apply -f all-in-one.yaml
```

# 使用方法：
在需要同步的 secret 的 yaml 文件中的 annotations 段中添加：
```yaml
secretsync.jetems.com/replicate: "true"
secretsync.jetems.com/replicate-to: default/mysecret1,default2/mysecret1
```
test.yaml 完整 yaml 文件：
```yaml
apiVersion: v1
data:
  password: YWRtaW4=
  username: YWRtaW4=
kind: Secret
metadata:
  annotations:
    secretsync.jetems.com/replicate: "true"
    secretsync.jetems.com/replicate-to: default/mysecret3,default/mysecret4
type: Opaque
```
第一行表示启用同步功能，如果使用 "false" 则会停止同步
第二行表示将次 secret 同步到 default 命名空间的 mysecret1 和同步到 default2 命名空间的 mysecret1，这样就会分别在这两个命名空间里面自动创建这两个 secret

# 自己构建 image 的方法
1. 将本代码 git pull 到本地
2. 安装 OPERATOR SDK，可以参考： https://sdk.operatorframework.io/docs/install-operator-sdk/
3. 进入到 代码目录执行：
`operator-sdk build <image name>`
4. 将 all-in-one.yaml 的 image 改成你自己的

