## KIE BAM Cartridge for OpenShift

Summary
-------
This cartridge provides the **_KIE Dashboard_** for easy deployment to OpenShift.

Deployment
----------

To try out the KIE Dashboard on OpenShift please follow the instructions:

If you want to use the [OpenShift create application page](https://openshift.redhat.com/app/console/application_types), enter the cartridge URI of **https://raw.githubusercontent.com/marianbuenosayres/openshift-cartridge-kiebam/master/metadata/manifest.yml** in the entry field (at the bottom left of the form).

Or if you want to use the [rhc command line](https://www.openshift.com/developers/rhc-client-tools-install) type:

    rhc create-app -g medium <APP NAME> https://raw.githubusercontent.com/marianbuenosayres/openshift-cartridge-kiebam/master/metadata/manifest.yml

This will output the generated users and passwords for KIE Dashboard.

You can use them to login into the KIE Dashboard.


Usage
-----

**Access the web application**

* In order to access the KIE Dashboard navigate to <code>http://&lt;app_name&gt;-&lt;rh_domain&gt;.&lt;domain&gt;:&lt;port&gt;/dashbuilder</code>   

**Access into the application shell**

To access into the application shell:

	rhc ssh <APPLICATION-ID>

**Manage users and roles**

When you install the cartridge, several users and roles are created. Their passwords are automatically generated for security reasons and displayed during cartridge installation. The following table summarizes this initial setup:

<table>
<tr>
	<th>User</th>
	<th>Description</th>
	<th>Roles</th>
</tr>
<tr>
	<td>p3-admin</td>
	<td>BPM Administrator</td>
	<td>admin</td>
</tr>
<tr>
	<td>p3-analyst</td>
	<td>Process analyst role</td>
	<td>analyst</td>
</tr>
<tr>
	<td>p3-manager</td>
	<td>BPM Manager</td>
	<td>manager</td>
</tr>
<tr>
	<td>p3-user</td>
	<td>BPM User</td>
	<td>user</td>
</tr>
</table>

* The application developer can show/add/remove/modify users and roles by using the <code>kiebam-users.properties</code> and <code>kiebam-roles.properties</code> files found in the cartridge git repository at <code>.openshift/config/</code> directory     
* Once users or roles files changed, do a <code>git push</code> in order to deploy the new files in the application container configuration path.         
* To display the current list of users and their passwords, you can simply run this command in your cartridge repository:

        cat .openshift/config/kiebam-users.properties
    
* The current assignation of roles can be displayed by running this command in your cartridge repository:

        cat .openshift/config/kiebam-roles.properties
        
* To create new users, change their passwords or role assignation, you must edit the following files in your cartridge repository.

        vi .openshift/config/kiebam-users.properties
        vi .openshift/config/kiebam-roles.properties

**Manage JBoss EAP configuration**

* The main configuration file for JBoss EAP is <code>standalone.xml</code>
* This file is available in your cartridge repository at location <code>.openshift/config/standalone.xml</code>
* Usefull for changing container configurations such as root logger level and so on

**Manage welcome root JBoss 7 web application**

* The developer cartridge git repository maven project generates a WAR artifact that is used as <code>welcome-root</code> webapp for the application URL.
* You can modify this application and when running a <code>git push</code> in your cartridge git repository, it is built and re-deployed in JBoss EAP.
* The generated WAR artifact is installed in the cartridge local repository, so it can be used as a Maven dependency too.

**Debugging application code**

If you need to debug your application code, you must enable the <code>enable_jpda</code> marker in the cartridge git repository:

    cd <app_name>/.openshift/markers/
    touch enable_jpda
    git add enable_jpda
    git commit enable_jpda -m "message"
    git push

Once the push is completed successfully, next step is to perform port forwarding:

    rhc port-forward <app_name>

You should see something similar to:

    Service Local               OpenShift
    ------- -------------- ---- ----------------
    java    127.0.0.1:3528  =>  127.1.244.1:3528
    java    127.0.0.1:4447  =>  127.1.244.1:4447
    java    127.0.0.1:5445  =>  127.1.244.1:5445
    java    127.0.0.1:8080  =>  127.1.244.1:8080
    java    127.0.0.1:8787  =>  127.1.244.1:8787
    java    127.0.0.1:9520  =>  127.1.244.1:9520
    java    127.0.0.1:9521  =>  127.1.244.1:9521
    java    127.0.0.1:9990  =>  127.1.244.1:9990
    java    127.0.0.1:9999  =>  127.1.244.1:9999

The debug port is <code>8787</code>. Now you can do remote debugging to <code>127.0.0.1:8787</code>.


