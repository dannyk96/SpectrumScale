# LAB 6: Setting up a Multi-Cluster Connection

Before starting each lab open at least one window to each test node. Use man pages or the online documentation for detailed information on GPFS commands.

## Objectives
In this lab you will 
Enable authentication on two GPFS clusters
Export your filesystem to the other cluster
Remote mount one of their filesystems .
Setup notes
   1.	This lab assumes that you have completed labs 1-4. It should work however even if you have not yet completed all step in labs 3 (replication),4 (additional storage pools and filesets) 
   2.	The concept here is that you are the administrator of one site of a university and you are working with the administrators of the other site. You want to make files and directories from the north site visible at the south site and files and directories from the south site be visible at the north site.


What is your team name ?  			_______________
And the name of the team you are working with? _______________

The team with the highest number needs to rename their cluster (not the filesystem) to be south.
Only do the next on the cluster with the highest numbered site.

   # mmshutdown –a
   # mmchcluster –N south
   # mmstartup –a

Check with mmlscluster that one of the two clusters is now called ‘south’

## Step 1: Enable Remote Authentication

On your cluster do the following
1.	Generate a public/private key pair. The key pair is placed in /var/mmfs/ssl: 
mmauth genkey new

2.	Look at the certificates in /var/mmfs/ssl
cd /var/mmfs/ssl
ls -l
You should see output that includes::
-rw-r--r-- 1 root root 1103 Oct  6 10:46 /var/mmfs/ssl/id_rsa_committed.cert
-rw-r--r-- 1 root root 1813 Oct  6 10:46 /var/mmfs/ssl/id_rsa_committed.pub
-rw-r--r-- 1 root root 1086 Oct  7 15:50 /var/mmfs/ssl/id_rsa_new.cert
-rw-r--r-- 1 root root 1791 Oct  7 15:50 /var/mmfs/ssl/id_rsa_new.pub
lrwxrwxrwx 1 root root   28 Oct  7 15:50 /var/mmfs/ssl/id_rsa.pub -> /var/mmfs/ssl/id_rsa_new.pub


3.	Enable authorization by issuing: 
mmauth update . -l AUTHONLY

mmauth genkey commit

4.	Send the file id_rsa.pub to the people in your partner team
You can use email, usb stick, carrier pigeon, twitter or just scp:
scp id_rsa.pub  <cluster2-node1>:/tmp/id_rsa.pub.<team #>


5.	Once you have their file in /tmp do the following:
Use the mmauth add command to authorize the other cluster to mount file systems owned by you.
 If you are north type:
 mmauth add south.platform -k /tmp/id_rsa.pub.<otherteam#>
If you are south type:
 mmauth add north.platform -k /tmp/id_rsa.pub.<otherteam#>

Step 2: Remote mount a file system

1.	On your cluster issue the mmauth grant command to authorize the other cluster to mount one or more of your GPFS  file systems.

If you are north type:
 mmauth grant south –f /dev/<team#> -a rw  (or use ro for read-only access)
If you are south type:
 mmauth grant north –f /dev/<team#> -a rw  (or use ro for read-only access)

2.	On your cluster define the cluster name, contact nodes and public key for the other cluster.
If you are north type:
mmremotecluster add south.platform -n <node1>,<node2> -k /tmp/id_rsa.pub.<other team#>
If you are south type:
mmremotecluster add north.platform -n <node1>,<node2> -k /tmp/id_rsa.pub.<other team#>

Note:  
Node1 and Node2 are nodes in the *other* cluster. The hostname or IP address that you specify must refer to the communications adapter that is used by GPFS as given by the mmlscluster command on a node in the other cluster.

3.	On your system, the sysadmin issues one or more mmremotefs commands to identify the file systems in the other cluster to be accessed by nodes in your cluster: 
If you are north type:
mmremotefs add <otherteam#> –f <other_team#> -C south.platform -T /gpfs/<otherteam#>
If you are south type:
mmremotefs add <otherteam#> –f <other_team#> -C north.platform -T /gpfs/<otherteam#>

Check that this has worked with 
mmremotefs show all

Output should be similar to
Local Name  Remote Name  Cluster name       Mount Point        Mount Options    Automount  Drive  Priority
team99      team99       south.platform     /gpfs/team99       rw               no           -        0

4.	Mount a remote file system on your cluster on your cluster

mmmount <otherteam#>




 
Step 3: View information about a remote cluster
	r07s6vlp2]# mmdf <otherteam#>

disk                disk size  failure holds    holds              free KB             free KB
name                    in KB    group metadata data        in full blocks        in fragments
--------------- ------------- -------- -------- ----- -------------------- -------------------
Disks in storage pool: system (Maximum disk size allowed is 242 GB)
nsd1                  2097152      101 Yes      Yes          966656 ( 46%)          3424 ( 0%)
nsd2                  2097152      102 Yes      Yes          964608 ( 46%)          4608 ( 0%)
                -------------                         -------------------- -------------------
(pool total)          4194304                               1931264 ( 46%)          8032 ( 0%)

Disks in storage pool: mynewarray (Maximum disk size allowed is 242 GB)
nsd03                 2097152      103 No       Yes         1977344 ( 94%)          1888 ( 0%)
nsd04                 2097152      103 No       Yes         1977344 ( 94%)          1888 ( 0%)
                -------------                         -------------------- -------------------
(pool total)          4194304                               3954688 ( 94%)          3776 ( 0%)

                =============                         ==================== ===================
(data)                8388608                               5885952 ( 70%)         11808 ( 0%)
(metadata)            4194304                               1931264 ( 46%)          8032 ( 0%)
                =============                         ==================== ===================
(total)               8388608                               5885952 ( 70%)         11808 ( 0%)

Inode Information
-----------------
Number of used inodes:            4110
Number of free inodes:           47602
Number of allocated inodes:      51712
Maximum number of inodes:        51712

Why not see if they have also completed all the previous exercises compared to you? :-)

diff /gpfs/team<#> /gpfs/<otherteam#>

And finally try writing a file

Cd /gpfs/<otherteam#>

echo “Hello from <John Smith> and <Pierre Blanchard>. Please rsvp” > Hello_from_<team#>.txt

Ask the other team to see if the see this file in their GPFS filesystem and if so to edit it with vi and add a reply adding their own two names on a new line.

Enjoy!

