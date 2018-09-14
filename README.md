# Something4NS2

- [ns \(simulator\) - Wikipedia](https://en.wikipedia.org/wiki/Ns_(simulator))

## Download

[ns-allinone-2.35.tar.gz](http://sourceforge.net/projects/nsnam/files/allinone/ns-allinone-2.35/ns-allinone-2.35.tar.gz/download)

## Compile

Dependency,

```shell
sudo apt install -y build-essential xorg-dev
```

"Install",

```shell
tar -xf ns-allinone-2.35.tar.gz
cd /the/path/to/ns-allinone-2.35/
./install
```

Set ```PATH```,

```ini
PATH=/the/path/to/ns-allinone-2.35/bin:/the/path/to/ns-allinone-2.35/tcl8.5.10/unix:/the/path/to/ns-allinone-2.35/tk8.5.10/unix:$PATH
LD_LIBRARY_PATH=/the/path/to/ns-allinone-2.35/otcl-1.14:/the/path/to/ns-allinone-2.35/lib
TCL_LIBRARY=/the/path/to/ns-allinone-2.35/tcl8.5.10/library
```

"Validate",

```shell
sudo apt install -y libperl4-corelib-perl
cd /the/path/to/ns-allinone-2.35/ns-2.35/
./validate
```
