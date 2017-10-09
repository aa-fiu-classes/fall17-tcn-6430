---
layout: page
title: "Project: SDN"
group: "Project"
---

* toc
{:toc}

Overview:
---------

The goal of this project is to build a simulation environment for experimenting with software-defined networks (SDN). The environment includes (i) a Mininet Virtual Machine, for simulating a network with switches and hosts; and (ii) a FloodLight controller, running on the host machine.

This document may be updated as needed to update version numbers and other important information. Please report any changes needed.

## Environment Setup

The best way to guarantee full credit for the project is to do project development using a Ubuntu 16.04-based virtual machine.

You can easily create an image in your favourite virtualization engine (VirtualBox, VMware) using the Vagrant platform and steps outlined below.

### Set Up Vagrant and Create VM Instance

**Note that all example commands are executed on the host machine (your laptop), e.g., in `Terminal.app` (or `iTerm2.app`) on macOS, `cmd` in Windows, and `console` or `xterm` on Linux.  After the last step (`vagrant ssh`) you will get inside the virtual machine and can compile your code there.**

- Download and install your favourite virtualization engine, e.g., [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

- Download and install [Vagrant tools](https://www.vagrantup.com/downloads.html) for your platform

- Set up project and VM instance

  * Clone project template

        git clone https://github.com/aa-fiu-classes/fall17-tcn-6430-project ~/project
        cd ~/project

  * Initialize VM

        vagrant up

    Do not start VM instance manually from VirtualBox GUI, otherwise you may have various problems (connection error, connection timeout, missing packages, etc.)
    {: class="alert alert-warning"}

  * To establish an SSH session to the created VM, run

        vagrant ssh

  If you are using Putty on Windows platform, `vagrant ssh` will return information regarding the IP address and the port to connect to your virtual machine.

- Work on your project

  All files in `~/project` folder on the host machine will be automatically synchronized with `/vagrant` folder on the virtual machine.  For example, to compile your code, you can run the following commands:

        vagrant ssh
        cd /vagrant
        make

### Notes

* If you want to open another SSH session, just open another terminal and run `vagrant ssh` (or create a new Putty session).

* If you are using Windows, read [this article](http://www.sitepoint.com/getting-started-vagrant-windows/) to help yourself set up the environment.

{% comment %}

## Submission Requirements

To submit your project, you need to prepare:

1. A `README.md` file placed in your code that includes:

    * Your name, UCLA ID
    * The high level design of your server and client
    * The problems you ran into and how you solved the problems
    * List of any additional libraries used
    * Acknowledgement of any online tutorials or code example (except class website) you have been using.

    **If you need additional dependencies for your project, you must update Vagrant file.**

1. All your source code, `Makefile`, `README.md`, `Vagrantfile`, and `.git` folder with your git repository history as a `.tar.gz` archive (and any files from extra credit part).

    To create the submission, **use the provided Makefile** in the skeleton project.  Just update `Makefile` to include your UCLA ID and then just type

        make tarball

    Then submit the resulting archive to Gradescope.

Before submission, please make sure:

1. Your code compiles
2. Client and server conforms to the specification
3. `.tar.gz` archive does not contain temporary or other unnecessary files.  We will automatically deduct points otherwise.

Submissions that do not follow these requirements will not get any credit.
{: class="bs-callout bs-callout-danger" }

{% endcomment %}

Detailed instructions for setting up the experiment environment:
----------------------------------------------------------------

1. Login to the virtualized environment (`vagrant ssh`)

    - Download and unpack [OpenDaylight controller Carbon SR1 release](https://nexus.opendaylight.org/content/repositories/public/org/opendaylight/integration/distribution-karaf/0.6.1-Carbon/distribution-karaf-0.6.1-Carbon.tar.gz)
    
            wget https://nexus.opendaylight.org/content/repositories/public/org/opendaylight/integration/distribution-karaf/0.6.1-Carbon/distribution-karaf-0.6.1-Carbon.tar.gz
            mkdir opendaylight
            tar -x -C opendaylight --strip-components 1 -zf distribution-karaf-0.6.1-Carbon.tar.gz
    
    - Start the controller (the process will take a couple of minutes first time):
    
            cd opendaylight
            ./bin/karaf
    
        After everything started, you should see
    
            vagrant@vagrant:~$ cd opendaylight
            vagrant@vagrant:~/opendaylight$ ./bin/karaf
            
            Apache Karaf starting up. Press Enter to open the shell now...
             52% [=====================================>                                  ]
                ________                       ________                .__  .__       .__     __
                \_____  \ ______   ____   ____ \______ \ _____  ___.__.|  | |__| ____ |  |___/  |_
                 /   |   \\____ \_/ __ \ /    \ |    |  \\__  \<   |  ||  | |  |/ ___\|  |  \   __\
                /    |    \  |_> >  ___/|   |  \|    `   \/ __ \\___  ||  |_|  / /_/  >   Y  \  |
                \_______  /   __/ \___  >___|  /_______  (____  / ____||____/__\___  /|___|  /__|
                        \/|__|        \/     \/        \/     \/\/            /_____/      \/
            
            
            Hit '<tab>' for a list of available commands
            and '[cmd] --help' for help on a specific command.
            Hit '<ctrl-d>' or type 'system:shutdown' or 'logout' to shutdown OpenDaylight.
            
            opendaylight-user@root>

    - Install OpenDaylight features in the prompt (it may take a while):
    
            feature:install odl-dlux-core odl-dluxapps-applications odl-mdsal-apidocs odl-l2switch-switch-ui odl-openflowplugin-flow-services odl-openflowplugin-flow-services-rest odl-openflowplugin-flow-services-ui

2. Login to the virtualized environment (`vagrant ssh` in a separate window or use screen/tmux)

    2.1. Start Mininet and connect to the controller

        sudo mn --topo single,3 --mac --switch ovsk --controller remote,ip=127.0.0.1,port=6653


    This will create a simple three node connected via an OpenFlow switch in Mininet.  The switch will also connect to the OpenDaylight controller running on the virtual machine..

    To learn more about the command line arguments, simply do ``sudo mn –h``

3. Explore OpenDaylight web GUI and Mininet

    To see a live view of the network, go to [http://localhost:8181/index.html](http://localhost:8181/index.html)  (login credentials are admin/admin)

    Until you start any network activity in the Mininet, you will see just switch.  After you start pinging hosts inside Mininet, the hosts will appear and you will be able to see per-port statistics.

4. Explore RESTCONF API

    To access API documentation for different YANG models for the working controller, go to [http://localhost:8181/apidoc/explorer/index.html](http://localhost:8181/apidoc/explorer/index.html)

{% comment %} 
> The particularly important one for this project is Firewall API, which is illustrated in the attached document *firewall\_rest\_api.pdf*.
{% endcomment %}

Exercise
--------

1. Create the following topology in Mininet: (Note: s1 and s2 are switches, h1, h2, h3 and h4 are hosts. **The bandwidth of each link is also given and must be implemented.** ) Take a screenshot of the Mininet output that shows the topology is correctly set up.

    <!-- ![](media/image1.png){width="5.386976159230096in" height="2.375in"} -->

    You will need to create a simple python file to do this.
    Refer to the walkthrough in Step 5 above, look for the “*Custom Topologies*” section to see an example Python script. You can modify the example script to create the topology shown above.
    You will also need to include the `bw=X` setting when adding links in the script, where X is a number such as 20, 15, 10 or 5.
    Finally, when starting Mininet using the `sudo mn ...` command, you will need to add `--link=tc` to enforce the bandwidth setting in the resulting network.
    `tc` stands for `traffic control`.

1.  Connecting the above Mininet topology to SDN Controller. Test the connectivity among the hosts, using the **pingall** command. Include screenshot of the output.

2.  *Immediately After* finishing the pingall test, take a screenshot of the network topology as displayed by OpenDaylight

3.  Now use `iperf` to confirm the bandwidth between h1 and h4, between h2 and h3, between h1 and h2, and between h3 and h4. Include the screenshots of all the iperf output.

More tasks will be posted later.

<!-- 4.  Using the REST Firewall API, implement the following access-control policy: the communication between h1 and h3, and between h2 and h4, should be blocked; communication between any other pairs of hosts are allowed. Use **pingall** again to verify and include the screenshot of the output. -->

<!-- Note: if you are on Linux or MAC OS, you can issue the REST API commands from your host machine; if you are on Windows, you may need to issue those command inside the virtual machine since windows does not come with the `curl` utility software. -->

{% comment %}

Submission Guide:
-----------------

Please submit all of the following:

1. a txt, doc or pdf file that describes your experience in following the Instructions in this handout for setting up the experiment environment, particularly any problem that you have encountered and how you resolved them. Be sure to detail any issue that you could not resolve.

2. The python file that you used to create the topology for the exercise.

3. All the screenshots you have been asked to take in the Exercise section. Make sure the resolution is high enough so that the screenshots can be easily read!

4. The REST Firewall API commands that you used to implement the access-control policy in the exercise.

Grading Criteria:
-----------------

  Item                                                                      Percentage
  ------------------------------------------------------------------------- ------------
  Set up and run Mininet with the specified custom topology                 20%
  Implement connectivity as demonstrated by pingall output                  20%
  Floodlight correctly illustrates the topology                             20%
  Correctly implement the link bandwidth, as demonstrated by iperf output   20%
  Correctly implement the firewall policy                                   20%

{% endcomment %}

Plagiarism will be reported to the university for academic dishonesty.
