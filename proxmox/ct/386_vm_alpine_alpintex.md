## Setup

### Install steps
- Get image x86 iso from https://alpinelinux.org/downloads/.
- Boot
- Log in with username "root" and empty password.
- Run: `setup-alpine`
- Reboot.

### Packages
Search package at https://pkgs.alpinelinux.org/packages?arch=x86.
```shell
apk update
apk add build-base cmake git
```

### Build
```shell
doas -u gendeux ash
# I am unpriviledged now.
# Go home.
cd
mkdir code
cd code
git clone https://github.com/fildesh/fildesh.git
cd fildesh
mkdir bld
cd bld
cmake .. -D CMAKE_BUILD_TYPE=Release
cmake --build .
```