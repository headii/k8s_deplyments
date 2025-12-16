oc -n argocd patch svc argocd-server -p '{
   "spec": {
      "type": "NodePort",
     "ports": [
       { "name": "https", "port": 443, "targetPort": 8080, "nodePort": 30443},
        { "name": "http",  "port": 80,  "targetPort": 8080, "nodePort": 30080}
      ]
    }
 }'



oc -n argocd patch deploy argocd-redis   --type=json   -p='[
     {"op":"remove","path":"/spec/template/spec/securityContext"},
      {"op":"remove","path":"/spec/template/spec/containers/0/securityContext"},
      {"op":"remove","path":"/spec/template/spec/initContainers/0/securityContext"}
   ]'
