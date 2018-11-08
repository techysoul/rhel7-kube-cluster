

#Adding all ssh-config to host machine so that ansible can use it.
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
