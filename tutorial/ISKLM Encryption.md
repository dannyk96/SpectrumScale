
Spectrum Scale File Encryption with ISKLM
=========================================

This lab shows the steps to add encryption of individual files

See the [Base GPFS setup](Software_Install.md) and teh [GPFS cluster creation](Create Cluster) for information. 

See the [Spectrum Scale Concepts, Planning and Installation Guide] [GPFSInstall] 


#### 3 Export the certificate to a file

On the ISKLM server, enter the built-in shell of ISKLM

       cd /opt/IBM/WebSphere/AppServer/bin/
       ./wsadmin.sh -username SKLMAdmin -password Passw0rd -lang jython
Output will be:
```
WASX7209I: Connected to process "server1" on node SKLMNode using SOAP connector;  The type of process is: UnManagedProcess
WASX7031I: For help, enter: "print Help.help()"
wsadmin>
```
Show the certificate
      wasadmin> print AdminTask.tklmCertList('[-alias cert1_label]')
Output will be 
```
CTGKM0001I Command succeeded.

uuid = CERTIFICATE-c00f107e-6970-44ff-9225-09c86c17dd85
alias = cert1_label
key store name = defaultKeyStore
key state = ACTIVE
issuer name = CN=cert1
subject name = CN=cert1
creation date = 3/16/15 2:02:13 PM India Standard Time
expiration date = 3/15/18 2:02:13 PM India Standard Time
serial number = 657606986627
```

Export the certificate as a file
      wsadmin> print AdminTask.tklmCertExport('[-uuid CERTIFICATE-c00f107e-6970-44ff-9225-09c86c17dd85 -format base64 -fileName /root/srvcert]')
Output will be:
```
CTGKM0001I Command succeeded.
/root/srvcert
```

#### 4 Create a new device group 

#### 5 Create a Keystore using this ISKLM Certificate

  mkdir /var/mmfs/etc/RKMcerts

Now check you have a certificate
    ls -lrth /root/srvcert
Output wil lbe like:
  -rw-r--r--. 1 root root 1.1K Mar 16 18:56 /root/srvcert

Add this ISKLM certifcxate to Spectrum Scale
      mmauth gencert --cname GPFS_TENANT1 --label client_label --cert /root/srvcert --out /var/mmfs/etc/RKMcerts/ISKLM.p12 --pwd client_label
And check
      # ls -lrth /var/mmfs/etc/RKMcerts/ISKLM.p12
-rw-------. 1 root root 4.0K Mar 17 11:55 /var/mmfs/etc/RKMcerts/ISKLM.p12

If you are interested, look at this entry
    cat /var/mmfs/etc/RKM.conf
```
ISKLM_srv {
  type = ISKLM
  kmipServerUri = tls://9.118.46.18:5696
  keyStore = /var/mmfs/etc/RKMcerts/ISKLM.p12
  passphrase = client_label
  clientCertLabel = client_label
  tenantName = GPFS_TENANT1
  ```
  
  Note the ip address of the ISKLM server and the pointer to the file where the encryption key is held
  
*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
    viverra nec, fringilla in, laoreet vitae, risus.
*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
    Suspendisse id sem consectetuer libero luctus adipiscing.
    
    
[GPFSInstall]: http://publib.boulder.ibm.com/epubs/pdf/a7604412.pdf "Spectrum Scale Installation Guide"
