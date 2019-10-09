# Demo script
##Preparation
* Launcher up
* CRC is running
* Close all other application


## Laucher
* login as user1
* create new app call angularfrontend
* configure frontend app using AngularJS
* Download the application zip file

## setup the git repo and start the IDE
* unzip the application zip to a location
* cd to the application directory
* run the follow:
```
curl -u 'piggyvenus' https://api.github.com/user/repos -d '{"name":"angularfrontend"}'
git init
git add .
git commit -m "first commit"
git remote add origin https://github.com/piggyvenus/angularfrontend.git
git push -u origin master
code .
```

## Modify the Frontend Application
* In the VSCODE, Go to Terminal -> New Terminal
* In the terminal, enter oc login -u developer -p developer https://api.crc.testing:6443
* Run: oc new-project quarkusbackend
* Run: ./gap deploy
* Run: oc cancel-build bc/angularfrontend
* Run: oc cancel-build bc/angularfrontend-welcome
* cp ~/ocp4-examples/rhte2019/index.html src/
* cp ~/ocp4-examples/rhte2019/app.component.html src/app/
* Remove line templateUrl: './app.component.html', from src/app/app.component.ts
* Git commit via VSCODE
* oc start-build angularfrontend
* Run: crc console to bring up the OpenShift console --> checking build
* ***SWitch back to Veer***
* go the the frontend application http://angularfrontend-quarkusbackend.apps-crc.testing/

## Moving on to Integration
* oc login -u user1 https://api.ocp4.home.ocpcloud.com:6443/
* oc project quarkusbackend
* cd /Users/shannachan/RHTE/pipeline
* oc create -f ocp-objects.yaml
* oc create -f s2i-nodejs-task.yaml
* oc create -f pipeline-resources.yaml
* oc create -f pipeline.yaml
* tkn pipeline start angularfrontend-deploy-pipeline -r app-git=angularfrontend-git -r app-image=angularfrontend-image -s pipeline
* ***SWitch back to Veer***
* tkn pipeline list
* go to http://angularfrontend-quarkusbackend.apps.ocp4.home.ocpcloud.com/
