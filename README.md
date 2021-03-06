INSTALLING KUBERNETES ON LINUX AMI - CENTOS USING ANSIBLE
----------------------------------------------------------------------------------------------------------------------------------------------------------------

Pre-Requisite
- Ansible installed
- Git installed for clone this repo
- Has at least 2 instance ec2 for master and worker1 and minimum 2 core vCPU
- Add Security Group for Kubernetes Cluster. Detailed Security Group shown below

+ Security Group : https://www.cloudiqtech.com/creating-aws-security-groups-for-kubernetes/
    - type: SSH, port: 22, from: anywhere `or your customized ip address`
    - type: Custom TCP, port: 6443, from: anywhere, description: Kubernetes Api
    - type: Custom TCP, port: 443, from: anywhere, description: TCP ingress rules
    - type: Custom TCP, port: 2379-2380, from: anywhere, description: etcd Server
    - type: Custom TCP, port: 10250, from: anywhere, description: Kubelet health check
    - type: Custom TCP, port: 10252, from: anywhere, description: Kube controller manager
    - type: Custom TCP, port: 10255, from: anywhere, description: Read only kubelet API
    - type: Custom TCP, port: 6783-6784, from: anywhere, description: Weave net port add on, Before installing Weave Net, you should make sure the following ports are not blocked by your firewall: TCP 6783 and UDP 6783/6784 https://www.weave.works/docs/net/latest/kubernetes/kube-addon/
    - type: Custom TCP, port: 30000-32767, from: anywhere `or your customized ip address`, description: for external applications. However, it is more likely that you will expose external applications to outside the cluster via load balancers, and restrict access to these ports to within your vpc
    - type: Custom TCP & UDP, port: 7946 , from: anywhere, description: MetalLB -> https://metallb.universe.tf/

Main
i. SSH into your master instance or node
ii. Run `git clone https://github.com/adamsh231/kubernetes-aws-ansible`
1. Edit the Inventory file corresponding for master and workers ip on your VPS (ec2 - aws)
    - Add .pem file for in same parent folder to inventory
    - Run `sudo chmod 400 {your .pem file path}`
2. Run `ansible-playbook -i inventory 1-dep.yaml`
3. Run `ansible-playbook -i inventory 2-master.yaml`
    - Run `watch kubectl get node` -> check if node is existing but status is `NotReady` this will take a moment to `Ready` state
4. Run `ansible-playbook -i inventory 3-workers.yaml`
    - Run `watch kubectl get node` -> check worker node are joined and status is `Ready`. This will take a moment to change node state to `Ready`

- Nb: [Warning] beware of Ansible cache
- Nb: If Calling service with DNS doesnt work
    - please check with ip address first -> if ip address work
    - run `kubectl -n kube-system rollout restart deployment coredns` -> https://stackoverflow.com/questions/45805483/kubernetes-pods-cant-resolve-hostnames, then check with DNS


-------------------------------------------------------------------------------------------------------------------------------------------
Add Ons
1. Installing `Helm` for Kubernetes Deployment Convinient
2. Installing `tree`
3. Installing `k9s` with `homebrew`
4. Installing `htop`
-------------------------------------------------------------------------------------------------------------------------------------------

https://www.youtube.com/watch?v=fy8SHvNZGeE&ab_channel=IBMCloud

https://www.youtube.com/watch?v=-ykwb1d0DXU&ab_channel=TechWorldwithNana

Helm issue - https://github.com/fnproject/fn-helm/issues/21
