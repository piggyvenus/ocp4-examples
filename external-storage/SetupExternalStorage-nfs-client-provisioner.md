# Setting up nfs-client-provisioner with NFS


1. git clone git clone https://github.com/kubernetes-incubator/external-storage.git
2. cd nfs-client
3. replace all "default" namespace to nfs-client
4. oc create -f deploy/rbac.yaml
5. oc adm policy add-scc-to-user hostmount-anyuid system:serviceaccount:$NAMESPACE:nfs-client-provisioner
6. Update deploy/deployment.yaml with your values for NFS_SERVER, NFS_PATH and PROVISIONER_NAME
7. Update `provisioner` to the same value of PROVISIONER_NAME in step #6
8. oc create -f deploy/deployment.yaml
9. oc create -f deploy/class.yaml
10. Run `oc get pods`, you should see the nfs-client-provisioner pod running
11. create a test pvc

  ```
  kubectl create -f deploy/test-claim.yaml -f deploy/test-pod.yaml
```
Reference: https://github.com/kubernetes-incubator/external-storage/tree/master/nfs-client
