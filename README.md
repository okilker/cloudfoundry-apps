 Application Instrumentation using AppDynamics
================================================================================

Prerequisites.
================================================================================

- CloudFoundry Environment with Appdynamics Tile installed and configured with Controller(s) information.
- The Cloud Foundry Command Line Interface (cf CLI).
   See [Installing the cf CLI](https://docs.pivotal.io/pivotalcf/cf-cli/install-go-cli.html)
- Sample Application. 


To Use
================================================================================

- Make sure the appdynamics service is available by using the `cf marketplace` command 

```
pavan.krishna@OSXLTPKrishna:~/pcf-dash-generator$ cf marketplace
Getting services from marketplace in org appdynamics-org / space dev as admin...
OK

service                       plans                          description
app-autoscaler                standard                       Scales bound applications in response to load
appdynamics                   443Controller, 450Controller   Appdynamics Monitoring Platform
```

- Create a service instance of appdynamics plan (controller configuration) that we want to expose to the application we are pushing. `cf create-service appdynamics <plan/controller_config> <name_of_the_application>`

```
pavan.krishna@OSXLTPKrishna:~/pcf-dash-generator$ cf create-service appdynamics 443Controller appd443
Creating service instance appd443 in org appdynamics-org / space dev as admin...
OK
```

If you already have an instance for the plan of your choice, you do not have to create another one. You can reuse the same instance across multiple applications. 

```
pavan.krishna@OSXLTPKrishna:~/pcf-dash-generator$ cf services
Getting services in org appdynamics-org / space dev as admin...

name      service       plan            bound apps              last operation
appd443   appdynamics   443Controller                          create succeeded
```



- Edit the manifest.yml file to include the service instance you created so that the application binds to the instance. 
Add `appd443` to the services section:

```
  services:
    - appd443
```

The `manifest.yml` file will resemble:

```
---
applications:
- name: spring-music
  memory: 1G
  random-route: true
  path: build/libs/spring-music-1-1.0.jar
  services:
    - appd443
```

- Push the application using `cf push`

```
pavan.krishna@OSXLTPKrishna:~/pcf-dash-generator$ cf push 
```

Once it is pushed, you can generate traffic and will notice the application getting instrumented on AppDynamics Controller.  
