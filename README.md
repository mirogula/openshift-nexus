<!--The OpenShift `diy` cartridge documentation can be found at:-->

<!--https://github.com/openshift/origin-server/tree/master/cartridges/openshift-origin-cartridge-diy/README.md-->

Nexus as diy application on OpenShift
=====================================

This simple application will enable you to run Nexus maven repository manager 
on Apache Tomcat in your OpenShift's diy gear.
It downloads Tomcat and Nexus war and installs them in your OpenShift gear.


Instructions
------------
1. You need to create diy application in your OpenShift account.

	It can be done by `rhc` client tool:

		rhc app create -a nexus -t diy-0.1
	
	Another way is to create it manually from your OpenShift web console:  
	Click on 'Add Application', scroll all the way down and select 'Do-It-Yourself 0.1', 
	insert name of your application into 'Public URL' field and click on 'Create Application'.
	
2. Clone git repository of your newly created application, and then merge it with 
	this repository:

		git remote add openshift-nexus https://github.com/mirogula/openshift-nexus.git
		git pull -s recursive -X theirs openshift-nexus master 
		git push

	Or you can just copy content of `diy/` directory and `.openshift/action_hooks/` 
	directory from this repository to your application repository.

3. Finally commit and push changes back to OpenShift.


Configuration
-------------
Optionally you can change versions of Tomcat or Nexus in `.openshift/action_hooks/deploy` 
file by editing `$TOMCAT_VER` or `$NEXUS_VER` variables. 

If you already had Tomcat and Nexus installed by this application, you can force 
installation of new versions by deleting `$OPENSHIFT_DATA_DIR/tomcat/` directory 
in your OpenShift gear:

```
ssh <username>@<app-name>-<domain>.rhcloud.com
rm -rf $OPENSHIFT_DATA_DIR/tomcat/
```

References
----------
 * [nexus on OpenShift](https://www.openshift.com/blogs/nexus-repository-manager-in-the-cloud-for-free-with-openshift)
 * [original work from which this app is derived](https://github.com/shekhargulati/nexus)
 * [run Tomcat 8 on OpenShift](https://www.openshift.com/blogs/how-to-run-apache-tomcat-8-on-openshift)
 * [tutorial for diy application on OpenShift](https://www.openshift.com/developers/do-it-yourself)
 * [how to redirect http to https on OpenShift](https://www.openshift.com/kb/kb-e1044-how-to-redirect-traffic-to-https)
