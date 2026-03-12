## Machine Learning Lifecycle steps:
- Identifying & Understanding Problem Statement
- Data Collection
- Exploratory Data Analysis
- Feature Engineering & Selection
- Model Training
- Model Evaluation & Tuning
- Model Deployment
- Model Monitoring

Machine Learning is a very iterative approach. That is why most of the ML Engineers have the model on their laptop but they do not ship it to production. Main reason for that is, initially the ML model efficiency may be high but as the new data keeps coming in, the efficiency keeps on reducing, hence they have to again train the model, tune it and then deploy it.

## Kubeflow
Kubeflow is not a single tool but rather a collection of components or projects like Kubeflow Spark Operator, Kubeflow Trainer, Kubeflow Katib, KServe, Model Registry, Pipelines etc where each component aims at solving the oprations invloved in ML lifecycle.

### Important components of Kubeflow:
- Kubeflow Pipelines: This is the workflow orchestration engine of Kubeflow. It allows you to define ML workflows as reproducible DAGs, where each stage runs as a Kubernetes container. </br>
  Each step:
  - runs in isolated container
  - has input/output artifacts
  - can retry independently
  - stores metadata

  If a setp fails, Only failed step reruns — not whole pipeline. </br>
  Internally uses: Kubernetes pods, Argo-based execution model (in many Kubeflow versions), metadata tracking.

- Katib: Katib is used for automated hyperparameter tuning. It runs multiple training experiments automatically to find best parameters. </br>
  <img width="478" height="638" alt="image" src="https://github.com/user-attachments/assets/1e0512ad-5201-4315-9111-e0bd37c3f255" />
