# vxlan_in_a_box

Inspired by NetDevOps Live
I’m thinking if it is possible to build a VXLAN/EVPN network in my notebook so that I can test/code the network whenever/wherever
Now, I can carry my network on the plane or in the submarine

youtube: https://youtu.be/GM7hpvsSFh8

***************************************************
This project is to create two nx-osv instances and run vxlan between them. Connecting two linux guest system and they can connect together.
ALL in a box (assuming you have at least 16GB (more is better) and descent CPU (mine is i7-8650U)


## Technology used:
1. nx-osv v9.2.3 CUSTOMIZED box image
2. CVAC ([Cisco Virtual Appliance Configuration](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus9000/sw/7-x/nx-osv/configuration/guide/b_Cisco_Nexus_9000v/b_Cisco_Nexus_9000v_chapter_011.html)) - Beginning with Cisco NX-OS Release 7.0(3)I5(2), Cisco Cisco Nexus 9000v supports the Cisco Virtual Appliance Configuration (CVAC). This out-of-band configuration mechanism is similar to the PowerOn Auto Provisioning (POAP) autoconfiguration, but instead of downloading the configuration across the network as POAP does, CVAC receives the configuration injected into the Cisco Cisco Nexus 9000v environment on a CD-ROM. The configuration is detected and applied at startup time. 
3. vagrant
4. centos7 vagrant image
5. VritualBox on windows 10

## Problem overcome:
1. NX-OSv doesn't require to reboot twice to accept the CVAC
2. NX-OSv doesn't vagrant up twice. Need to disable the auto-shared-folder, set the ssh.shell to "run bash" to avoid the issue that vagrant cannot recognize the nx-os shell. Thanks run bash!
3. Need to modify the nic promiscuous mode to "allow-vms" such that host can communicate to nx-osv ethernet port

## Caveat:
1. NX-OSv 9.2.3 required at least 6GB memroy instead of 4GB. 4GB wont bootup properly since v9.2.3
2. CVAC: CVAC won't work if you use the box image directly download from CCO, you have to follow the steps below to build a new box image in order to accept the CVAC

  Steps to customized a nxosv box image:
    
>    a.	Vagrant up (use the box image download from (https://software.cisco.com))

>    b.	Modify the boot system image to the proper one ((config)# boot system bootflash:///……)

>    c.	Copy run start

>    d.	Vagrant halt -f

>    e.	Vagrant package –output nxosv-new.box

>    f.	Vagrant box add –name nxosv-new nxosv-new.box
    
 
By using a new box image, the CVAC works now.
I can use a new image to build a vxlan_in_a_box with just vagrant up once.

CVAC: Build a iso for configuration file
  Steps to create the iso configuration file:

  Linux: mkisofs -output nxosconfig.iso -l --relaxed-filenames --iso-level 2 <file(s) to add>
URL: https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus9000/sw/7-x/nx-osv/configuration/guide/b_Cisco_Nexus_9000v.pdf

## Steps to build the vxlan in a box Lab: 
### [Refer to the video](https://youtu.be/GM7hpvsSFh8)

   1. open a cmd prompt: "vagrant box list" to check the vx-osv box image and centos image are ready

          ### Example:
          -----------------------
          centos/7                        (virtualbox, 1811.02)
          nxosv/9.2.2-final               (virtualbox, 0)

   2. edit .\2_switches_cvac\Vagrant: Modify "node.vx.box" to boot the required nx-osv images
   3. open a cmd prompt: "vagrant up" and wait a few minutes to boot up two vx-osv VM
   4. edit .\centos7\Vagrant: Modify "node.vx.box" to boot the required centos7 image
   5. open a cmd prompt: "vagrant up" and wait a few minutes to boot up two centos7 VM
   6. open a cmd prompt: "vagrant ssh n9k1" to check vxlan status
   7. open a cmd prompt: "vagrant ssh n9k2" to check vxlan status
   8. open a cmd prompt: "vagrant ssh host1" to do ping test to host2: "ping 192.168.0.12"

