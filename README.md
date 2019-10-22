# Something4NS2

- [ns \(simulator\) - Wikipedia](https://en.wikipedia.org/wiki/Ns_(simulator))
- [The Network Simulator - ns-2](https://www.isi.edu/nsnam/ns/)

## Download

[ns-allinone-2.35.tar.gz](http://sourceforge.net/projects/nsnam/files/allinone/ns-allinone-2.35/ns-allinone-2.35.tar.gz/download) SHA256: 2216f4e8e274f5c2437741fc6e9c9728369fabe1838c708ef974d262b941cd5d

## Build

*Only tested on Ubuntu 18.04.1.*

### Dependency

```xorg-dev``` for ns2,

```shell
sudo apt install -y xorg-dev
```

Maker and compiler,

```shell
sudo apt install -y build-essential
```

Install `g++-5` if you are using Ubuntu 17.10+,

```shell
sudo apt install -y g++-5
```

### Debug

The error,

```
In file included from linkstate/ls.cc:67:0:
linkstate/ls.h: In instantiation of ‘void LsMap<Key, T>::eraseAll() [with Key = int; T = LsIdSeq]’:
linkstate/ls.cc:396:28:   required from here
linkstate/ls.h:137:25: error: ‘erase’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]
  void eraseAll() { erase(baseMap::begin(), baseMap::end()); }
                    ~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
linkstate/ls.h:137:25: note: declarations in dependent base ‘std::map<int, LsIdSeq, std::less<int>, std::allocator<std::pair<const int, LsIdSeq> > >’ are not found by unqualified lookup
linkstate/ls.h:137:25: note: use ‘this->erase’ instead
```

```shell
vi +137 /the/path/to/ns-allinone-2.35/ns-2.35/linkstate/ls.h
```

``` diff
+  void eraseAll() { erase(baseMap::begin(), baseMap::end()); }
-  void eraseAll() { this->erase(baseMap::begin(), baseMap::end()); }
```

### "Install"

```shell
tar -xf ns-allinone-2.35.tar.gz
cd /the/path/to/ns-allinone-2.35/
export CC=gcc-5 CXX=g++-5 && ./install
```

### Environment variables

Set ```PATH```,

```ini
NS_HOME=/the/path/to/ns-allinone-2.35
PATH=$NS_HOME/bin:$NS_HOME/tcl8.5.10/unix:$NS_HOME/tk8.5.10/unix:$PATH
LD_LIBRARY_PATH=$NS_HOME/otcl-1.14:$NS_HOME/lib
TCL_LIBRARY=$NS_HOME/tcl8.5.10/library
```

### "Validate"

```shell
sudo apt install -y libperl4-corelib-perl
cd /the/path/to/ns-allinone-2.35/ns-2.35/
./validate
```
