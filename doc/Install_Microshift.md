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