# Dynamic Service Function Chaining Demonstration
Dynamic SFC Demo at OpenStack Summit 2018 Vancouver by Tacker team

## I. Prerequisites

**1. Two libvirt VMs** to install devstack and Zabbix server.

**2. Two libvirt networks** which can reach the internet (one management, one devstack external)
 
## II. Topology

**1. Chain:** IDS (Suricata), Openwrt (VNFs)

**2. Target to protect:** Server nova instance.

![alt text](https://github.com/dangtrinhnt/DynamicSFCDemo/raw/master/topology/DSFC1.png "Dynamic SFC 1")

![alt text](https://github.com/dangtrinhnt/DynamicSFCDemo/raw/master/topology/DSFC2.png "Dynamic SFC 2")

## III. Setting up

**1.** Install **devstack** in a VM as it is described here *([0])* using the configuration sample here *([1])* (Please add here the right configuration for the management and external networks). Two network interfaces one, for the management and one for the external_network.

**2.** Create a second VM where you will install the **Zabbix server** according to these instuctions *([2])*. Please be sure that the Zabbix server has an interface on the same network as the external_network of devstack to be able to monitor the server nova instance through the floating IP.

**3.** Install in the **server nova instance** and in the devstack VM natively the **zabbix agent** *([2])*. The zabbix agent in the server nova instance is used to send data back to the zabbix server. The zabbix agent in the devstack VM is essential because we execute from the zabbix server the vnffg-update command so we can update the classifier of the chain. And for this we need the zabbix agent to the devstack VM.

**4.** Deploy two Service Functions (SFs) via two VNFs:

+ The first one is Suricata (IDS) which will be deployed using this VNFD template *([7])*. After the VNF instance has been deployed successfully, SSH to it and configure as in *([3])*.

+ The second one is OpenWrt with the configuration is the same as it is described in the Tacker docs *([4])*. Use the VNFD template here *([8])* and param file here *([9])*.

**5.** Create a VNFFG with a chain (IDS, Openwrt) and no classifier *([5])*.

**6.** Generate ICMP traffic using PING towards the floating IP of the server nova instance and when that traffic reaches a threshold a specific event is published to the zabbix server and zabbix server executes the vvnffg-update action which update the already created VNFFG with a classifier which classifies the ICMP traffic *([6])*. That means that the traffic will be steered to the SFs and it will be mitigated.

## IV. Contributors

Yong Sheng Gong <gong.yongsheng@99cloud.net> - [99cloud](http://99cloud.net)

Dimitrios Markou <mardim@intracom-telecom.com> - [Intracom Telecom](http://www.intracom-telecom.com/)

Yan Xing'an <yanxingan@cmss.chinamobile.com> - [China Mobile](http://www.chinamobileltd.com/en/global/home.php)

Trinh Nguyen <dangtrinhnt@gmail.com> - [EdLab](https://www.edlab.xyz/)


[0]: https://docs.openstack.org/devstack/latest/

[1]: https://github.com/openstack/tacker/blob/master/devstack/local.conf.example

[2]: https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-zabbix-to-securely-monitor-remote-servers-on-ubuntu-16-04

[3]: https://blog.rapid7.com/2017/02/14/how-to-install-suricata-nids-on-ubuntu-linux/

[4]: https://docs.openstack.org/tacker/latest/install/deploy_openwrt.html

[5]: https://github.com/dangtrinhnt/DynamicSFCDemo/blob/master/tosca_templates/vnffgd_no_classifier.yaml

[6]: https://github.com/dangtrinhnt/DynamicSFCDemo/blob/master/tosca_templates/vnffgd_icmp.yaml

[7]: https://github.com/dangtrinhnt/DynamicSFCDemo/blob/master/tosca_templates/suricata_vnfd.yaml

[8]: https://github.com/dangtrinhnt/DynamicSFCDemo/blob/master/tosca_templates/openwrt_vnfd.yaml

[9]: https://github.com/dangtrinhnt/DynamicSFCDemo/blob/master/tosca_templates/openwrt_vnfd_config.yaml
