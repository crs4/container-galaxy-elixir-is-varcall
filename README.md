
# Variant calling on Galaxy-Kubernetes



This is a Galaxy init container image to show the feasibility of running Galaxy-based
variant calling pipelines on microservice infrastructures.  It was created
within the context of an Elixir Implementation Study and it is based on the
[Galaxy container for single cell RNA-Seq tertiary analysis
tools](https://github.com/ebi-gene-expression-group/container-galaxy-sc-tertiary)
by [Pablo Moreno](https://github.com/pcm32) and his colleagues at the EBI Gene
Expression Group:  we have taken much of the code and documentation from that
project.

This init container is to be used as part of a larger orchestration of containers
within Kubernetes which is built under the [galaxy-kubernetes
umbrella](https://github.com/galaxyproject/galaxy-kubernetes). This image
includes tools, workflows and predefined settings while galaxy-kubernetes
provides the runtime.
