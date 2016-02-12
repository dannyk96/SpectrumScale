Installing Spectrunm Scale 
===========================

This lab shows how to install Spectrum Scale in text-only mode on theconsole.

There are two methods that can be used here: the traditional manual install of the RPMs and the newer method which uses the `spectrumscale`
tool. For this lab you can use either method. The manual method will give you a closer understand of what is happening at a systenm level; the 
`spectrumscale` tool though is faster, particularly as it will will install all nodes froma single command line.




### Step 1.2 : Unpack the tarball

Simply copy and unzip the gzipped tarball to upack the self extracting Installer (and a README file).



``` bash
/gpfs-course/software/base/Spectrum_Scale_Protocols_Advanced-4.2.0.0-x86_64-Linux-install
```

Two files will be extracted
```
Spectrum_Scale_install-4.1.1.0_x86_64_standard_protocols
README
```
### Step 1.2 Unpack the Installation files


    /gpfs-course/software/base/Spectrum_Scale_Protocols_Advanced-4.2.0.0-x86_64-Linux-install

Output will be similar to:
```
Extracting License Acceptance Process Tool to /usr/lpp/mmfs/4.2.0.0 ...
tail -n +544 /gpfs-course/software/base/Spectrum_Scale_Protocols_Advanced-4.2.0.0-x86_64-Linux-install | /bin/tar -C /usr/lpp/mmfs/4.2.0.0 -xvz --exclude=installer --exclude=*_rpms --exclude=*rpm  --exclude=*tgz --exclude=*deb 1> /dev/null

Installing JRE ...
tail -n +544 /gpfs-course/software/base/Spectrum_Scale_Protocols_Advanced-4.2.0.0-x86_64-Linux-install | /bin/tar -C /usr/lpp/mmfs/4.2.0.0 --wildcards -xvz  ibm-java*tgz 1> /dev/null
/bin/tar -C /usr/lpp/mmfs/4.2.0.0/ -xzf /usr/lpp/mmfs/4.2.0.0/ibm-java*tgz
Defaulting to --text-only mode.

Invoking License Acceptance Process Tool ...
/usr/lpp/mmfs/4.2.0.0/ibm-java-x86_64-71/jre/bin/java -cp /usr/lpp/mmfs/4.2.0.0/LAP_HOME/LAPApp.jar com.ibm.lex.lapapp.LAP -l /usr/lpp/mmfs/4.2.0.0/LA_HOME -m /usr/lpp/mmfs/4.2.0.0 -s /usr/lpp/mmfs/4.2.0.0  -text_only
** cut **
Product rpms successfully extracted to /usr/lpp/mmfs/4.2.0.0

   Cluster installation and protocol deployment
      To install a new cluster with the Spectrum Scale Install GUI:  /usr/lpp/mmfs/4.2.0.0/installer/spectrumscale installgui start
      To install a cluster or deploy protocols from command line with the Spectrum Scale Install Toolki:  /usr/lpp/mmfs/4.2.0.0/installer/spectrumscale -h
      To install a cluster manually:  Use the gpfs rpms located within /usr/lpp/mmfs/4.2.0.0/gpfs_rpms

** cut **

Now follow either of teh two methods for installing the software


Method 1: Install using the `spectrumscale` tool
------------------------------------------------

First we change directory to the installer and check what our ip addres is
    cd /usr/lpp/mmfs/4.2.0.0/installer/
    hostname -i

Here my ip address is retuned as:
```
10.3.64.150
```

Step 1.4 (optional) In another window, you can do the followign to monitor progress

   cd /usr/lpp/mmfs/4.2.0.0/installer/configuration/
   cat clusterdefinition.txt
    
step 1.3 Run `specturmscale`  several times to create the installation environment
    
    ./spectrumscale setup -i ~/.ssh/id_rsa -s 10.3.64.150 --storesecret

The above defines which node is the base one for installation. In the this case it is the current node.
We also point to our ssh key and allow it to be stored in the config file


``` bash
[ INFO  ] Installing pre requisites for install node
[ INFO  ] Chef successfully installed and configured
[ INFO  ] Your control node has been configured to use the IP 10.3.64.150 to communicate with other nodes.
[ INFO  ] The key located at /root/.ssh/id_rsa, will be used during install.
[ WARN  ] The encryption has been stored in the cluster definition file for convenience. Any passwords will not be securely stored until the secret has been deleted again.
[ INFO  ] Port 8889 will be used for chef communication.
[ INFO  ] Port 10080 will be used for package distribution.
[ INFO  ] SUCCESS
```
Step 1.4 add our first node

    ./spectrumscale node add frvm150 -p
```
[ INFO  ] Adding node frvm150.platform as a GPFS node.
[ INFO  ] Setting frvm150.platform as an protocol node.
[ INFO  ] Configuration updated.
```

Step 1.5 Add our second node

    ./spectrumscale node add frvm151 -a -n -g
````
[ INFO  ] Adding node frvm151.platform as a GPFS node.
[ INFO  ] Adding node frvm151.platform as an NSD server.
[ INFO  ] Setting frvm151.platform as an admin node.
[ INFO  ] Setting frvm151.platform as a GUI server.
[ INFO  ] Configuration updated.
````


Step 1.7 check progress

At any time we can check how the configuration is progressing. This is held in a single file `/usr/lpp/mmfs/4.2.0.0/installer/configuration/clusterdefinition.txt
You can either simply cat this file or better use teh `spectrumscale` tool to decode it

   ./spectrumscale node list
