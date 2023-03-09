
port 7860

Using https://hub.docker.com/r/goolashe/automatic1111-sd-webui,
which is modeled after https://github.com/AbdBarho/stable-diffusion-webui-docker/blob/master/docker-compose.yml.

Using a ZFS subvol for the `/data` directory because it's supposed to be big. I put it on flash.
Just using a normal Docker volume for `/output` directory.