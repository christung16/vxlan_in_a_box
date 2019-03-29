# vxlan_in_a_box

Inspired by NetDevOps Live
I’m thinking if it is possible to build a VXLAN/EVPN network in my notebook so that I can test/code the network whenever/wherever
Now, I can carry my network on the plane or in the submarine

youtube: https://youtu.be/GM7hpvsSFh8

***************************************************
This project is to create two nx-osv instances and run vxlan between them. Connecting two linux guest system and they can connect together.
ALL in a box (assuming you have at least 16GB (more is better) and descent CPU (mine is i7-8650U)


Technology used:
1. nx-osv v9.2.3 customized box image
2. CVAC (Cisco Virtual Appliance Configuration)
3. vagrant
4. centos7 vagrant image
5. VritualBox on windows 10

Problem overcome:
1. NX-OSv doesn't require to reboot twice to accept the CVAC
2. NX-OSv doesn't vagrant up twice. Need to disable the auto-shared-folder, set the ssh.shell to "run bash" to avoid the issue that vagrant cannot recognize the nx-os shell. Thanks run bash!
3. Need to modify the nic promiscuous mode to "allow-vms" such that host can communicate to nx-osv ethernet port

Caveat:
1. NX-OSv 9.2.3 required at least 6GB memroy instead of 4GB. 4GB wont bootup properly since v9.2.3
