# tekton-listener-demo

## Change the repositories
Change the ownerAndRepository field in file `manifests/githubsource.yaml`. 
Also, change the image and git repo in resourceTemplate of  `manifests/eventbinding.yaml`.

## Create the namespace

`
oc new-project demo-tekton-listener
`

## Create pipeline
Add secret and user name for image repo.

`
oc apply -f manifests/pipeline.yaml
`

## Create Github Secret

Add a token in the secret with `repo:public_repo` and `admin:repo_hook` permissions. They are required to create webhook and fire webhooks.
Refer to [Creating Personal Access Token](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line#creating-a-token)

`
oc apply -f manifests/githubsecret.yaml
`

## Create Service Account

`
oc apply -f manifests/accesstoken.yaml
`

## Create Eventbinding and Listener

`
oc apply -f manifests/eventbinding.yaml
`

## Create Service for Listener

`
oc apply -f manifests/service.yaml
`

## Create Github Source

`
oc apply -f manifests/githubsource.yaml



Now you can push commit to your repo and pipeline would be trigered.
`
