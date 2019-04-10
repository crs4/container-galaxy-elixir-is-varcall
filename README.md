
# Variant calling on Galaxy-Kubernetes

This project consists of Galaxy container images to show the feasibility of
running Galaxy-based variant calling pipelines on microservice infrastructures.
It was created within the context of an Elixir Implementation Study and it is
based on the [Galaxy container for single cell RNA-Seq tertiary analysis
tools](https://github.com/ebi-gene-expression-group/container-galaxy-sc-tertiary)
by [Pablo Moreno](https://github.com/pcm32) and his colleagues at the EBI Gene
Expression Group.

These images are to be used as part of a larger orchestration of containers
within Kubernetes which is built under the [galaxy-kubernetes
umbrella](https://github.com/galaxyproject/galaxy-kubernetes). They
include tools, workflows and predefined settings and patch the runtime provided
by galaxy-kubernetes to work for this use case.

## Tools

These images include a simple variant-calling pipeline composed of:

  * FastQC
  * BWA-MEM
  * Samtools sort
  * FreeBayes

These tools are preinstalled within the instance.  Once the platform is
deployed, can add more tools with the Galaxy Tool Shed.

For more advanced scenarios, you can build your own images based on these ones
using the provided Dockerfiles.

## Usage

This documentation assumes you have access to a Kubernetes cluster.  If you
don't, but you have access to cloud resources (i.e.,
Infrastructure-as-a-Service) you can use any of the standard Kubernetes
deployment tools available (e.g., [KubeNow](http://kubenow.readthedocs.io),
[Kubespray](https://github.com/kubernetes-sigs/kubespray) or its simplified
front-end (but OpenStack-only)
[manage-cluster](https://github.com/tdm-project/tdm-manage-cluster).
Alternatively, some commercial vendors also offer managed-Kubernetes services
(e.g., [Amazon EKS](https://aws.amazon.com/eks/).


From here, we assume you already have access to a Kubernetes cluster.

## Deploying the Galaxy-Kubernetes VC platform

You can deploy these images by using the [Helm Kubernetes package
manager](https://helm.sh).

For instructions on how to install helm, please consult the [helm installation
guide](https://helm.sh/docs/using_helm/#installing-helm).


We have provided some preset deployment settings under the directory
`helm-configs`.  Clone this repository to your local machine to get them:

    git clone https://github.com/crs4/container-galaxy-elixir-varcall.git

Consult the settings and adapt them to your specific situation.  You
can find the meaning of these and other possible settings in the [official
documentation for the Galaxy-Kubernetes Helm
chart](https://github.com/galaxyproject/galaxy-kubernetes#available-chart-variables).
Some settings that require particular attention:

| galaxy_conf.\* | All your usual galaxy settings usually in `galaxy.ini` or `galaxy.yaml` |
| persistence.size | Size of storage volume |

  
Once you're happy with the settings, proceed with the following steps.

Add the Galaxy Helm repository to your local settings:

    helm repo add galaxy-helm-repo https://pcm32.github.io/galaxy-helm-charts
    helm repo update

Install the Galaxy-Kubernetes chart, configuring your desired login email and
password:

    helm install \
      -f helm-configs/elixir-is-1.0.0_g18.05.yaml \
      --set admin.email=your@email.com,admin.password="superpassword",admin.api_key="set-your-api-key",galaxy_conf.admin_users=your@email.com \
      galaxy-helm-repo/galaxy-stable


The deployment procedure takes a while (about 10 minutes) to complete.  You can
monitor the progress through the usual Kubernetes tools (e.g., use `kubectl` to
monitor the status of the new pods and follow their log output).  You can
access the Galaxy instance on the Kubernetes master's port 30700 (i.e.,
`http://<master ip>:30700`).
