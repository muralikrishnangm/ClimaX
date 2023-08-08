# Installation Guide


```bash title="clone the repo"
git clone https://github.com/microsoft/ClimaX
```

=== "`conda`"

    ```bash title="Step 1: create and activate env"
    cd ClimaX
    conda env create --file docker/environment.yml
    conda activate climaX
    ```

    ```bash title="Step 1: OLCF Frontier"
    # first activate your base miniconda env
    source $PROJWORK/stf006/muraligm/software/miniconda3-frontier/bin/activate
    # follow rest of the steps but use the environment file for Frontier
    cd ClimaX
    conda env create --file docker/environment_frontier.yml
    conda activate climaX
    ```

    ```bash title="Step 2: install this package"
    # install so the project is in PYTHONPATH
    pip install -e .
    ```

    ```bash title="Note: editing the environment file for OLCF Frontier"
    # The idea is to install rocm version of torch and others on a temp env and create a temp environment file for that env.
    # Then, copy the versions from this temp environment file to the ClimaX environment file
    source $PROJWORK/stf006/muraligm/software/miniconda3-frontier/bin/activate
    conda create --prefix=env-temp python=3.8
    conda activate $PROJWORK/stf006/proj-shared/muraligm/ML/ClimaX/env-temp
    pip3 install torch torchvision torchaudio torchdata --index-url https://download.pytorch.org/whl/rocm5.4.2
    pip install -U rich
    conda env export | grep -v "^prefix: " > environment_temp.yml
    # edit the `docker/environment.yml` file with the new rocm versions giving `docker/environment_frontier.yml`
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
