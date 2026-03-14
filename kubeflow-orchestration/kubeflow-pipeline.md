## Kubeflow Pipeline setup
First we need to have a k8s cluster setup, then in the cluster we can follow the steps provided here to install Kubeflow Pipelines:
https://www.kubeflow.org/docs/components/pipelines/operator-guides/installation/    
</br>

### Working
First developers/ML engineers/devops write a Python script. Then there is a Python package/module "kfp" which installed using pip. What it does is basically compiles the python script to yaml file and this yaml file is submitted to the k8s cluster. 
And when it is submitted to the k8s cluster, this cluster will run pods for each stage of Kubeflow pipeline. Let saqy we have 3 stages: Loading, Training, Evaluation, so pods for each of these will get triggered on the k8s cluster.
1. Create a Virtual environment and activate it.
  ```bash
  python3 -m venv .kfp
  source .kfp /bin/activate
  ```
2. Install kfp(kubeflow pipeline) package
  ```bash
  pip install kfp==2.9.0                      
  ```

3. Below is the python code for Pipeline. For every component(stage) within the python script in Kubeflow, a decorater will be added like @dsl.component so that it can be reused. For this component a pod is created and this pod is executed independent of
   other components so that we can maintain scalability, maintain parallelism within the Kubeflow pipelines. </br>
   </br>
   DSL is Domain-Specific Language (language designed for one specific domain instead of general programming). Kubeflow DSL is built on top of Python, so you write normal Python syntax, but with Kubeflow decorators and components to describe ML workflow logic.
   </br>
   </br>
   @dsl.pipeline is the orchestrator. As there are different stages in ML pipeline, all of these stages have to be orchestrated just like in github-actions, k8s etc. In @dsl.pipeline, all the functions will get invoked. </br>
   </br>
   Here we're using a model and this model will focus on identifying the type of the flower. We're building a model by taking some parameters in consideration which will identify what kind of flower it is. We have 4 features for each flower: sepal-length, sepal-    width, petal-length, petal-width. Depending on these it will identify the type of flower it is. For this we will need a dataset and here we're using: Iris datatset. </br>
   </br>
   Inside the component, first we we have written a function for Loading of data, then function for training the model. Inside the component, we mention the depdendencies such as base_image, packages_to_install then the function defination (including parameters,    labels, features etc)
```bash
import kfp
from kfp import dsl
from typing import NamedTuple, List

# Step 1: Load data into memory and return as lists
@dsl.component(
    base_image="python:3.8-slim",
    packages_to_install=["pandas", "scikit-learn"]
)
def load_data() -> NamedTuple("Outputs", [("features", List[List[float]]), ("labels", List[int])]):
    from sklearn.datasets import load_iris
    iris = load_iris()
    return (iris.data.tolist(), iris.target.tolist())

# Step 2: Train model and return accuracy
@dsl.component(
    base_image="python:3.8-slim",
    packages_to_install=["scikit-learn"]
)
def train_model(
    features: List[List[float]],
    labels: List[int]
) -> NamedTuple("Output", [("accuracy", float)]):
    from sklearn.ensemble import RandomForestClassifier
    from sklearn.model_selection import train_test_split
    from sklearn.metrics import accuracy_score

    X_train, X_test, y_train, y_test = train_test_split(features, labels, test_size=0.2)

    clf = RandomForestClassifier()
    clf.fit(X_train, y_train)
    y_pred = clf.predict(X_test)
    acc = accuracy_score(y_test, y_pred)

    print(f"Model accuracy: {acc}")
    return (acc,)

# Step 3: Define the pipeline
@dsl.pipeline(
    name="iris-no-artifacts-pipeline",
    description="ML pipeline without file artifacts, returns accuracy."
)
def iris_pipeline():
    data = load_data()
    train_model(
        features=data.outputs["features"],
        labels=data.outputs["labels"]
    )

# Step 4: Compile
if __name__ == "__main__":
    kfp.compiler.Compiler().compile(
        pipeline_func=iris_pipeline,
        package_path="iris_pipeline.yaml"
    )
```

4. As soon as execute the python script, it will form a yaml manifest.
```bash
python3 pipeline.py
```
