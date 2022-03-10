[root@tk-bastion ~]# cd ocp4
[root@tk-bastion ocp4]#  echo ******* > pw
[root@tk-bastion ocp4]#  ansible-galaxy collection install -r requirements.yml
 
[root@bastion ~]# ssh-keygen -t rsa -q -N "" -f ~/.ssh/id_rsa
[root@bastion ~]# cat ~/.ssh/id_rsa.pub > ~/.ssh/authorized_keys
 
# install openshift
[root@tk-bastion ocp4]# ansible-playbook ocp4.yml   
 
 
# Create VMs with RHCOS Template and vmoption files
[root@tk-bastion ocp4]# find /root/openshift/TK/*vmoptions
 
 
# Connect via oc to openshift cluster
[root@tk-bastion openshift]# export KUBECONFIG=$(find /root -name kubeconfig )
[root@tk-bastion openshift]# cp $(find /root -name oc) /usr/local/bin
[root@tk-bastion openshift]# oc get nodes
 
# approve csr for worker nodes twice
[root@tk-bastion openshift]# oc get csr|awk '$NF=="Pending"{print $1}'|xargs oc adm certificate approve
 
# all nodes (worker and master should be ready)
[root@tk-bastion openshift]# oc get nodes
 
# wait until all pods running and ready
[root@tk-bastion openshift]# watch "oc get pods -A|grep -v Completed|egrep -v '([1-9])\/\1'"
