![](../imgs/k8s/202011071500.jpeg)
#### K8S中遇到的问题
<hr>

##### 1. error execution phase kubelet-start: error uploading crisocket: timed out waiting for the condition
问题来源：在子节点执行 kubeadm join 命令后返回上载 crisocket 超时报错
解决方法：
```python
[root@k8s-node-01 ~]$ swapoff -a 
[root@k8s-node-01 ~]$ kubeadm reset
[root@k8s-node-01 ~]$ systemctl daemon-reload
[root@k8s-node-01 ~]$ systemctl restart kubelet
[root@k8s-node-01 ~]$ iptables -F && iptables -t nat -F && iptables -t mangle -F && iptables -X
```
以上执行完成后再执行加入子结点的操作：
```python
[root@k8s-node-01 ~]$ kubeadm join 10.0.0.2:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:23ffa46116c302c99714ddc4f4a679753ccef1a7eabddf035014fbc09673cd7a
```
<hr>

##### 2. error execution phase preflight: couldn’t validate the identity of the API Server: abort connecting to API servers after timeout of 5m0s
问题来源：在子节点上执行 kubeadm join 命令后返回 <font>连接 API 服务器超时</font> 5分钟后终止连接的报错
问题原因：token过期
解决方法：在 <font>master 主机上重新生成 token</font>，子结点执行 kubeadm join 命令的 --token 参数要换成新的 token
```python
# master主机重新生成token
[root@k8s-master-01 ~]$ kubeadm token create
pmafdi.6c2g8u0r4q50pw8g
# 子节点使用新的token
[root@k8s-node-01 ~]$ kubeadm join 10.0.0.2:6443 --token pmafdi.6c2g8u0r4q50pw8g --discovery-token-ca-cert-hash sha256:23ffa46116c302c99714ddc4f4a679753ccef1a7eabddf035014fbc09673cd7a
# master主机查看状态，已经正常
[root@k8s-master-01 ~]$ kubectl get nodes 
NAME            STATUS   ROLES    AGE     VERSION
k8s-master-01   Ready    master   9h      v1.15.1
k8s-node-01     Ready    <none>   8h      v1.15.1
```
<hr>

##### 3. Error from server (AlreadyExists): deployments.apps "nginx-deployment" alrea
问题来源：K8S集群中删除一个 pod
问题原因：--replicas 的设置，删除了会立即生成新pob的代替之前的pob
错解：发现因为 --replicas 的设置，如果是按照下面的方法，pod一直是删除不了的：
```python
[root@k8s-master-01 ~]$ kubectl get pod
NAME                               READY   STATUS             RESTARTS   AGE
nginx-deployment-85756b779-wmrnl   0/1     ImagePullBackOff   0          26s
[root@k8s-master-01 ~]$ kubectl delete pod nginx-deployment-85756b779-wmrnl
pod "nginx-deployment-85756b779-wmrnl" deleted
```
解决方法：使用下面的命令
```python
[root@k8s-master-01 ~]$ kubectl delete deployment nginx-deployment
deployment.extensions "nginx-deployment" deleted
[root@k8s-master-01 ~]$ kubectl get rs
No resources found.
```