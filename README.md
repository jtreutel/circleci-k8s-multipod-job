# circleci-k8s-multipod-job
Simple demo project showing how to orchestrate testing in my on-prem Kubernetes cluster using CircleCI's [Runner on Kubernetes](https://circleci.com/docs/2.0/runner-on-kubernetes/).

Here's what's happening in this example:

### Preparation before running the pipeline

- I had an existing AWS EKS cluster
- I deployed Runners to my k8s cluster using [these instructions](https://circleci.com/docs/2.0/runner-on-kubernetes/).
- I [base64 encoded ](https://support.circleci.com/hc/en-us/articles/360003540393-How-to-insert-files-as-environment-variables-with-Base64) my kubeconfig file
- I stored the base64 encoded data in a [context](https://circleci.com/docs/2.0/contexts/) so that I could use the [CircleCI kubernetes orb](https://circleci.com/developer/orbs/orb/circleci/kubernetes) (see command [here](https://circleci.com/developer/orbs/orb/circleci/kubernetes#commands-install-kubeconfig))

### Pipeline jobs

1.  **checkout-code**
    - Checkout code from github and store it in the workspace cache
2.  **testing-deploy-pod**

    - Install necessary utilities (kubectl, dockerize, etc)
    - Deploy my application as a k8s deployment in my on-prem k8s cluster
    - Create a ClusterIP service so that other pods can access the application
    - Use dockerize to wait until the application is ready
    - Store the ClusterIP service IP + port in the workspace cache

3.  **testing-execute-test**
    - Run my "integration test"  (this is a simple example -- I'm just curling the application webpage and storing it to a file)
    - Upload the "test results" (I'm uploading the file to CircleCI UI -- you can see it [here](https://app.circleci.com/pipelines/github/jtreutel/circleci-k8s-multipod-job/25/workflows/86c067db-3c2a-41c2-938d-b828a20f874c/jobs/59/artifacts))

4.  **testing-delete-pod**
    - Install necessary utilities
    - Delete the deployment and service created in step 2