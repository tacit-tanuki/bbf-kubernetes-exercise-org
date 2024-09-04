# Chapter 02

## 2.2 つくって、直す Kubernetesクラスタを構築して消す
### kubectlをインストールする
```
❯ which kubectl
~/.local/bin/kubectl
```

### kindをインストールする
```
❯ which kind
~/.local/bin/kind
```

### Kubernetesクラスタを構築する
```
❯ kind create cluster --image=kindest/node:v1.29.0
Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.29.0) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind

Not sure what to do next? 😅  Check out https://kind.sigs.k8s.io/docs/user/quick-start/

❯ kubectl cluster-info --context kind-kind
Kubernetes control plane is running at https://127.0.0.1:42089
CoreDNS is running at https://127.0.0.1:42089/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
                                                                                                                 To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

### Kubernetesクラスタを消す
```
❯ kind delete cluster
Deleting cluster "kind" ...
Deleted nodes: ["kind-control-plane"]
```




