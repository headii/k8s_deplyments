# Microshift Lab

Setup  
4 Cores  
8 GB RAM  
Disk1 50GB (System)  
Disk2 50GB (Microshift-Storage)  

DNS:  
*.microshift.lab


# Install

Add subscription
```bash
subscription-manager
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

Create Config for "oc"
```bash
mkdir -p ~/.kube/
sudo cat /var/lib/microshift/resources/kubeadmin/kubeconfig > ~/.kube/config
chmod go-r ~/.kube/config
```

Create VG
```bash
pvcreate /dev/sdb
vgcreate microshift /dev/sdb
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

Firewall
```bash

```


openshift-console  
http://openshift-console.apps.microshift.lab




Install Microshift links  
https://docs.redhat.com/en/documentation/red_hat_build_of_microshift/4.20/  
https://github.com/openshift/microshift/blob/main/docs/user/getting_started.md  

ISO Download:  
https://developers.redhat.com/content-gateway/file/rhel/Red_Hat_Enterprise_Linux_9.6/rhel-9.6-x86_64-dvd.iso
