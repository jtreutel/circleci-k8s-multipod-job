# circleci-k8s-multipod-job
Simple demo project showing how to orchestrate testing in an on-prem Kubernetes cluster using CircleCI's [Runner on Kubernetes](https://circleci.com/docs/2.0/runner-on-kubernetes/).

Here's what's happening in this example:

### Preparation before running the pipeline

- Existing AWS EKS cluster
- Existing Runners on pods in my k8s cluster (see [these instructions](https://circleci.com/docs/2.0/runner-on-kubernetes/)).
- kubeconfig file [base64 encoded](https://support.circleci.com/hc/en-us/articles/360003540393-How-to-insert-files-as-environment-variables-with-Base64) and stored in a [context](https://circleci.com/docs/2.0/contexts/) so that I could use the [CircleCI kubernetes orb](https://circleci.com/developer/orbs/orb/circleci/kubernetes#commands-install-kubeconfig).
- Pulled a sample app, Guestbook, from the [Kuberenetes sample app repo](https://github.com/kubernetes/examples).

### Pipeline jobs

1.  **checkout-code**
    - Checkout code from github and store it in the workspace cache

2.  **create-temporary-namespace**
    - Creates a temporary namespace into which k8s objects will be deployed for testing

3.  **deploy-\<k8s object name\>**
    - Deploys a kubernetes object
    - This is a matrix job, so it will create an instance of this job for each object that will be deployed
    - If the object is a service, waits until it can successfully connect to the service

4.  **execute-tests**
    - Run my "integration test" (this is a simple example -- I'm just curling the application webpage and storing it to a file)
    - Upload the test results to the CircleCI UI

5.  **delete-\<k8s object name\>**
    - Deletes a kubernetes object
    - Also a matrix job, so a job will be created for each object that needs to be deleted

2.  **delete-temporary-namespace**
    - Deletes the temporary namespace