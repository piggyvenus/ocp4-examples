## Migration Test Notes

### Environment
- OCP 4.3.1
- CAM 1.1 (non OLM)



Non OLM:

instruction: https://github.com/fusor/mig-operator/blob/master/docs/usage/1.md

YAMLs to use:
For both source and destination:
https://raw.githubusercontent.com/fusor/mig-operator/master/deploy/non-olm/v1.1.0/operator.yml

OCP 4
https://raw.githubusercontent.com/fusor/mig-operator/master/deploy/non-olm/v1.1.0/controller-4.yml

OCP 3
https://raw.githubusercontent.com/fusor/mig-operator/master/deploy/non-olm/v1.1.0/controller-3.yml

Tips:
- Use cluster admin token for migration from the source
- oc adm policy add-cluster-role-to-user cluster-admin system:serviceaccount:openshift-migration:migration-controller
