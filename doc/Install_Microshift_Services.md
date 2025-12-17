
## Install Microshift Services  

Install Openshift Console
```bash
oc apply -k microshift_install/services/openshift-console
oc get pods -n openshift-console
```

Get URL from Openshift Console
```bash
echo "http://$(oc get route openshift-console -n openshift-console -o jsonpath='{.spec.host}')"
```

Install ArgoCD
```bash
oc apply -k microshift_install/services/argo/argo_install/
oc get pods -n argocd
```

Get Admin Password from ArgoCD  
```bash
oc get secret argocd-initial-admin-secret -n argocd -o jsonpath={.data.password} | base64 -d
```


Get URL from ArgoCD
```bash
echo "http://$(oc get route argocd-server -n argocd -o jsonpath='{.spec.host}')"
```


Install App bootstrap-cluster for Argo  
```bash
oc apply -f microshift_install/services/argo/argo_bootstrap_cluster/bootstrap-cluster.yaml
```