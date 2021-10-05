# GCP Mlops Pipeline

This Repository contains notebook,scripts and files for building MLops Pipeline in GCP Cloud.

## Deep Learning Project 💻

The project is based on a simple image cllasification on Leaf Disease Problem, it contains 3 classes of leaf disease, `['angular_leaf_spot', 'bean_rust', 'healthy']`.Our task is to predict the class of the image.

## Motivation 🛠
As with growth of Machine Learning, most of the organization are shifitng their whole ML workloads to cloud platform so that they can achive High Availability and utilize high performance,As their are a lot of cloud platforms are available to deploy our whole ML models.Some are AWS,GCP,Azure.For this Project I have selected GCP as one of the cloud platform.

I have already build the whole Deep Learning Project and Saved its artifacts in models folder.So now we can easily build a pipeline to deploy our model in GCP and automate it.


## Deployment 🚀
The whole MLops workflow in GCP looks like this:

<p align="center">
  <img src="images\workflow.png" alt="workflow"/>
</p>

In the above Pipeline I am using K8s as the orchestrator and whole pipeline is automated with GCP Cloud Build.

### Services Used:

1. Google Cloud Build
2. Google Kubernetes Engine
3. Google Container Registry

cloudbuild.yaml
```yaml
steps:
- name: 'gcr.io/cloud-builders/docker'
  args: ['build', '-t', 'gcr.io/$PROJECT_ID/imgwebapp:v1', '.']
  timeout: 180s
- name: 'gcr.io/cloud-builders/docker'
  args: ['push', 'gcr.io/$PROJECT_ID/imgwebapp:v1']
- name: 'gcr.io/cloud-builders/gcloud'
  entrypoint: "bash"
  args:
  - "-c"
  - |
    echo "Docker Container Built"
    ls -la
    ls -al models/
- name: "gcr.io/cloud-builders/gke-deploy"
  args:
  - run
  - --filename=k8s/
  - --location=us-west1-b
  - --cluster=project-kube
  - --timeout=500s
```

You can see in the above yaml the project is first pushed to gcr as a image and from gcr, gke takes the image and deploys it as a service.

This pipleine can be customized for any project and you can try by your own.

## Usage 📦  

You need to run the following command to initilize the K8s cluster and then kick start the pipeline:

**Create a K8s Cluster:**

```bash
gcloud container clusters create project-kube --zone "us-west1-b" --machine-type "n1-standard-1" --num-nodes "1"
```

**KickStart Pipeline:**

```bash
gcloud builds submit --config cloudbuild.yaml
```

## Code 🆘

The code is present in the form of a notebook and a streamlit app.But one needs to have a billing-enabled GCP account (with a few APIs enabled) to successfully run these notebooks and scripts.
