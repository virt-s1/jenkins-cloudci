# Cloud CI

Jenkins for Cloud CI has prod and stage deployments on PSI OpenShift 4.6. Prod jenkins has 100G persistent storage, but stage one does not have persistent storage.

## How to deploy Jenkins and Jslave

    $ oc login https://api.ocp-c1.prod.psi.redhat.com:6443 --token=<openshift token>
    $ oc project virt-qe-3rd
    $ oc create -f jslave-cloudci-prod-buildconfig-template.yaml
    $ oc create -f jenkins-cloudci-prod-c1-persistent-template.yaml

## How to update Jenkins and Jslave configuration

    $ oc login https://api.ocp-c1.prod.psi.redhat.com:6443 --token=<openshift token>
    $ oc project virt-qe-3rd
    $ oc process -f jenkins-cloudci-prod-c1-persistent-template.yaml | oc apply -f -
    $ oc process -f jslave-cloudci-prod-buildconfig-template.yaml | oc apply -f -

> *Build jslave image before jenkins image because the seed job in CasC will be running on it. All of Jenkins job will be run on jslave.*

> *Please remove all jobs to avoid duplicate job trigger if you deploy your own Jenkins for debugging*

## Add UMB certificate for redhat-ci-plugin

Add certificate and related files as OpenShift secret

    $ oc create secret generic umb-secrets --from-file /home/foo/Documents/redhat/redhat-ci-plugin-cert

## Configure Jenkins Credential

Do not configure Jenkins Credential from Jenkins, use openshift secret instead. [kubernetes-credentials-provider-plugin](https://github.com/jenkinsci/kubernetes-credentials-provider-plugin) is able to sync openshift secret with Jenkins credential.

    $ oc process -f my-secret-template.yaml | oc apply -f -

NOTE: To use `data` with [base-64](https://kubernetes.io/docs/concepts/configuration/secret/#creating-a-secret-manually) encoded string, please do not include `\n` in string

    $ echo -n "you string" | base64

> Use `\\\\n` to escape `\n`
> All Json key and value must be qouted by `""` and it has to be escapted by `\"`

### Credential Example

All secret-Jenkins credential example can be found from [here](https://github.com/jenkinsci/kubernetes-credentials-provider-plugin/tree/master/docs/examples)


## How to debug your test script

Run/Debug your test script in CI container environment. 

1. Build Jslave container locally

    $ podman build -t local/jslave:latest ./jslave

2. Run Jslave locally

    $ podman run -rm --name jslave -it -e ABC=foo -e XYZ=bar local/jslave:latest bash

## Notes:

1. To support [ScriptApproval on JCasC](https://issues.jenkins-ci.org/browse/JENKINS-57563), Script Security Plugin version 1.64 or above has to be installed.
