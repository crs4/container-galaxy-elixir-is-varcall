

# Recipe for running the pipeline

1. Deployment

    helm install \
           -f helm-configs/elixir-is-1.0.0_g18.05-minikube.yaml \
           --set
admin.email="me@me.com",admin.password="change.me",admin.api_key="abcdefg",galaxy_conf.admin_users=me@me.com \
           galaxy-helm-repo/galaxy-stable


2. Upload indexed reference

  * The post-install script takes care of creating a directory /export/references.
  * `kubectl cp` is an easy way to upload the reference:
       kubectl cp ~/data/hg19.tar.gz wobbly-porcupine-galaxy-stable-7d77f88cc5-j2t7b:/export/references/
  * You may be happier with Galaxy's more sophisticated facilities; for the sake of this experiment, I manually
    added the reference to the relevant loc files in `/export/tool-data`
  * You need to edit that either a) within the running container, or b) by creating your own derived image
    (the `tool-data` directory in this repository gets picked up).  If you edit within the container
    you must restart the galaxy services before exiting with `supervisorctl restart galaxy:*`.

# Installing tools

The tools within this setup image have been installed manually.  You can add
more tools from the tool shed.  The manual procedure is the following.

1. Add your wrapper in the `tools` directory.
2. Add a reference to the wrapper in `config/tool_conf.xml`
3. Add a `<tool>` tag in `<tools>` in `config/job_conf.xml` to  specify the preset resource
	 usage profile for your tool (`dynamic-k8s-tiny`, `dynamic-k8s-medium`, etc.)
4. Add an entry in `config/phenomenal_tools2container.yaml` to specify the container image
   to be used to execute your tool.

The tool id used in steps 3 and 4 must match the tool id specified by your
wrapper.  Again, if you need to use a that's in the tool shed and that already
has a container available in biocontainers all this is probably unnecessary.


# Using your computing resources

The current version of the Kubernetes runner doesn't set the `GALAXY_SLOTS`
environment variable appropriately.  Some wrappers rely on that to set the
the number of cores to be used by their tool.  This problem will result in your
tool running on a single core, unless the wrapper provides you want a way to
override it.  This issue has been fixed in Galaxy dev and will soon arrive
downstream to the galaxy-kubernetes images.
