https://bytexd.com/install-chrome-remote-desktop-headless/

https://www.linuxjournal.com/content/how-can-you-install-google-browser-debian

```shell
sudo apt install \
  curl \
  git \
  build-essential \
  cmake \

# for steam
sudo apt install software-properties-common
sudo apt-add-repository non-free
sudo dpkg --add-architecture i386
sudo apt install steam

# for dev
apt install libopenblas-dev
```