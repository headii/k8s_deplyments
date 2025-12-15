# Microshift Lab Setup

Virtualbox VM with RHEL 9.6  
4 Cores  
8 GB RAM  
Disk1 50GB (System)  
Disk2 50GB (Microshift-Storage)  

DNS:  
*.microshift.lab


# Install Microshift

Add subscription
```bash
sudo subscription-manager
```


Add Repo and Install
```bash
sudo subscription-manager repos \
    --enable rhocp-4.20-for-rhel-9-$(uname -m)-rpms \
    --enable fast-datapath-for-rhel-9-$(uname -m)-rpms
sudo subscription-manager release --set=9.6
sudo dnf install -y microshift openshift-clients
sudo dnf update
sudo reboot
```


Create Pull Secret  
https://console.redhat.com/openshift/install/pull-secret
```bash
sudo cp $HOME/openshift-pull-secret /etc/crio/openshift-pull-secret
sudo chown root:root /etc/crio/openshift-pull-secret
sudo chmod 600 /etc/crio/openshift-pull-secret
``` 


Firewall
```bash
sudo firewall-cmd --permanent --zone=trusted --add-source=10.42.0.0/16
sudo firewall-cmd --permanent --zone=trusted --add-source=169.254.169.1
sudo firewall-cmd --reload
```


Create VG
```bash
sudo pvcreate /dev/sdb
sudo vgcreate microshift /dev/sdb
```


Copy Config
```bash
sudo cp microshift_config/config.yaml microshift_config/lvmd.yaml /etc/microshift/
```


Start Service
```bash
sudo systemctl enable microshift
sudo systemctl start microshift
```


Create Config for "oc"
```bash
mkdir -p ~/.kube/
sudo cat /var/lib/microshift/resources/kubeadmin/kubeconfig > ~/.kube/config
chmod go-r ~/.kube/config
oc get pods -A
```


oc bash completion
```bash
echo 'source <(oc completion bash)' >> ~/.bashrc
```




openshift-console  
http://openshift-console.apps.microshift.lab



###########################################################################
Install Microshift links  
https://docs.redhat.com/en/documentation/red_hat_build_of_microshift/4.20/  
https://github.com/openshift/microshift/blob/main/docs/user/getting_started.md  

ISO Download:  
https://developers.redhat.com/content-gateway/file/rhel/Red_Hat_Enterprise_Linux_9.6/rhel-9.6-x86_64-dvd.iso
