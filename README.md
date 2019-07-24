# tekton-listener-demo

## Prerequisites
- Knative eventing and serving. Can be installed via operatorhub.
- Tektoncd: [Install](https://github.com/tektoncd/pipeline/blob/master/docs/install.md#installing-tekton-pipelines-on-openshiftminishift)
- KO: [Install](https://github.com/google/ko#installation)


## Setup Docker Repo
Check that `docker login` can be done successfully.
Setup the `KO_DOCKER_REPO` variable. 

`
export KO_DOCKER_REPO=docker.io/kbaig
`

## Install Listener

```
mkdir -p ${GOPATH}/src/github.com/tektoncd
cd ${GOPATH}/src/github.com/tektoncd
git clone git@github.com:tektoncd/experimental.git
cd tekton-listener
ko apply -f config
```

## Install Github Eventing Source

```
mkdir -p ${GOPATH}/src/github.com/knative
cd ${GOPATH}/src/github.com/knative
git clone git@github.com:khrm/eventing-contrib.git
git checkout Openshift
cd eventing-contrib
ko apply -f contrib/github/config
```

## Change the repositories
Change the ownerAndRepository field in file `manifests/githubsource.yaml`. 
Also, change the image and git repo in resourceTemplate of  `manifests/eventbinding.yaml`.

## Create the namespace

```
oc new-project demo-tekton-listener
```

## Create pipeline
Add secret and user name for image repo.

```
oc apply -f manifests/pipeline.yaml
```

## Create Github Secret

Add a token in the secret with `repo:public_repo` and `admin:repo_hook` permissions. They are required to create webhook and fire webhooks.
Refer to [Creating Personal Access Token](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line#creating-a-token)

```
oc apply -f manifests/githubsecret.yaml
```

## Create Service Account

```
oc apply -f manifests/accesstoken.yaml
```

## Create Eventbinding and Listener

```
oc apply -f manifests/eventbinding.yaml
```

## Create Service for Listener

```
oc apply -f manifests/service.yaml
```

## Create Github Source

```
oc apply -f manifests/githubsource.yaml
```

# Give Privileged access to sevice account

```
oc adm policy add-scc-to-user privileged  -z demo-tekton-listener-account -n demo-tekton-listener
```



Now you can push commit to your repo and pipeline would be trigered.
`
