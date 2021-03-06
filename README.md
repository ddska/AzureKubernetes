# AzureKubernetes
Kubernetes provisioning template

Kubernetes is an open-source orchestration platform for automating deployment, operations, and scaling of applications across multiple hosts. It targets applications composed of multiple Docker containers, such as distributed micro-services and provides ways for containers to find and communicate with each other. It enables users to ask a cluster to run a set of containers with an automatic scheduling and choice of the hosts to run those containers on. It regroups containers that make up an application into logical units for easy management with self-healing mechanisms (auto-restarting, re-scheduling, and replicating containers) and discovery. Kubernetes has been created by Google but contributors to this project now include IBM, Microsoft, Red Hat, CoreOS, Mesosphere, and others… 


                          ┌───────────────────────────────┐
                          │                            ┌─┐│
                          │                            │1││
                          │      ┌─────────────────┐   └─┘│
                          │      │┌────────────────┴┐     │
                          │      └┤┌────────────────┴┐    │
             ┌────────────┼───────▶┤    etcd srv     │    │
             │            │        └─────────────────┘    │
             │            │                               │
             │            │                               │
             │            │      ┌─────────────────┐      │
             │            │      │┌────────────────┴┐     │
             │            │      └┤┌────────────────┴┐    │◀─┐
             │            │ ┌────▶└┤  k8 master srv  │    │  │
             │            │ │      └─────────────────┘    │  │
    ┌─────────────────┐   │ │                             │  │
    │azuredeploy.json │───┼─┤                             │  │
    └─────────────────┘   │ │    ┌─────────────────┐      │  │
             │            │ │    │┌────────────────┴┐     │  │
             │            │ │    └┤┌────────────────┴┐    │  │
             │            │ └────▶└┤ k8 minions srv  │    │  │
             │            │        └─────────────────┘    │  │
             │            │                               │  │
             │            │                               │  │
             │            └───────────────────────────────┘  │
             │                                               │
             │            ┌───────────────────────────────┐  │
             │            │                            ┌─┐│  │
             │            │        ┌─────────────────┐ │2││  │
             └────────────┼───────▶│ Ansible Bastion │ └─┘│  │
                          │        └─────────────────┘    │──┘
                          │         ansible playbook      │
                          │       to deploy k8 cluster    │
                          └───────────────────────────────┘

arm:  
  - first deploy:
    - n etcd servers  
    - n masters kubernetes masters  
    - n minions kubernetes minions  
    on each node the script first-boot.sh is played to register the private ip and the role of the node (etc, masters, minions) 
  - then deploy:  
    - 1 ansible command control vm  
    on this vm the script deploy.sh is played to : 
       - install epel repo
       - install curl
       - configure ssh to access to k8 nodes 
       - ssh to nodes to get private ip to create the inventory file
       - install required install groups needed to compile ansible
       - install required system packages to use ansible
       - install required python modules to use ansible
       - install ansible by clone main repo and submodules
       - configure ansible (ansible.cfg)
       - check ansible is working fine
       - create the inventory file from gathered informations
       - clone kube playbook from herveleclerc repo
       - play playbook to deploy the kubernetes cluster  




### azure-cli : 
```bash
azure group create kuber8grp northeurope
azure group deployment create kuber8grp kuber8cluster --template-uri https://raw.githubusercontent.com/DXFrance/AzureKubernetes/master/Kubernetes-Ansible-Centos-Azure/azuredeploy.json
```
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FDXFrance%2FAzureKubernetes%2Fmaster%2FKubernetes-Ansible-Centos-Azure%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>
<a href="http://armviz.io/#/?load=https://raw.githubusercontent.com/DXFrance/AzureKubernetes/master/Kubernetes-Ansible-Centos-Azure/azuredeploy.json" target="_blank">
    <img src="http://armviz.io/visualizebutton.png"/>
</a>