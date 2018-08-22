# kubernetes-kong-with-dashboard
The yml scripts to run kong on kubernetes with a dashboard

# Note

This is for local testing with NodePort, not for production or cloud use. 
I also used postgres.

## Original yml scripts can be found here

https://github.com/Kong/kong-dist-kubernetes

## Kong kubernetes setup documentation here:

https://docs.konghq.com/install/kubernetes/

## Steps to set up kong locally using kubernetes and docker for mac

### Enable kubernetes with docker for mac

1. Click on docker preferences
1. Click on the Kubernetes tab
1. Select enable kubernetes checkbox and click on the kubernetes radio button

Note: Make sure kubernetes has access to internet, if it does not start up, check internet connection. If you run on a VPN that has strict security firewalls, that might be preventing kubernetes from installing. 

### Update type

In order for kong to run locally you need to update the type from LoadBalancer to NodePort.

Also make sure the kong version you are using is supported by the kong dashboard image. At the time of writing this only kong version under 0.14 are supported. So I updated the version of kong to 0.13 in the yaml scripts.

Updated kong tag to 0.13

### Commands:

    kubectl create -f postgres.yaml    

    kubectl create -f kong_postgres.yaml

    kubectl create -f kong_migration_postgres.yaml

Check the service ip for 

    kubectl get svc

Copy the ip of the kong-admin service and paste it in kong_dashboard.yml as an "args" value, eg:

When you run "$ kubectl get service" you might get this response:

    NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
    ...
    kong-admin         NodePort    10.101.71.20     <none>        8001:30916/TCP   46m
    ...

What you want to take is the CLUSTER-IP and the first part of the PORT(S)

    10.97.55.180:8001

You will add it in the args list around line 34:

    args: ["start", "--kong-url", "http://10.101.71.20:8001"]

Then create the kong-dashboard:

    kubectl create -f kong_dashboard.yml

To check if your dashboard runs correctly check the logs. 

First get the full pod name for kong-dashboard:

    kubectl get pods 

It will be something like **kong-dashboard-86dfddcfdf-qgnhl**

Then check the logs:

    kubectl logs [pod-name]

eg

    kubectl logs kong-dashboard-86dfddcfdf-qgnhl

You should see

    Connecting to Kong on http://10.101.71.20:8001 ...
    Connected to Kong on http://10.101.71.20:8001.
    Kong version is 0.13.1
    Starting Kong Dashboard on port 8080
    Kong Dashboard has started on port 8080

If you only see

    Connecting to Kong on http://10.101.71.20:8001

It might still be starting up or your internal kong-admin url could be incorrect. Remember the url is the kubernetes internal url. 

### Test the dashboard works

You should be able to access your kong-dashboard using the url: 

# Note

This is for local testing with NodePort, not for production or cloud use. 

