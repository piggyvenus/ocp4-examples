# From Local Environment to Integration

## Access Launcher and download the quick start for the frontend application

  * Go to Launcher: https://launcher-launcher-infra.apps.rhte2019.sc.osecloud.com
  * Click `Create a New Application`
  * Enter your application name: rhtedemo5
  * Click `Authorize` and login to github
  * Click `Configure a Frontend`
  * Select `Angular`
  * Click `Save`
  * Click `Download`
  * Click onto the link `Download.zip` to download the zip file (rhtedemo5.zip)

## Create Code Ready Container (CRC) - Local OpenShift 4 cluster

 * Go to https://cloud.redhat.com
 * Click `Create cluster`
 * Scroll to the bottom and Click `Laptop`
 * Copy/Download the pull secret to somewhere
 * Download one of the following links to download the CRC binary
     * Windows: Download (HyperV)
     * macOS: Download (HyperKit)
     * Linux: Download (Libvirt)
 * Save the download file the you local filesystem and extract it
 * Add the `crc` binary to the $PATH
 * Run `crc setup`
 * Run `crc config set memory 12288`
 * Run `crc start`
```
crc start
INFO Checking if running as non-root              
INFO Checking if oc binary is cached              
INFO Checking if HyperKit is installed            
INFO Checking if crc-driver-hyperkit is installed
INFO Checking file permissions for /etc/resolver/testing
INFO Checking file permissions for /etc/hosts     
? Image pull secret [? for help]
```
 * Copy the pull secret from the previous step and paste it when you are prompt
 * hit enter and wait
 ```
 INFO Extracting bundle: crc_hyperkit_4.2.0-0.nightly-2019-09-26-192831.crcbundle ...
 INFO Creating CodeReady Containers VM for OpenShift 4.2.0-0.nightly-2019-09-26-192831...
 INFO Verifying validity of the cluster certificates ...
 INFO Restarting the host network                  
 INFO Check internal and public dns query ...      
 INFO Copying kubeconfig file to instance dir ...  
 INFO Adding user's pull secret and cluster ID ...
 INFO Starting OpenShift cluster ... [waiting 3m]  
 INFO                                              
 INFO To access the cluster, first set up your environment by following 'crc oc-env' instructions
 INFO Then you can access it by running 'oc login -u developer -p developer https://api.crc.testing:6443'
 INFO To login as an admin, username is 'kubeadmin' and password is xxxxx-xxxxx-xxxxx-xxxxx
 INFO                                              
 INFO You can now run 'crc console' and use these credentials to access the OpenShift web console
 ```
Now, your CRC is up!

## Setup Application and start IDE (VSCODE)
 * Move the download zip from launcher
 * Extract the zip file
 * Change directory into the extracted directory
 * Create github repo and commit project
 ```
 $ curl -u 'USER' https://api.github.com/user/repos -d '{"name":"rhtedemo5"}'
 $ git init
 $ git add .
 $ git commit -m "first commit"
 $ git remote add origin https://github.com/piggyvenus/rhtedemo5.git
 $ git push -u origin master
 $ code .
 ```
## Deploy Application Locally
 * In the VSCODE, Go to `Terminal` -> `New Terminal`
 * In the terminal, enter `oc login -u developer -p developer https://api.crc.testing:6443`
 * Run: `oc new-project rhtedemo5`
 * Run: `./gap deploy`
 * Run: `crc console` to bring up the OpenShift console

## Modify the Frontend Application
 * cd ~
 * git clone https://github.com/piggyvenus/ocp4-examples.git
 * cp ~/ocp4-examples/rhte2019/index.html src/
 * cp ~/ocp4-examples/rhte2019/app.component.html src/app/
 * Remove line `templateUrl: './app.component.html',` from src/app/app.component.ts
 * Git commit via VSCODE
 * Rebuild from the OpenShift Console
 * Test the route of the application via browser

## Create Pipeline in the Integrated Environment
 * login to the integrated OCP4 cluster
 * oc new-project rhtedemo5
 * setup permissions for Pipeline
 ```
 $ oc create serviceaccount pipeline
 $ oc adm policy add-scc-to-user privileged -z pipeline
 $ oc adm policy add-role-to-user edit -z pipeline
 $ oc adm policy add-role-to-user edit -z default

 ```
 * Create OpenShift deployment related objects
 ```
 $ oc create -f ocp-objects.yaml
 ```
 * Create Tasks
 ```
 $ oc create -f openshift-client-task.yaml
 $ oc create -f s2i-nodejs-task.yaml
 $ tkn task list
 ```
 * Create pipeline resources
 ```
 $ oc create -f pipeline-resources.yaml
 $ tkn resource list
 $ oc create -f pipeline.yaml
 $ tkn pipeline list
 ```
 * Start pipeline
 ```
 tkn pipeline start frontend-deploy-pipeline -r app-git=frontend-git -r app-image=frontend-image -s pipeline
 ```
 * Watch the pipeline log
 This command is from the return information from the previous command.

 ```
 tkn pipelinerun logs frontend-deploy-pipeline-run-scc72 -f -n rhtedemo5
 ```

## Test the application via the browser
