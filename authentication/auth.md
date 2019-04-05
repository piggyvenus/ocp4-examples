## htpasswd Atuhentication

0. oc login -u kubeadmin -p <your admin password>

1. create 2 users for test
printf "admin:$(openssl passwd -apr1 redhatocp4)\n" >> htpasswd
printf "testuser1:$(openssl passwd -apr1 openshift4)\n" >> htpasswd
you should see a file call htpaswd created.

2. oc create secret generic htpass-secret --from-file=htpasswd=htpasswd -n openshift-config

3. oc apply -f https://raw.githubusercontent.com/openshift/training/master/assets/htpasswd-cr.yaml

4. Login as one of the new user to test
oc login -u testuser1 -p openshift4
