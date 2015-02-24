JBoss Operations Network Showcase
==================================

A. Synopsis
===========

What this is about
------------------
This demo project showcases some of the most common use cases regarding JBoss Operations Network, Red Hat's leading enterprise middleware systems management tooling (more information on JON can be found here: http://www.redhat.com/de/technologies/jboss-middleware/operations-network).

- Automated provisioning of resources (e.g. JBoss Enterprise Application Platform)
- Integration of custom JMX MBeans into JON

The resources provided with this showcase are intended as a starting point and shall help you to setup these use cases in your own JBoss Operations Network environment.

Screenshots - Automated provisioning of resources
------------------
![1. Deploy Bundle - Select Bundle](./doc/11_provisioning_03_deploy_bundle_01.png)
![2. Deploy Bundle - Select Bundle](./doc/11_provisioning_03_deploy_bundle_05_deployment_summary.png)
![3. Deploy Bundle - Select Bundle Version](./doc/11_provisioning_03_deploy_bundle_03_select_version.png)
![4. Deploy Bundle - Enter input vars](./doc/11_provisioning_03_deploy_bundle_04_enter_input_vars.png)
![5. Deploy Bundle - Bundle Deployment successful](./doc/11_provisioning_03_deploy_bundle_05_deployment_success.png)
![6. Inventory - Import EAP instance](./doc/11_provisioning_04_inventory_01_import.png)
![7. Inventory - JBoss EAP successfully imported](./doc/11_provisioning_04_inventory_03_eap_ready.png)


Screenshots - Integration of custom JMX MBeans
------------------
![1. Custom MBean overview](./doc/01_custom_mbean_overview.png)
![2. Execute an operation on a custom MBean](./doc/02_custom_mbean_execute_operation.png)
![3. View the operations history](./doc/03_custom_mbean_execute_operation_history.png)
![4. Monitoring of custom MBean metrics](./doc/04_custom_mbean_monitoring.png)


B. Base installation and prerequisites
==============================================

1. JBoss Operations Network (JON)
---------------------------------
Follow the official installation instructions for JON V3.3 here: https://access.redhat.com/documentation/en-US/Red_Hat_JBoss_Operations_Network/3.3/html/Installation_Guide/index.html

2. Apache Maven
---------------
You will need to install Apache Maven for building the source code for this showcase. Please follow the installation instructions here: http://maven.apache.org/download.cgi



C. Use Case - Automated provisioning of resources
==============================================

1. Benefits
-----------
This use case describes how to create a standardised JBoss Enterprise Application Platform (EAP) installation package. In terms of JON this is a so called "Bundle". This Bundle can then be provisioned to a target system (or even multiple) in a highly automated fashion.

More information on the provisioning features can be found in the official JON documentation: https://access.redhat.com/documentation/en-US/Red_Hat_JBoss_Operations_Network/3.3/html/Users_Guide/pt04.html.

2. Showcase components
-------------------
1. eap-bundle - This project provides the core ingredients for creating a bundle to provision instances of JBoss Enterprise Application Platform V6.3.


3. Installation
---------------
1. Create Bundle Group "JBoss EAP V6 Base Installation"
![Create Bundle Group](./doc/11_provisioning_01_create_bundle_group.png)

2. Define a new bundle
![Create Bundle - Upload Recipe](./doc/11_provisioning_02_create_bundle_01.png)
![Create Bundle - Assign Bundle Group](./doc/11_provisioning_02_create_bundle_02_assign_bundle_group.png)
![Create Bundle - Upload Bundle Files](./doc/11_provisioning_02_create_bundle_03_upload_bundle_files.png)
![Create Bundle - Summary](./doc/11_provisioning_02_create_bundle_04_bundle_summary.png)

