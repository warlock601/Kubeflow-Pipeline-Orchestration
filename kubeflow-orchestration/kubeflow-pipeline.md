## Kubeflow Pipeline setup
First we need to have a k8s cluster setup, then in the cluster we can follow the steps provided here to install Kubeflow Pipelines:
https://www.kubeflow.org/docs/components/pipelines/operator-guides/installation/    
</br>

### Working
First developers/ML engineers/devops write a Python script. Then there is a Python package/module "kfp" which installed using pip. What it does is basically compiles the python script to yaml file and this yaml file is submitted to the k8s cluster. 
And when it is submitted to the k8s cluster, this cluster will run pods for each stage of Kubeflow pipeline. Let saqy we have 3 stages: Loading, Training, Evaluation, so pods for each of these will get triggered on the k8s cluster.
