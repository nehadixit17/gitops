{{ source_repo() }}
===============================================================================

The {{ source_repo() }} (usually [ibm-mas/gitops](https://github.com/ibm-mas/gitops)) provides Helm Charts that define all of the Kubernetes resources required to deploy MAS instances using ArgoCD. The Helm Charts are split across three sub directories, depending on their intended target:

- **{{ gitops_repo_dir_link("root-applications") }}**: these charts define ArgoCD Application and ApplicationSet templates following the [App of Apps pattern](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/#app-of-apps-pattern) and target the {{ management_cluster() }} where ArgoCD is running. The Applications and ApplicationSets render other charts from {{ gitops_repo_dir_link("root-applications") }}, {{ gitops_repo_dir_link("cluster-applications") }} or {{ gitops_repo_dir_link("instance-applications") }}
- **{{ gitops_repo_dir_link("cluster-applications") }}**: these charts define Kubernetes resources for installing cluster-wide MAS pre-requisites on {{ target_clusters() }} where MAS is to be installed and managed.
- **{{ gitops_repo_dir_link("instance-applications") }}**: these charts define Kubernetes resources for installing and managing MAS instances on {{ target_clusters() }}.


The following figure shows a tree of ArgoCD applications and Application Sets generated by the charts under **{{ gitops_repo_dir_link("root-applications") }}**, starting with the **Account Root Application** at the top.

![Application Structure](drawio/appstructure.drawio)


## {{ account_root_chart() }} 

**Account Root Applications** render the {{ account_root_chart() }}, which installs the {{ cluster_root_app_set() }}. This generates a set of **Cluster Root Applications** based on the configuration in the {{ config_repo() }} 


## {{ cluster_root_chart() }} 

**Cluster Root Applications** render the {{ cluster_root_chart() }}, which contains templates to generate ArgoCD Applications for configuring various dependencies and supporting services shared by MAS instances on {{ target_clusters() }}.

| Application | Template | Helm Chart |
|-|-|-|
|Job Cleaner | {{ gitops_repo_file_link("root-applications/ibm-mas-cluster-root/templates/000-job-cleaner.yaml", "000-job-cleaner.yaml") }} | {{ gitops_repo_dir_link("cluster-applications/000-job-cleaner", "000-job-cleaner") }} |
|IBM Operator Catalog | {{ gitops_repo_file_link("root-applications/ibm-mas-cluster-root/templates/000-ibm-operator-catalog-app.yaml", "000-ibm-operator-catalog-app.yaml") }} | {{ gitops_repo_dir_link("cluster-applications/000-ibm-operator-catalog", "000-ibm-operator-catalog") }} |
|Redhat Certificate Manager | {{ gitops_repo_file_link("root-applications/ibm-mas-cluster-root/templates/010-ibm-redhat-cert-manager-app.yaml", "010-ibm-redhat-cert-manager-app.yaml") }} | {{ gitops_repo_dir_link("cluster-applications/010-redhat-cert-manager", "010-redhat-cert-manager") }} |
|Data Reporter Operator (DRO) | {{ gitops_repo_file_link("root-applications/ibm-mas-cluster-root/templates/020-ibm-dro-app.yaml", "020-ibm-dro-app.yaml") }} | {{ gitops_repo_dir_link("cluster-applications/020-ibm-dro", "020-ibm-dro") }} |
|CIS Compliance | {{ gitops_repo_file_link("root-applications/ibm-mas-cluster-root/templates/040-cis-compliance-app.yaml", "040-cis-compliance-app.yaml") }} | {{ gitops_repo_dir_link("cluster-applications/040-cis-compliance", "040-cis-compliance") }} |
|NFD Operator | {{ gitops_repo_file_link("root-applications/ibm-mas-cluster-root/templates/050-nfd-operator-app.yaml", "050-nfd-operator-app.yaml") }} | {{ gitops_repo_dir_link("cluster-applications/050-nfd-operator", "050-nfd-operator") }} |
|Nvidia GPU Operator | {{ gitops_repo_file_link("root-applications/ibm-mas-cluster-root/templates/051-nvidia-gpu-operator-app.yaml", "051-nvidia-gpu-operator-app.yaml") }} | {{ gitops_repo_dir_link("cluster-applications/051-nvidia-gpu-operator", "051-nvidia-gpu-operator") }} |
|Custom Cluster Service Accounts | {{ gitops_repo_file_link("root-applications/ibm-mas-cluster-root/templates/060-custom-sa.yaml", "060-custom-sa.yaml") }} | {{ gitops_repo_dir_link("cluster-applications/060-custom-sa", "060-custom-sa") }} |
|MAS Provisioner Service | {{ gitops_repo_file_link("root-applications/ibm-mas-cluster-root/templates/300-mas-provisioner-app.yaml", "300-mas-provisioner-app.yaml") }} | {{ gitops_repo_dir_link("cluster-applications/300-mas-provisioner", "300-mas-provisioner") }} |

The {{ cluster_root_chart() }} also installs the {{ instance_root_app_set() }}. This generates a set of **Instance Root Applications** based on the configuration in the {{  config_repo() }}.  

## {{ instance_root_chart() }} 

**Instance Root Applications**  render the {{ instance_root_chart() }}, which contains templates to generate ArgoCD Applications that installs MAS on {{ target_clusters() }}, including:

##### Instance-level MAS dependencies:

| Application | Template | Helm Chart |
|-|-|-|
|Db2u Operator | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/110-ibm-db2u-app.yaml", "110-ibm-db2u-app.yaml") }} | {{ gitops_repo_dir_link("instance-applications/110-ibm-db2u", "110-ibm-db2u") }} |
|Cloudpak 4 Data (CP4D) | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/110-ibm-cp4d-app.yaml", "110-ibm-cp4d-app.yaml") }} | {{ gitops_repo_dir_link("instance-applications/110-ibm-cp4d", "110-ibm-cp4d") }} |
|Cloudpak 4 Data (CP4D) Operators | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/110-ibm-cp4d-operator-app.yaml", "110-ibm-cp4d-operator-app.yaml") }} | {{ gitops_repo_dir_link("instance-applications/110-ibm-cp4d-operators", "110-ibm-cp4d-operators") }} |
|Cloudpak 4 Data (CP4D) CS Control| {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/110-ibm-cs-control-app.yaml", "110-ibm-cs-control-app.yaml") }} | {{ gitops_repo_dir_link("instance-applications/110-ibm-cs-control", "110-ibm-cs-control") }} |
|Suite License Service (SLS) | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/100-ibm-sls-app.yaml", "100-ibm-sls-app.yaml") }} | {{ gitops_repo_dir_link("instance-applications/100-ibm-sls", "100-ibm-sls") }}
|DB2 Databases | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/120-db2-databases-app.yaml", "120-db2-databases-app.yaml") }}[^1] | {{ gitops_repo_dir_link("instance-applications/120-ibm-db2u-database", "120-ibm-db2u-database") }} |
|Watson Studio Local (WSL) | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/120-ibm-wsl-app.yaml", "120-ibm-wsl-app.yaml") }} | {{ gitops_repo_dir_link("instance-applications/120-ibm-wsl", "120-ibm-wsl") }} |
|Watson Machine Learning (WML) | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/120-ibm-wml-app.yaml", "120-ibm-wml-app.yaml") }} | {{ gitops_repo_dir_link("instance-applications/120-ibm-wml", "120-ibm-wml") }} |
|Analytics Engine powered by Apache Spark (Spark) | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/120-ibm-spark-app.yaml", "120-ibm-spark-app.yaml") }} | {{ gitops_repo_dir_link("instance-applications/120-ibm-spark", "120-ibm-spark") }} |
|SPSS Modeler | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/120-ibm-spss-app.yaml", "120-ibm-spss-app.yaml") }} | {{ gitops_repo_dir_link("instance-applications/120-ibm-spss", "120-ibm-spss") }} |

##### MAS Core Platform

This includes the suite, suite configurations and core workspaces

| Application | Template | Helm Chart |
|-|-|-|
|MAS Core Suite | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/130-ibm-mas-suite-app.yaml", "130-ibm-mas-suite-app.yaml") }} | {{ gitops_repo_dir_link("instance-applications/130-ibm-mas-suite", "130-ibm-mas-suite") }} |
|MAS Workspaces | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/200-ibm-mas-workspaces.yaml", "200-ibm-mas-workspaces.yaml") }}[^1] | {{ gitops_repo_dir_link("instance-applications/220-ibm-mas-workspace", "220-ibm-mas-workspace") }} |
|Suite Configs  | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/130-ibm-mas-suite-configs-app.yaml", "130-ibm-mas-suite-configs-app.yaml") }}[^1] | Multiple charts [^2] |


#### MAS Applications

These share a {{ gitops_repo_dir_link("instance-applications/500-540-ibm-mas-suite-app-install", "generic chart") }}.

| Application | Template |
|-|-|
|Assist | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/510-ibm-mas-masapp-assist-install.yaml", "510-ibm-mas-masapp-assist-install.yaml") }} |
|IoT    | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/510-ibm-mas-masapp-iot-install.yaml", "510-ibm-mas-masapp-iot-install.yaml") }} |
|Manage | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/500-ibm-mas-masapp-manage-install.yaml", "500-ibm-mas-masapp-manage-install.yaml") }} |
|VisualInspection | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/510-ibm-mas-masapp-visualinspection-install.yaml", "510-ibm-mas-masapp-visualinspection-install.yaml") }} |
|Health | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/520-ibm-mas-masapp-health-install.yaml", "520-ibm-mas-masapp-health-install.yaml") }} |
|Monitor | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/520-ibm-mas-masapp-monitor-install.yaml", "520-ibm-mas-masapp-monitor-install.yaml") }} |
|Optimizer | {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/520-ibm-mas-masapp-optimizer-install.yaml", "520-ibm-mas-masapp-optimizer-install.yaml") }} |
|Predict |  {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/540-ibm-mas-masapp-predict-install.yaml", "540-ibm-mas-masapp-predict-install.yaml") }} |
|Facilities |  {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/540-ibm-mas-masapp-facilities-install.yaml", "540-ibm-mas-masapp-facilities-install.yaml") }} |

#### MAS Application Workspace Config

The generic {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/510-550-ibm-mas-masapp-configs.yaml", "510-550-ibm-mas-masapp-configs.yaml") }}[^1] template and {{ gitops_repo_dir_link("instance-applications/510-550-ibm-mas-suite-app-config", "510-550-ibm-mas-suite-app-config") }} chart is used for creating MAS Application workspaces.



[^1]: These templates are capable of generating multiple Applications; necessary because there may be one or more instances of the **type** of resource they are responsible for managing.
[^2]:
    The {{ gitops_repo_file_link("root-applications/ibm-mas-instance-root/templates/130-ibm-mas-suite-configs-app.yaml", "Suite Configs") }} Application is responsible for installing various types of suite configuration types (Mongo, BAS, SMTP, etc) at various scopes (`system`, `app`, `ws`, `wsapp`). The chart is chosen dynamically based on the configuration type: {{ gitops_repo_dir_link("instance-applications/130-ibm-jdbc-config", "JDBC") }}, {{ gitops_repo_dir_link("instance-applications/130-ibm-kafka-config", "Kafka") }}, {{ gitops_repo_dir_link("instance-applications/130-ibm-mas-bas-config", "BAS") }}, {{ gitops_repo_dir_link("instance-applications/130-ibm-mas-idp-config", "IDP") }}, {{ gitops_repo_dir_link("instance-applications/130-ibm-mas-mongo-config", "Mongo") }}, {{ gitops_repo_dir_link("instance-applications/130-ibm-mas-sls-config", "SLS") }}, {{ gitops_repo_dir_link("instance-applications/130-ibm-mas-smtp-config", "SMTP") }}, {{ gitops_repo_dir_link("instance-applications/130-ibm-objectstorage-config", "COS") }}

    
