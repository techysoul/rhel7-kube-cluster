##Troubleshooting
#Issue
vagrant up taking time or not moving as expected..
#Solution
vagrant landrush rm <problemvm_name> e.g. master3
vagrant landrush restart
vagrant landrush list

#Adding all ssh-config to host machine so that ansible can use it.
rm -rf $HOME/.ssh/config
vagrant ssh-config master1 >> $HOME/.ssh/config
vagrant ssh-config master2 >> $HOME/.ssh/config
vagrant ssh-config master3 >> $HOME/.ssh/config
vagrant ssh-config worker1 >> $HOME/.ssh/config
vagrant ssh-config worker2 >> $HOME/.ssh/config
vagrant ssh-config worker3 >> $HOME/.ssh/config

vagrant snapshot save fresh_vms_setup

cd ansible

#Running for a single node
ansible-playbook -i inventory pre-reqs.yaml --limit @/Users/madhav/github/rhel7-kube-cluster/ansible/pre-reqs.retry


ansible-playbook -i inventory pre-reqs.yaml
ansible-playbook -i inventory kernel-params.yaml

vagrant snapshot save post_pre_req_setup

#On Master1

sudo kubeadm init --apiserver-advertise-address=192.168.33.10

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

#On Woker Nodes - Exact command is from output of init above
sudo kubeadm join 192.168.33.10:6443 --token yzubgc.1i552kqrzhmgxf7q --discovery-token-ca-cert-hash sha256:a48217b428a2e27c2c94d5ef015e8f6c7d8e54234144a17b533dd296f8a2ac82

#On Master1
kubectl get nodes

#Generate SSH Key pair on master0 node,

ssh master0
sudo su -
#ssh-keygen -t rsa <-- Accept the default for everything

ssh master1
sudo su -
#ssh-keygen -t rsa <-- Accept the default for everything

ssh master2
sudo su -
#ssh-keygen -t rsa <-- Accept the default for everything


ssh master0
sudo su -
#cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys

ssh master1
sudo su -
#cat $HOME/.ssh/id_rsa.pub <-- Copy the content of this file and paste on master0:/root/.ssh/authorized_keys

ssh master2
sudo su -
#cat $HOME/.ssh/id_rsa.pub <-- Copy the content of this file and paste on master0:/root/.ssh/authorized_keys


ssh master0
sudo su -
#scp $HOME/.ssh/authorized_keys master1:/root/.ssh
#scp $HOME/.ssh/authorized_keys master2:/root/.ssh

ssh master0
sudo su -
#ssh master0 date
#ssh master1 date
#ssh master2 date

#If it does not ask for password, then you have configured password less authentication on all the master nodes.
