# Dockerfile Collection for DGX-230

## Dockerfile list

To view the manual, please check [here](./manual).

To view the detailed graphic guides, click [here](./manual-session).

To view extra manuals about xUbuntu, please check [here](./manual-xubuntu).

To view our dockerfile list, please check [here](./dockerlist).

## Dockerfiles

Here is the list of each dockerfile:

1. xubuntu at the branch [**xubuntu**](https://github.com/moxx799/Docker-file)

    Run such a command to build the newest image online:

    ```Bash
    docker build -t xubuntu:1.0 https://github.com/moxx799/Docker-file.git
    ```

2. jupyterlab at the branch  [**jupyterlab**](https://github.com/moxx799/Docker-file/tree/jupyterlab)

    Run such a command to build the newest image online:

    ```Bash
    docker build -t jlab:1.0 https://github.com/moxx799/Docker-file.git#jupyterlab
    ```

## Update records

### ver 1.8 @ 12/26/2022

#### :small_airplane: Update

1. Copy from original documentation and update licence to latest version.
2. Update the tiger-vnc version.
3. Update the configs for XFCE panel.
4. Update the extension list of VSCode.
5. Update the `yjin-tool`.
6. Use `mamba` to update the `conda` to avoid the time costs and error.

#### :rocket: Add and config

1. Add `INIT_UID` and `INIT_GID` to the docker build arguments.
2. Add xubuntu to `dconf` users.
3. Add the environment variable to the `.profile`.
4. Debug of `dconf`: Add `dbus-launch`.
5. Move the package `kaleido` to the `CONDA_OPTIONAL` because of the version conflict.
6. Move the `screensaver` config to the correct path in 18.04 & 16.04.
7. Config the xubuntu 16.04 `shinit` file.
8. Config the `.profile` to the correct path.
9. Config the XDG paths and unset the `SESSION_MANAGER`.

#### :bug: Delete

1. Clear the Jupyterlab related configs.
2. Delete all the information about Jupyterlab.
3. Delete the option for installation of Atom.
4. Delete the VSCode extension: `hookyqr.beautify`.
