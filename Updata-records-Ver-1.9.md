# Dockerfile Collection for DGX-230

## XUbuntu

The overall upgrade work consists of 3 Parts, DGX-OS/ubuntu, docker file/base image update, and manual part, this file will introduce the update operations step by step.

### DGX-OS/ubuntu upgrade

The first step is to check the latest version of the dgx-os system and the ubuntu version on https://docs.nvidia.com/base-os/, and ```cat /etc/dgx-release``` respectively. Decide if the upgrade is needed, then follow the steps below to upgrade them if needed:

* Back up the files to raid under the ```/home ``` disk
* Back up the necessary user images with the command
```bash
 docker push <image name>:<tag>
 ```
 It needs to log into the nvcr.io with the correct permission. Here is the command```docker login nvcr.io```, user name `$oauthtoken`, and the password is `my-api-key`. Find/generate the key on the up-right user icon and click `set-up`. 
* Operate the following steps on the dgx-machine, without the remote desktop.
* Restart the dgx by `sodu reboot`, login to the desktop on dgx, and close all the windows and applications. 
* Find the tutorial on the upgrade documentation, such as:https://docs.nvidia.com/dgx/dgx-os-6-user-guide/upgrading.html#performing-a-release-upgrade-from-dgx-os-5. Follow the steps.

Here is the upgrade operation from DGX-OS-5 to DGX-OS-6, please execute the commands one by one:
* First upgrade the current dgx-os to the latest version, for example, the latest version of OS-5 is 5.4 and our machine is 5.2, we need to upgrade it to OS- 5.4 before we move the OS-6.
```bash
sudo apt update
sudo apt -y full-upgrade
```
* Restart the machine `sudo reboot` and go to OS-6, it should take more than one hour and there are several conflicts that need you to decide the operations, some conflicts should follow the official documentation and some can be decided by yourself, default operation is to keep the current one, but we recommend to use the new one:
```bash
# Check the network with wget, and follow the documentation.
# wget -O f1-changelogs http://changelogs.ubuntu.com/meta-release-lts
sudo apt install -y nvidia-release-upgrade
sudo apt autoremove
sudo nvidia-release-upgrade
```
* Restart the machine `sudo reboot` and check the version  ```cat /etc/dgx-release```.

### Image upgrade by dockerfile

We use the dockerfile to update the base image, the previous dockerfile is compatible with Ubuntu 16.04, 18.04, 20.04, we need to update it to 22.04.

Git the dockerfile from Github and update the scripts, the details are in the history and here is an overall guidance:

* Change the main code of the dockerfile, to update the arguments, we set "with_chinese" to false and the extension apps to "go".
* Change the vscode extension under scripts/install-desktop-exts, delete/change the extension index
* Change the `configs/detach` with the correct version in the if condition. For Ubuntu 22.04, we add "xjammy" to the specific lines of the code.
* Change the `scripts/` with the correct version in the if condition. 
* Change the pkgs name in the script and delete the out-of-date names, check it on the web https://packages.ubuntu.com/search?suite=default&section=all&arch=any&keywords=libgtk-3-dev&searchon=names.

* Check the base images on Nvidia NGC https://catalog.ngc.nvidia.com/, find the latest version of the pytorch and tensorflow respectively, and try to pull the image by :
```bash
 docker pull <image name>:<tag>
 ```
* Finally, run and debug the script, main is the repository name so you need to cd to the correct repository path,
  ```bash
  docker build -t <name:tag> --build-arg BASE_IMAGE=nvcr.io/nvidia/pytorch:23.12-py3 --build-arg BASE_LAUNCH=/opt/nvidia/nvidia_entrypoint.sh main
  ```

### Mannualy setting
Check the desktop, such as the browser, panel, and appearance.
If firefox is not installed correctly, please use the command below, and paste the echo part together:
```bash
sudo apt install -y software-properties-common
sudo add-apt-repository -y ppa:mozillateam/ppa

echo '
Package: *
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001
' | sudo tee /etc/apt/preferences.d/mozilla-firefox

echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:${distro_codename}";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-firefox

sudo apt install -y firefox --allow-downgrades
```
Mannually set the panel with right click edit panel and set the theme with gnome-tweaks in command line, Appearance (xfce) in the applications, the other themes can be found in http://www.gnome-look.org , download, extract, and put them in /home/.theme or /usr/share/themes/ and can also modify them in the `~/.config/gtk-4.0/settings.ini.`, or ` [Settings] gtk-theme-name = YourThemeName`. Commit the image.
Finally, delete the usr configs by yjin-tool 
```bash
sodu yjin-tool --rmvnc
```
which is equal for the
```bash
tigervncserver -kill :1
rm ~/.vnc/*.log || fail
rm ~/.vnc/passwd || fail
ls ~/.vnc || fail
```
Commit the image.