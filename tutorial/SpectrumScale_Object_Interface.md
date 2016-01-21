Spectrum Scale Object Interface
===============================

Create /etc/yum.repos.d/spectrumscale.repo and add
```
[spectrum_scale]
name=spectrum_scale
baseurl=file:///usr/lpp/mmfs/4.2.0.0/object_rpms
enabled=1
```
Now install the RPM plus all of its dependancies such httpd etc. (there are 27 of them!)

   yum -y install spectrum-scale-object

Try and setup the object interface

    mmobj swift base -g /scratch/ -o object_fileset --cluster-hostname t11vm71 --local-keystone --admin-password passw0rd
Output will be similar to:
```
[E] Could not run /usr/bin/openstack-config.  Verify the Object protocol has been installed.
mmcesobjcrbase: Command failed. Examine previous error messages to determine cause.
```
#### Enable across all protocol nodes
Complete the configuration and start object services on all protocol nodes by using the mmces service
enable command.

    mmces service enable OBJ -a
