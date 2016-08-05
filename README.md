# TODOs for running on a different OSE instance
* change registry IP
* change wildcard domain
* change token in Jenkins jobs

# create project dev
```
oc new-project dev
```

# create project int
```
oc new-project int
```

# create project prod
```
oc new-project prod
```

# give dev and prod image puller permissions
```
oc policy add-role-to-group system:image-puller system:serviceaccounts:int -n dev
```
```
oc policy add-role-to-group system:image-puller system:serviceaccounts:prod -n int
```
```
oc policy add-role-to-group system:image-puller system:serviceaccounts:prod -n dev
```

# create Template

## using WebUI
### create templates
```
oc create -f template.json -n dev
```
```
oc create -f acceptance.template.json -n int
```
```
oc create -f production.template.json -n prod
```
### add template to project
### create deployments

## using CLI in one go
```
oc new-app -f template.json -n dev
```

# tag for production deployment
```
oc tag v1:latest v1:1.0
oc tag v1:1.0 production:v1
oc tag production:v1 production:latest
```

# simulate crash
```
docker kill <container-id>
```

# clone v2 repo
```
git clone https://github.com/j1cken/nodejs-ex
git co v2
```

# curl loop to v2
```
for i in {1..10000};do curl v2-ab.apps.lab; sleep 0.5;done
```

# rsync changes into running container

## show terminal access in WebUI to find rsync directory

## show from CLI
```
oc get pods
oc rsh v2-1-4bnic
oc rsync --exclude='.git' . v2-1-4bnic:/opt/app-root/src
```

# add Jenkins using Web UI

## create release build job
```
curl -k -u admin:password -XPOST -d @jenkins-jobs/rel-v2.xml 'https://jenkins-dev.apps.lab/createItem?name=rel-v2' -H "Content-Type: application/xml"
```

## trigger build for v2:2.0 thru Jenkins Web UI

# Deploy Acceptance

## Create DC for Acceptance
```
oc create -f acceptance.template.json
```

## Tag Release Build Image for Acceptance

### create jenkins job
```
curl -k -u admin:password -XPOST -d @jenkins-jobs/promote-int.xml 'https://jenkins-dev.apps.lab/createItem?name=promote-int' -H "Content-Type: application/xml"
```

### trigger build of deploy-acc job

### show reference of tagged image: acc --> v2-1.0

## Watch deploy to Acceptance

# Promote to Production
```
curl -k -u admin:password -XPOST -d @jenkins-jobs/promote-prod.xml 'https://jenkins-dev.apps.lab/createItem?name=promote-prod' -H "Content-Type: application/xml"
```

## Rolling Update
run promote-prod job

## Rollback
```
curl -k -u admin:password -XPOST -d @jenkins-jobs/rollback-prod.xml 'https://jenkins-dev.apps.lab/createItem?name=rollback-prod' -H "Content-Type: application/xml"
```
