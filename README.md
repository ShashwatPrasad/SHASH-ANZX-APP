# SHASH-ANZX-APP


Mode.js app On Standalone VM deployment: 
------------------------------------------------------------------
1. Preq sites Node 14 isnalled ( curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash - && sudo apt install nodejs && node --version && npm install && npm install express) 
   create a new directory where all the files would live. In this directory create a package.json file that describes your app and its dependencies:
2. Then, create a main.js file that defines a web app using the Express.js framework:
3. Create a app.json file which will show the desired list output, and keep on changing it if you want the vesrion and commit to change, same will be read by nodejs code and show in output:
4. Check you application is working:
   curl http://<IP>/listapp
        http://<pubicip>/listapp

output: 

root@shahswat:~/NJS# node main.js 
Example app listening at http://:::8081
{
  "myapplication" : {
     "version" : "1.0",
     "lastcommit" : "10082021",
     "description" : "pre-interview technical test"
      }
}

------------------------------------------------------------------
GITHUB Repo
-------------
5. For SCM use the GIT Hub ( Prereq having GIT instaled on local system or associated with Visual Code )
a. git clone https://github.com/ShashwatPrasad/SHASH-ANZX-APP.git ( on local station)
b. When ever changes of code or file on local , update the remote GIT on Github , to be sync with local Repo.
   git add .
   git commit -m <Message>
   git push https://github.com/ShashwatPrasad/SHASH-ANZX-APP.git

 Git Action 
---------------------
6. create work flow file having details of action ".github/workflows/4.js.yml"

# Runs a set of commands using the runners shell --> using the Container provided by GITHUB as stand alone system for this excercise. 
      - name: Run a multi-line script
        run: |
          npm install express
          ifconfig -a
          route -n
          node main.js &
          ps -ef | grep 'node main.js' | awk '{print $2}'
          netstat -tnlp
          sleep 10s
          netstat -tnlp
          curl http://localhost:8081/listapp
           echo Please use the https://IP:8081/lisport to get the output
          sleep 1m
          for n in $(ps -ef | grep 'node main.js' | awk 'NR==1 {print $2}'); do echo "######## killing $n########"; ps -ef | grep $n && kill -9 $n && ps -ef | grep $n ; done

7. Execte the worflow and check the results:

sample output:
{
  "myapplication" : {
     "version" : "1.0",
     "lastcommit" : "10082021",
     "description" : "pre-interview technical test"
      }
}


-------------------------------
8 Dockarise your app ( Pre req Docker installed) 
-----------------------------
a. Create an empty file called Dockerfile: ( open port , cmd to execute , req package/library installation) 
b. docker build . -t shashwat/anzx-demo
c. docker images docker run -p 49155:8081 -d shashwat/anzx-demo
d. docker exec -it <container id> /bin/bash
e. curl -i localhost 49155/listapp
f. docker ps
g. docker stop <container id>

output:

root@shahswat:~/NJS/SHASH-ANZX-APP# curl -i localhost:49155/listapp
HTTP/1.1 200 OK
X-Powered-By: Express
Date: Wed, 11 Aug 2021 07:03:34 GMT
Connection: keep-alive
Keep-Alive: timeout=5
Content-Length: 142

{
  "myapplication" : {
     "version" : "1.0",
     "lastcommit" : "10082021",
     "description" : "pre-interview technical test"
      }
}
root@shahswat:~/NJS/SHASH-ANZX-APP# docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED         STATUS         PORTS                                         NAMES
7a4c4a13b781   shash/anzx-test-app   "docker-entrypoint.s…"   3 minutes ago   Up 3 minutes   0.0.0.0:49155->8081/tcp, :::49155->8081/tcp

----------------------------------------------
9. Export the image to external repo ( Azure Container Repo)  ( file size less than 25 MB wil be uploaded on Git Hub ) in this case we have 350MB of image file.

  docker save -o /root/NJS/SHASH-ANZX-APP/anzx-test-app.tar shash/anzx-test-app

10. docker tag shash/anzx-test-app shashwatprasad.azurecr.io/anzx-test-app
11. docker login shashwatprasad.azurecr.io --username ShashwatPrasad --password xxxxx
12. docker push shashwatprasad.azurecr.io/anzx-test-app


K8 Deployment: ( prereq K8 cluster should be deployed) 

az account set --subscription XXXXXX  ( to access K8 using Bash CLI)
az aks get-credentials --resource-group sh1 --name shashwatk8 ( to access K8 using Bash CLI)


az aks update -n shashwatk8 -g sh1 --attach-acr ShashwatPrasad ( attach K8 with Container repo) 

kubectl create namespace ( create namespace)
create anzx-test.yaml for deployment ( having detais of namespace,limits, ports and image) 


output:

kubectl get pods --namespace=anzx-test
NAME                             READY   STATUS    RESTARTS   AGE
anzx-test-app-6868559b9c-m8k5p   1/1     Running   0          3m7s



shashwat_prasad@Azure:~$ kubectl describe pod --namespace=anzx-test
Name:         anzx-test-app-6868559b9c-m8k5p
Namespace:    anzx-test
Priority:     0
Node:         aks-agentpool-32317397-vmss000000/10.240.0.4
Start Time:   Wed, 11 Aug 2021 14:04:35 +0000
Labels:       app=anzx-test-app
              pod-template-hash=6868559b9c
Annotations:  <none>
Status:       Running
IP:           10.244.0.9
IPs:
  IP:           10.244.0.9
Controlled By:  ReplicaSet/anzx-test-app-6868559b9c
Containers:
  anzx-test-app:
    Container ID:   containerd://901f9ea2b74d41f2c422b64955776579bf71e2eeb23c419a9e1f910d94c3373f
    Image:          shashwatprasad.azurecr.io/anzx-test-app:latest
    Image ID:       shashwatprasad.azurecr.io/anzx-test-app@sha256:260bbc46ff21f2042a4e096a4c79bfb709f7a17f8514c2921f60f2dd97cbad26
    Port:           49155/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Wed, 11 Aug 2021 14:04:37 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     250m
      memory:  256Mi
    Requests:
      cpu:     100m
      memory:  128Mi
    Environment:
      ALLOW_EMPTY_PASSWORD:  yes
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-stlnb (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-stlnb:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              kubernetes.io/os=linux
Tolerations:                 node.kubernetes.io/memory-pressure:NoSchedule op=Exists
                             node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  8m19s  default-scheduler  Successfully assigned anzx-test/anzx-test-app-6868559b9c-m8k5p to aks-agentpool-32317397-vmss000000
  Normal  Pulling    8m18s  kubelet            Pulling image "shashwatprasad.azurecr.io/anzx-test-app:latest"
  Normal  Pulled     8m17s  kubelet            Successfully pulled image "shashwatprasad.azurecr.io/anzx-test-app:latest" in 1.229014873s
  Normal  Created    8m17s  kubelet            Created container anzx-test-app
  Normal  Started    8m17s  kubelet            Started container anzx-test-app