Output is of the form:
``` bash
[ INFO  ] List of nodes in current configuration:
[ INFO  ] [Installer Node]
[ INFO  ] 10.3.64.150
[ INFO  ]
[ INFO  ] [Cluster Name]
[ INFO  ] No cluster name configured
[ INFO  ]
[ INFO  ] [Protocols]
[ INFO  ] Object : Disabled
[ INFO  ] SMB : Disabled
[ INFO  ] NFS : Disabled
[ INFO  ]
[ INFO  ] GPFS Node        Admin  Quorum  Manager  NSD Server  Protocol  GUI Server
[ INFO  ] frvm150.platform                                         X
[ INFO  ] frvm151.platform   X                          X                     X
[ INFO  ]
[ INFO  ] [Export IP address]
[ INFO  ] No export IP addresses configured
```

Step 1.7 Add details of the disk drives to be used as NSDs

Check which block devices you have. In our case as well is teh root filesystem on `/dev/vda`, we will have 6 labeled vdb through vdg
     lsblk |grep ^vd
```
vda           252:0    0   50G  0 disk
vdb           252:16   0    2G  0 disk
vdc           252:32   0    2G  0 disk
vdd           252:48   0    2G  0 disk
vde           252:64   0    2G  0 disk
vdf           252:80   0    2G  0 disk
vdg           252:96   0    2G  0 disk
```
Now add the first 2 disk drives

    ./spectrumscale nsd add /dev/vdb -p frvm151  -fs scratch -u dataAndMetadata -po system
```
[ INFO  ] Connecting to frvm151.platform to check devices and expand wildcards.
[ INFO  ] The installer will create the new file system scratch if it does not exist.
[ INFO  ] Adding NSD nsd1 on frvm151.platform using device /dev/vdb.
```
    ./spectrumscale nsd add /dev/vdc -p frvm151  -fs scratch -u dataAndMetadata -po system
```
[ INFO  ] Connecting to frvm151.platform to check devices and expand wildcards.
[ INFO  ] Adding NSD nsd2 on frvm151.platform using device /dev/vdc.
```

and check
    ./spectrumscale nsd  list
```
[ INFO  ] Name FS      Size(GB) Usage           FG Pool   Device   Servers
[ INFO  ] nsd1 scratch 2        dataAndMetadata 1  system /dev/vdb [frvm151.platform]
[ INFO  ] nsd2 scratch 2        dataAndMetadata 1  system /dev/vdc [frvm151.platform]
```


** Nore  need a ces_shared from say /dev/sdg for later use.

** Can perhaps skip `specrtumscale config ntp ...` as should be already sorted.

    ./spectrumscale config gpfs -c treacle
````
[ INFO  ] Setting GPFS cluster name to treacle
````

Step 1.10 Do the actual Install !

    ./spectrumscale install
    


  (optional) You may be insterested to watch teh progress in another window
  
      tail -f /var/log/messages
      
      
        ./spectrumscale filesystem modify ces_shared -m /gpfs/ces_shared
        
        ./spectrumscale modify gpfs device -m /gpfs/gpfs_device
        
        ./spectrumscale config protocols -e 1.2.3.4,1.2.3.5,1.2.3.6,1.2.3.7
        
  Set the object password      
        
        ./spectrumscale config object -ap
        
        ./spectrumscale config object -dp
        
        ./spectrumscale config enable smb nfs object
        
        ./spectrumscale config object -i 20000 -e 1.2.3.4 -m /gpfs/gpfs_device -f gpfs_device
        
        ./spectrumscale deploy
        
        (optional) can check that everything has been set up.
        
        ./spectrmscale deploy -po
        
        
        



Method 2: Manual Install 
------------------------
