<!--The OpenShift `diy` cartridge documentation can be found at:-->

<!--https://github.com/openshift/origin-server/tree/master/cartridges/openshift-origin-cartridge-diy/README.md-->

Nexus as diy application on OpenShift
=====================================

This simple application will enable you to run Nexus maven repository manager 
on Apache Tomcat in your OpenShift's diy gear.
It downloads Tomcat and Nexus war and installs them in your OpenShift gear.


Installation instructions
-------------------------
1. You need to create diy application in your OpenShift account.

	It can be done by `rhc` client tool:

		rhc app create -a nexus -t diy-0.1
	
	Another way is to create it manually from your OpenShift web console:  
	Click on 'Add Application', scroll all the way down and select 'Do-It-Yourself 0.1', 
	insert name of your application (most likely `nexus`) into 'Public URL' field 
	and click on 'Create Application'.
	
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

### Tomcat and Nexus versions
You can change versions of Tomcat or Nexus in `.openshift/action_hooks/deploy` 
file by editing `$TOMCAT_VER` or `$NEXUS_VER` variables. 

If you had already Tomcat and Nexus installed by this application, you can force 
installation of new versions by deleting `$OPENSHIFT_DATA_DIR/tomcat/` directory 
in your OpenShift gear:

```
ssh <username>@<nexus>-<your-subdomain>.rhcloud.com
rm -rf $OPENSHIFT_DATA_DIR/tomcat/
```

### Activate http protocol
By default this application configures Tomcat and Nexus to use only https protocol.
To activate http protocol comment out this lines in `.openshift/action_hooks/deploy` 
file:

```
cp $OPENSHIFT_DATA_DIR/tomcat/conf/context.xml \
		$OPENSHIFT_DATA_DIR/tomcat/conf/context.xml.original
delta_file="$OPENSHIFT_REPO_DIR/diy/tomcat/conf/context.xml.add-remoteIpValve"
echo -e "/<\/Context>/-1r $delta_file\n%w" \
		| ed -s $OPENSHIFT_DATA_DIR/tomcat/conf/context.xml
cp $OPENSHIFT_DATA_DIR/tomcat/webapps/ROOT/WEB-INF/web.xml \
		$OPENSHIFT_DATA_DIR/tomcat/webapps/ROOT/WEB-INF/web.xml.original
delta_file="$OPENSHIFT_REPO_DIR/diy/nexus/WEB-INF/web.xml.add-https-security-constraint"
echo -e "/<\/web-app>/-1r $delta_file\n%w" \
		| ed -s $OPENSHIFT_DATA_DIR/tomcat/webapps/ROOT/WEB-INF/web.xml
```

As previously if you had already Tomcat and Nexus installed by this application, 
you must delete `$OPENSHIFT_DATA_DIR/tomcat/` directory in your OpenShift gear 
to force reinstallation. Otherwise changes you had just made won't be effective.


References
----------
 * [nexus on OpenShift](https://www.openshift.com/blogs/nexus-repository-manager-in-the-cloud-for-free-with-openshift)
 * [original work from which this app is derived](https://github.com/shekhargulati/nexus)
 * [run Tomcat 8 on OpenShift](https://www.openshift.com/blogs/how-to-run-apache-tomcat-8-on-openshift)
 * [tutorial for diy application on OpenShift](https://www.openshift.com/developers/do-it-yourself)
 * [how to redirect http to https on OpenShift](https://www.openshift.com/kb/kb-e1044-how-to-redirect-traffic-to-https)