3. Deploy the bundle to a target system
![Deploy Bundle - Select Bundle](./doc/11_provisioning_03_deploy_bundle_01.png)
![Deploy Bundle - Select Bundle](./doc/11_provisioning_03_deploy_bundle_05_deployment_summary.png)
![Deploy Bundle - Define Destination](./doc/11_provisioning_03_deploy_bundle_02_define destination.png)
![Deploy Bundle - Select Bundle Version](./doc/11_provisioning_03_deploy_bundle_03_select_version.png)
![Deploy Bundle - Enter input vars](./doc/11_provisioning_03_deploy_bundle_04_enter_input_vars.png)
![Deploy Bundle - Deployment scheduled](./doc/11_provisioning_03_deploy_bundle_05_deployment_scheduled.png)
![Deploy Bundle - Bundle Deployment successful](./doc/11_provisioning_03_deploy_bundle_05_deployment_success.png)
![Deploy Bundle - Filesystem view on deployment](./doc/11_provisioning_03_deploy_bundle_05_deployment_success_fs.png)

4. Import the newly created EAP instance into JON inventory
![Inventory - Import EAP instance](./doc/11_provisioning_04_inventory_01_import.png)

5. Configure connection settings
![Inventory - Configure connection settings](./doc/11_provisioning_04_inventory_02_configure_password.png)

6. Start the server
![Inventory - JBoss EAP successfully imported](./doc/11_provisioning_04_inventory_03_start_server.png)
![Inventory - JBoss EAP successfully imported](./doc/11_provisioning_04_inventory_03_eap_ready.png)



D. Use Case - Integration of custom JMX MBeans
==============================================
1. Benefits
-----------
It is a very common scenario that your applications have been enhanced with MBeans for management, runtime configuration or even monitoring capabilities. JBoss Operations Network allows you to integrate these MBeans into its management environment, which actually means that you can then leverage JON's full systems management and monitoring capabilities:
- Historical monitoring of your MBeans
- Dashboarding and alerting
- Execution of MBean operations


2. Showcase components
-------------------
1. helloworld-mbean - JEE application that shows different ways of exposing MBeans (taken from EAP quickstarts at https://github.com/jboss-developer/jboss-eap-quickstarts/).
2. custom-jmx-plugin - JBoss Operations Network Agent plugin that integrates custom MBeans (provided by helloworld-mbean) via JMX into JON's monitoring & dashboarding.


3. Installation
---------------
### 1. Build and deploy the MBean provider application

Build the application with Maven:
```
# OPTIONAL: Copy the provided settings.xml to your local maven conf dir
cp ./doc/settings.xml ~/.m2/

# Start the build
cd ./helloworld-mbean
mvn clean install

# Hot deploy the application to an instance of JBoss EAP
# you could use the instance that has been provisioned in the use case "Automated provisioning of resources"
cp ./helloworld-mbean-webapp/target/jboss-helloworld-mbean-helloworld-mbean-webapp.war <EAP_INSTALLATION_DIR>/standalone/deployments
```


### 2. Build and deploy the agent plugin
Build the application with Maven:

```
# OPTIONAL: Copy the provided settings.xml to your local maven conf dir
cp ./doc/settings.xml ~/.m2/

# Start the build
cd ./custom-jmx-plugin
mvn clean install
```

Deploy the agent plugin:
```
cp ./target/custom-jmx-plugin-1.0-SNAPSHOT.jar <JON_SERVER_INSTALL_DIR>/plugins/
```

The JON server periodically scans the "plugins" directory for updates and will pickup the agent plugin after some time, which is finally pushed to the connected JON agents. The agents might need to be restarted to detect the plugin after initial installation.

In the end you should see a service group called "Myapp Services" that contains the applications' MBeans. You can now start to add the contained metrics to custom dashboard, define alerts on it and so on.

![1. Custom MBean overview](./doc/01_custom_mbean_overview.png)

Z. Appendix
===========
* JBoss Operations Network Plugin Development Guide - https://access.redhat.com/documentation/en-US/JBoss_Operations_Network/3.1/html/Dev_Writing_Custom_Plug-ins/index.html
* RHQ Agent Plugin HowTo - http://rhq-project.github.io/rhq-agent-plugin-plugin/index.html
* JBoss EAP Quickstarts - https://github.com/jboss-developer/jboss-eap-quickstarts/
