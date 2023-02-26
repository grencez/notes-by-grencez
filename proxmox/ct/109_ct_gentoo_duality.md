# duality
Container based on Gentoo template.
Used for building stuff.

## Everything Done

```shell
#emerge app-eselect/eselect-repository
mkdir -p /etc/portage/repos.conf
printf '[grencez]\nlocation = /var/db/repos/grencez\n' >/etc/portage/repos.conf/grencez.conf
```

```shell
# We have readonly repositories in /var/db/repos/, so no need to `emerge --sync`.
# Just need to tell the system that my personal repo exists there.
mkdir -p /etc/portage/repos.conf
printf '[grencez]\nlocation = /var/db/repos/grencez\n' >/etc/portage/repos.conf/grencez.conf
# Initial update.
emerge -uDN world
emerge --depclean
```

```shell
# For eix but probably others.
echo '*/* sqlite' >> /etc/portage/package.use/00_world
emerge app-portage/eix
eix-update
```

```shell
# Vim is important to me.
emerge app-editors/vim
emerge dev-vcs/git
```

```shell
echo '*/* headless-awt' >> /etc/portage/package.use/00_world
echo 'dev-util/bazel' >> /etc/portage/package.accept_keywords/00_world
echo 'dev-util/bazel tools' > /etc/portage/package.use/bazel
#echo 'dev-java/openjdk-bin' >> /etc/portage/package.mask/00_world
emerge bazel
```


```shell
useradd --group users --create-home gendeux
emerge app-admin/doas
echo 'permit nopass nolog root' >> /etc/doas.conf
cd /home/gendeux
doas -u gendeux mkdir code
cd code
doas -u gendeux git clone https://github.com/fildesh/fildesh.git
cd fildesh
doas -u gendeux sh -c -l 'bazel build //...'
doas -u gendeux sh -c -l 'bazel run --config=asan-libfuzzer //test/fuzz/kv:ensure_fuzz_test_full -- -fork=16'
```
