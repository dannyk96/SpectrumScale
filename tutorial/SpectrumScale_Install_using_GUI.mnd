Installing Spectrunm Scale using the GUI
========================================

The Spectrum Scale Installer GUI provides a web-accessable GUI for cluster install

The GUI is new in 4.2 and as such there are many features that will be added in future releases. 
For example the GUI can be used for a fresh install and not for an upgrade, nor can it be used to add addional nodes to the GPFS cluster
once teh first node has been installed and a GPFS cluster created (!)

     cd /usr/lpp/mmfs/4.2.0.0/installer/
     ./spectrumscale installgui start
     
     Output will be of the form:
     ```
[ INFO  ] Starting install toolkit Graphical User Interface on t11vm71
[ INFO  ] Enter the following URL with the resolvable hostname or IP of t11vm71
[ INFO  ]  in your browser in order to
[ INFO  ] launch the Install GUI:
[ INFO  ] https://<Hostname or IP>:9443/gui
[ INFO  ] Enter the following password:
[ INFO  ] Passw0rd
```

Point your desktop web browser at:

>   https://10.3.64.71:9443/gui
   
password is "Passw0rd". It appears that this is hard coded in the file ` cli/gui.py`
```
cli/gui.py:    jvm_args = ("-Dcom.ibm.gpfs.install.password=Passw0rd"
```
Yes this does look dodgy coding. It will be fixed in future releases.


