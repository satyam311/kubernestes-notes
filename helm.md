# Helm Chart Structure
A Helm chart = a folder that defines an application.
Example folder structure:

```
myapp/
├── Chart.yaml              # Metadata about the chart
├── values.yaml             # Default configuration values
├── templates/              # Directory of YAML templates
│   ├── deployment.yaml
│   ├── service.yaml
│   └── configmap.yaml
└── charts/                 # (optional) subcharts for dependencies

```

### How to Start 

1. First install helm on the os : http://helm.sh/docs/intro/install/

2. do  ```helm --help ``` and then ```help create --help ```
3. ```helm create NAME [flags] ``` -- this command create sample chart with default file we get.
4. Modify values.yaml

   


