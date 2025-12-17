# WIP

# Microshift Lab

This is my Microshift 4.20 Lab Setup

## Setup

Virtualbox VM with RHEL 9.7  
4 Cores  
8 GB RAM  
Disk1 50GB (System)  
Disk2 50GB (Microshift-Storage)  

DNS:  
*.microshift.lab


## Install Microshift

Add subscription
```bash
sudo subscription-manager register
```

Add Repo and Install
```bash
sudo subscription-manager repos \
    --enable rhocp-4.20-for-rhel-9-$(uname -m)-rpms \
    --enable fast-datapath-for-rhel-9-$(uname -m)-rpms
```

Set fix RHEL Release  
```bash
sudo subscription-manager release --set=9.7
```

Install packages
```bash
sudo dnf install -y microshift openshift-clients git
sudo dnf update -y
sudo reboot
```

Create Pull Secret  [pull-secret](https://console.redhat.com/openshift/install/pull-secret)
  
```bash
echo '<SECRET-FROM-REDHAT>' > $HOME/openshift-pull-secret
sudo mv $HOME/openshift-pull-secret /etc/crio/openshift-pull-secret
sudo chown root:root /etc/crio/openshift-pull-secret
sudo chmod 600 /etc/crio/openshift-pull-secret
``` 

Firewall
```bash
sudo firewall-cmd --permanent --zone=trusted --add-source=10.42.0.0/16
sudo firewall-cmd --permanent --zone=trusted --add-source=169.254.169.1
sudo firewall-cmd --reload
```

Create VG for PV
```bash
sudo pvcreate /dev/sdb
sudo vgcreate microshift-pv /dev/sdb
sudo vgdisplay
```

Pull Repo
```bash
git clone https://github.com/headii/k8s_deplyments.git
cd k8s_deplyments
```

Copy Config
```bash
sudo cp microshift_install/config/config.yaml microshift_install/config/lvmd.yaml /etc/microshift/
```

Start Service
```bash
sudo systemctl enable --now microshift.service
```

oc bash completion
```bash
echo 'source <(oc completion bash)' >> ~/.bashrc
```

Create Config for "oc"    
```bash
mkdir -p ~/.kube/
sudo cat /var/lib/microshift/resources/kubeadmin/kubeconfig > ~/.kube/config
chmod go-r ~/.kube/config
oc get pods -A
```    


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
oc create namespace argocd
oc apply -f microshift_install/services/argo/argo_install/argocd-install.yaml -n argocd
oc get pods -n argocd
```

Get Admin Password from ArgoCD  
```bash
oc get secret argocd-initial-admin-secret -n argocd -o jsonpath={.data.password} | base64 -d
```

Install App bootstrap-cluster for Argo  
```bash
oc apply -f microshift_install/services/argo/argo_bootstrap_cluster/bootstrap-cluster.yaml
```


#### Microshift Official Doku   
https://docs.redhat.com/en/documentation/red_hat_build_of_microshift/4.20/  
https://github.com/openshift/microshift/blob/main/docs/user/getting_started.md  

##### ISO Download  
https://developers.redhat.com/content-gateway/file/rhel/Red_Hat_Enterprise_Linux_9.7/rhel-9.7-x86_64-dvd.iso
