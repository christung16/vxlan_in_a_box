# Vxlan_in_a_box

Inspired by [NetDevOps Live](https://developer.cisco.com/netdevops/live/)
I’m thinking if it is possible to build a VXLAN/EVPN network in my notebook so that I can test/code the network whenever/wherever
Now, I can carry my vxlan network on the plane or in the submarine

youtube: https://youtu.be/GM7hpvsSFh8

***************************************************
This project is to create two nx-osv instances and run vxlan between them. Connecting two linux guest system and they can connect together.
ALL in a box (assuming you have at least 16GB (more is better) and descent CPU (mine is i7-8650U)

## Use Case Description
While we learn Vxlan, we usually need two spine and two leaf and some hosts connecting to it. Usually a physical environment or VIRL is required. This lab can build a simplified Vxlan inside your notebook just using vagrant/virtualbox. You can test/learn some Vxlan features:
Underlay:
  - Layer 3
  - OSPF

Overlay:
  - Anycast GW
  - BGP
  - L3VNI
  - VRF
  - RD & RT
  - VTEP
  - APR suppression

### Technology used:
1. nx-osv v9.2.3 CUSTOMIZED box image
2. CVAC ([Cisco Virtual Appliance Configuration](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus9000/sw/7-x/nx-osv/configuration/guide/b_Cisco_Nexus_9000v/b_Cisco_Nexus_9000v_chapter_011.html)) - Beginning with Cisco NX-OS Release 7.0(3)I5(2), Cisco Cisco Nexus 9000v supports the Cisco Virtual Appliance Configuration (CVAC). This out-of-band configuration mechanism is similar to the PowerOn Auto Provisioning (POAP) autoconfiguration, but instead of downloading the configuration across the network as POAP does, CVAC receives the configuration injected into the Cisco Cisco Nexus 9000v environment on a CD-ROM. The configuration is detected and applied at startup time. 
3. vagrant
4. centos7 vagrant image
5. VritualBox on windows 10

### Problem overcome:
1. NX-OSv doesn't require to reboot twice to accept the CVAC
2. NX-OSv doesn't vagrant up twice. Need to disable the auto-shared-folder, set the ssh.shell to "run bash" to avoid the issue that vagrant cannot recognize the nx-os shell. Thanks run bash!
3. Need to modify the nic promiscuous mode to "allow-vms" such that host can communicate to nx-osv ethernet port


## Installation:

###  Pre-requisite:
    1. Virtual Box
    2. Vagrant
    3. NX-OSv box image (Download from (https://software.cisco.com))
    4. vagrant linux host eg centos/7 or ubuntu/xenial64
  
##  Installation steps:
### 1. Customized a NX-OSv box image  
>     a.	Vagrant up (use the box image download from (https://software.cisco.com))
      
>     b.	Modify the boot system image to the proper one ((config)# boot system bootflash:///……)
      
>     c.	Copy run start

>     d.	Vagrant halt -f

>     e.	Vagrant package –output nxosv-new.box

>     f.	Vagrant box add –name nxosv-new nxosv-new.box
    
### 2. Build a CVAC ISO for startup configuration file
 
         By using a new box image, the CVAC works now. Reference link: NX-OSv Configuration(https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus9000/sw/7-x/nx-osv/configuration/guide/b_Cisco_Nexus_9000v.pdf) The new image to build a vxlan_in_a_box with just vagrant up once.
         Linux: mkisofs -output nxosconfig.iso -l --relaxed-filenames --iso-level 2 <file(s) to add>

##  Configuration
     1. open a cmd prompt: "vagrant box list" to check the vx-osv box image and centos image are ready

          ### Example:
          -----------------------
          centos/7                        (virtualbox, 1811.02)
          nxosv/9.2.2-final               (virtualbox, 0)
     
     2. Edit .\2_switches_cvac\Vagrant: Modify "node.vx.box" to boot the required nx-osv images
     3. Edit .\centos7\Vagrant: Modify "node.vx.box" to boot the required centos7 image
     
##  Usage [Refer to the video](https://youtu.be/GM7hpvsSFh8?t=29)

     1. cd .\2_switches_cvac
     2. open a cmd prompt: "vagrant up" and wait a few minutes to boot up two NX-OSv VM
     3. cd .\centos7
     4. open a cmd prompt: "vagrant up" and wait a few minutes to boot up two centos7 VM
     
##  How to test [Refer to the video](https://youtu.be/GM7hpvsSFh8?t=128)

     1. open a cmd prompt: "vagrant ssh n9k1" to check vxlan status
     2. open a cmd prompt: "vagrant ssh n9k2" to check vxlan status
     3. open a cmd prompt: "vagrant ssh host1" to do ping test to host2: "ping 192.168.0.12"

## Known issues:

     1. NX-OSv 9.2.3 required at least 6GB memroy instead of 4GB. 4GB wont bootup properly since v9.2.3
     2. CVAC: CVAC won't work if you use the box image directly download from CCO, you have to follow the steps below to build a new box image in order to accept the CVAC

## Getting help
     If you have questions, concerns, bug reports, etc., please create an issue against this repository.

----

## Licensing info

A license is required for others to be able to use your code. An open source license is more than just a usage license, it is license to contribute and collaborate on code. Open sourcing code and contributing it to [Code Exchange](https://developer.cisco.com/codeexchange/) or [Automation Exchange](https://developer.cisco.com/automation-exchange/) requires a commitment to maintain the code and help the community use and contribute to the code. 

Choosing a license can be difficult and depend on your goals for your code, other licensed code on which your code depends, your business objectives, etc.   This template does not intend to provide legal advise. You should seek legal counsel for that. However, in general, less restrictive licenses make your code easier for others to use.

> Cisco employees can find licensing options and guidance [here](https://wwwin-github.cisco.com/eckelcu/DevNet-Code-Exchange/blob/master/GitHubUsage.md#licensing-guidance).

Once you have determined which license is appropriate, GitHub provides functionality that makes it easy to add a LICENSE file to a GitHub repo, either when creating a new repo or by adding to an existing repo.

When creating a repo through the GitHub UI, you can click on *Add a license* and select from a set of [OSI approved open source licenses](https://opensource.org/licenses). See [detailed instructions](https://help.github.com/articles/licensing-a-repository/#applying-a-license-to-a-repository-with-an-existing-license).

Once a repo has been created, you can easily add a LICENSE file through the GitHub UI at any time. Simply select *Create New File*, type *LICENSE* into the filename box, and you will be given the option to select from a set of common open source licenses. See [detailed instructions](https://help.github.com/articles/adding-a-license-to-a-repository/).

Once you have created the LICENSE file, be sure to update/replace any templated fields with appropriate information, including the Copyright. For example, the [3-Clause BSD license template](https://opensource.org/licenses/BSD-3-Clause) has the following copyright notice:

`Copyright (c) <YEAR>, <COPYRIGHT HOLDER>`

See the [LICENSE](./LICENSE) for this template repo as an example.

Once your LICENSE file exists, you can delete this section of the README, or replace the instructions in this section with a statement of which license you selected and a link to your license file, e.g.

This code is licensed under the BSD 3-Clause License. See [LICENSE](./LICENSE) for details.

Some licenses, such as Apache 2.0 and GPL v3, do not include a copyright notice in the [LICENSE](./LICENSE) itself. In such cases, a NOTICE file is a common place to include a copyright notice. For a very simple example, see [NOTICE](./NOTICE). 

In the event you make use of 3rd party code, it is required by some licenses, and a good practice in all cases, to provide attribution for all such 3rd party code in your NOTICE file. For a great example, see [https://github.com/cisco/ChezScheme/blob/master/NOTICE](https://github.com/cisco/ChezScheme/blob/master/NOTICE).   

## Credits and references

1. [NetDevOps Live](https://developer.cisco.com/netdevops/live/)
2. [Vagrant for Network Programmability](https://developer.cisco.com/codeexchange/github/repo/hpreston/vagrant_net_prog/)




