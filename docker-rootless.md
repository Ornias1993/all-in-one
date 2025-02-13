# Docker rootless

You can run AIO with docker rootless by following the steps below.

0. If docker is already installed, you should consider disabling it first: (`sudo systemctl disable --now docker.service docker.socket`)
1. Install docker rootless by following the official documentation: https://docs.docker.com/engine/security/rootless/#install. The easiest way is installing it **Without packages**. Further limitations, distribution specific hints, etc. are discussed on the same site. Also do not forget to enable the systemd service, which may not be enabled always by default. See https://docs.docker.com/engine/security/rootless/#usage. (`systemctl --user enable docker`)
1. Do not forget to set the mentioned environmental variables and in best case add them to your `~/.bashrc` file as shown!
1. Expose the privileged ports by following https://docs.docker.com/engine/security/rootless/#exposing-privileged-ports. (`sudo setcap cap_net_bind_service=ep $(which rootlesskit); systemctl --user restart docker`)
1. Use the official AIO startup command but use `--volume $XDG_RUNTIME_DIR/docker.sock:/var/run/docker.sock:ro` instead of `--volume /var/run/docker.sock:/var/run/docker.sock:ro` and also add `-e DOCKER_SOCKET_PATH=$XDG_RUNTIME_DIR/docker.sock` to the initial container startup (which is needed for mastercontainer updates to work correctly).
1. Now everything should work like without docker rootless. You can consider using docker-compose for this or running it behind a reverse proxy. Basically the only thing that needs to be adjusted always in the startup command or docker-compose file (after installing docker rootles) are things that are mentioned in point 3.

**Please note:** All files outside the containers get created, written to and accessed as the user that is running the docker daemon or a subuid of it. So for the built-in backup to work you need to allow this user to write to the target directory. For changing Nextcloud's datadir, you need to adjust the permissions of the chosen folders to be accessible/writeable by the userid `100032:100032` (if running `grep ^$(whoami): /etc/subuid` as the user that is running the docker daemon returns 100000 as first value). This logically also applies to the NEXTCLOUD_MOUNT option.
