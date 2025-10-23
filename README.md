
# Home Lab

This repo contains all of the configuration and documentation of my homelab.

The purpose of my homelab is to learn and to have fun. Being a Cloud Native Engineer by choice, I work with kubernetes every day, and my homelab is the place where I can try out and learn new things. On the other hand, by self-hosting some applications
,it makes me feel responsible for the entire process of deploying and maintaining an applications, it makes me feel responsible for the entire process of SDLC. It forces me to think about backup strategies, security, scalability and the ease of deployment
and maintenace.





## Cluster Provisioning

I use Ubuntu server LTS version as OS for my VM and I use Proxmox as a Type I hypervisor which is installed directly on my bare metal machine.
Since the compute and memory space is limited due to hardware I end up provision VM consuming all the resources for one single node.



## Hardware
I'm using a very basic computer machine and the specs are as follows.

* Master Node --> Lenovo ThinkCentre 710Q 2 vCPU and 8 GB RAM + 256 GB SSD
* Slave1 Node --> Lenovo ThinkCentre 710Q 2 vCPU and 8 GB RAM + 256 GB SSD

This is a very basic system and as requirement and the project grows, we will upgrade this system.

#### Cautions for Hardware
* Make sure that virtualization is enabled at CPU level.
* Mother board should support upgrade of CPU and RAM as demand increases.
* Refurbished or Old Laptops just work fine.
