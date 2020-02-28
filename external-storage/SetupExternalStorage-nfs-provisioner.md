# Setting up nfs-provisioner with NFS

## Git
1. git clone git clone https://github.com/kubernetes-incubator/external-storage.git

## Setup nfs-provisioner on OCP
1. oc new-project nfs
2. cd external-storage/nfs
3. Edit deploy/kubernetes/deployment.yaml with the following changes:
```
volumes:
  - name: export-volume
    hostPath:
      path: /tmp/nfs-provisioner
```
and

  ```
  securityContext:
    capabilities:
      add:
        - DAC_READ_SEARCH
        - SYS_RESOURCE
    privileged: true

  ```

  IF you log of the nfs-provisioner is getting permissions denied error from the pod, you did not set the securityContext with `privileged: true`

4. kubectl create -f deploy/kubernetes/deployment.yaml
5. kubectl create -f deploy/kubernetes/class.yaml
6. Giving cluster-admin to the SA

  ```
  oc adm policy add-scc-to-user privileged -z nfs-provisioner -n nfs
  oc adm policy add-cluster-role-to-user cluster-admin system:serviceaccount:nfs:nfs-provisioner

  ```
7. Set nfs-provisioner as default storage storageclass

  ```
  oc patch storageclass example-nfs -p '{"metadata":{"annotations":{"storageclass.beta.kubernetes.io/is-default-class":"true"}}}'
  ```

8. On the NFS server:

  ```
  mkdir -p /exports/nfs
  chown -R nfsnobody:nfsnobody /exports/nfs
  chmod 777 -R /exports/nfs
  ```
