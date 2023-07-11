# Installation Guide


```bash title="clone the repo"
git clone https://github.com/microsoft/ClimaX
```

=== "`conda`"

    ```bash title="create and activate env"
    cd ClimaX
    conda env create --file docker/environment.yml
    conda activate climaX
    ```

    ```bash title="install this package"
    # install so the project is in PYTHONPATH
    pip install -e .
    ```

    ```bash title="OLCF Frontier"
    # after the above steps, reinstall pytorch for ROCm
    pip uninstall torch
    pip uninstall torch
    # running it a couple of times until "WARNING: Skipping torch as it is not installed." is seen.
    pip uninstall torchvision
    pip uninstall torchaudio
    pip uninstall torchdata
    pip3 install torch torchvision torchaudio torchdata --index-url https://download.pytorch.org/whl/rocm5.4.2
    # install rish for RichModelSummary
    pip install -U rich
    ```

=== "`docker`"

    ```bash title="build docker container"
    cd docker
    docker build -t ClimaX .
    ```

    ```bash title="run docker container"
    cd ClimaX
    docker run --gpus all -it --rm --user $(id -u):$(id -g) \
        -v $(pwd):/code -v /mnt/data:/data --workdir /code -e PYTHONPATH=/code/src \
        ClimaX:latest
    ```


    !!! note

        - `--gpus all -it --rm --user $(id -u):$(id -g)`: enables using all GPUs and runs an interactive session with current user's UID/GUID to avoid `docker` writing files as root.
        - `-v $(pwd):/code -v /mnt/data:/data --workdir /code`: mounts current directory and data directory (i.e. the cloned git repo) to `/code` and `/data` respectively, and use the `code` directory as the current working directory.
