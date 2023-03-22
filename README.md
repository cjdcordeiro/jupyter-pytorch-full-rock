# jupyter-pytorch-full

> ⚠️ WARNING ⚠️ This is very much alpha software! This has yet to be tested in a real deployment
> and serves as a proof-of-concept

This ROCK is a recreation of the `kubeflownotebookswg/jupyter-pytorch-full` image using
[rockcraft](https://github.com/canonical/rockcraft).

This image is used as part of the Charmed Kubeflow product. It is deployed in a user namespace, and
enables users to perform CPU-accelerated tasks using PyTorch from within a Jupyter Notebook.

Both PyTorch and Jupyter are installed in Conda environment, which is automatically activated.

## Build reference

Using the [Makefile](https://github.com/kubeflow/kubeflow/blob/68bf3da20c95f38051b1b60c787e11705cc5d645/components/example-notebook-servers/jupyter-pytorch-full/Makefile) for the original image, I was able to
recreate the image in a few stages. The upstream image is built from the following layers:

- [base](https://github.com/kubeflow/kubeflow/blob/master/components/example-notebook-servers/base/Dockerfile)
- [jupyter](https://github.com/kubeflow/kubeflow/blob/master/components/example-notebook-servers/jupyter/Dockerfile)
- [jupyter-pytorch](https://github.com/kubeflow/kubeflow/blob/master/components/example-notebook-servers/jupyter-pytorch/cpu.Dockerfile)
- [jupyter-pytorch-full](https://github.com/kubeflow/kubeflow/blob/master/components/example-notebook-servers/jupyter-pytorch-full/cpu.Dockerfile)

In this version, the last three layers are combined into a single `part` in [`rockcraft.yaml`](./rockcraft.yaml) named `conda-jupyter`.

## Usage

```bash
# Install some deps
$ sudo snap install rockcraft --classic --edge
$ sudo snap install skopeo --edge --devmode
$ sudo snap install yq

# Pack the ROCK (optionally add --verbose)
$ rockcraft pack
$ version=$(yq -r '.version' rockcraft.yaml)

# For the following commands, you must either use `sudo` or ensure your user is a member of the
# `docker` group, or the commands will fail.

# Import the ROCK into the local docker image cache
$ sudo skopeo --insecure-policy copy \
    oci-archive:"jupyter-pytorch-full_${version}_amd64.rock" \
    docker-daemon:"canonical/jupyter-pytorch-full:${version}"

# Run the image, invoking `jupyter` through pebble
$ sudo docker run --rm "canonical/jupyter-pytorch-full:${version}"
```

## TODO

- [ ] Test with a real Charmed Kubeflow
- [x] Upgrade to Ubuntu 22.04
