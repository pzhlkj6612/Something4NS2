# Something4NS2

- [ns \(simulator\) - Wikipedia](https://en.wikipedia.org/wiki/Ns_(simulator))
- [The Network Simulator - ns-2](https://www.isi.edu/nsnam/ns/)

## Download

[ns-allinone-2.35.tar.gz](http://sourceforge.net/projects/nsnam/files/allinone/ns-allinone-2.35/ns-allinone-2.35.tar.gz/download) SHA256: 2216f4e8e274f5c2437741fc6e9c9728369fabe1838c708ef974d262b941cd5d

## Build

*Only tested on Ubuntu 18.04.1.*

### Dependency

```xorg-dev``` for X11 libraries,

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

``` diff
-  void eraseAll() { erase(baseMap::begin(), baseMap::end()); }
+  void eraseAll() { this->erase(baseMap::begin(), baseMap::end()); }
```

```shell
sed -i '137s/erase(/this->erase(/' /the/path/to/ns-allinone-2.35/ns-2.35/linkstate/ls.h
```

### Add mUdp, mUdpSink and mTcpSink

http://csie.nqu.edu.tw/smallko/ns2_old/tool_en.htm

https://blog.csdn.net/zhoujunbuaa/article/details/7103561

https://blog.csdn.net/Joanna_yan/article/details/41786757

http://www.voidcn.com/article/p-rwcsrkzl-yx.html

1. Create a folder named measure under ns. (`/the/path/to/ns-allinone-2.35/ns-2.35/measure`)
2. Put these six files into measure folder.
3. Add `sendtime_`, `pkt_id_` and `sendtime()` into packet common header. (starting from the 599th line in `/the/path/to/ns-allinone-2.35/ns-2.35/common/packet.h`)

``` diff
struct hdr_cmn {
        enum dir_t { DOWN= -1, NONE= 0, UP= 1 };
        packet_t ptype_;      // packet type (see above)
        int     size_;                // simulated packet size
        int     uid_;         // unique id
        int     error_;              // error flag
        int     errbitcnt_;     // # of corrupted bits jahn
        int     fecsize_;
        double      ts_;           // timestamp: for q-delay measurement
        int     iface_;              // receiving interface (label)
        dir_t direction_;        // direction: 0=none, 1=up, -1=down
+       double  sendtime_;
+       unsigned long int pkt_id_;

        //...

        inline int& addr_type() { return (addr_type_); }
        inline int& num_forwards() { return (num_forwards_); }
        inline int& opt_num_forwards() { return (opt_num_forwards_); }
        //monarch_end
+       inline double& sendtime() { return (sendtime_); }

        //...
   }
```

```shell
sed -i '667a \\tinline double& sendtime() { return (sendtime_); }' /the/path/to/ns-allinone-2.35/ns-2.35/common/packet.h
sed -i '609a \\tunsigned long int pkt_id_;' /the/path/to/ns-allinone-2.35/ns-2.35/common/packet.h
sed -i '609a \\tdouble  sendtime_;' /the/path/to/ns-allinone-2.35/ns-2.35/common/packet.h
```

4. Fix some bug of constructor in `mudp.cc` (starting from the 16th line).

``` diff
- mUdpAgent::mUdpAgent() : id_(0), openfile(0)
+ mUdpAgent::mUdpAgent() : UdpAgent(), id_(0), openfile(0)
  {
	bind("packetSize_", &size_);
- 	UdpAgent::UdpAgent();
  }
```

```shell
sed -i '19d' /the/path/to/ns-allinone-2.35/ns-2.35/measure/mudp.cc
sed -i '16s/mUdpAgent() :/mUdpAgent() :UdpAgent(),/' /the/path/to/ns-allinone-2.35/ns-2.35/measure/mudp.cc
```

5. Add the `measure/mudp.o measure/mudpsink.o measure/mtcpsink.o \` in the `OBJ_CC` of `Makefile.in` (starting from the 164th line).

```diff
# !include <conf/makefile.win>

OBJ_CC = \
+	measure/mudp.o measure/mudpsink.o measure/mtcpsink.o \
	tools/random.o tools/rng.o tools/ranvar.o common/misc.o common/timer-handler.o \
	common/scheduler.o common/object.o common/packet.o \
	common/ip.o routing/route.o common/connector.o common/ttl.o \
```

```shell
sed -i '164a \\tmeasure\/mudp.o measure\/mudpsink.o measure\/mtcpsink.o \\' /the/path/to/ns-allinone-2.35/Makefile.in
```

6. Add `Agent/mUDP set packetSize_ 1000` in the `ns-default.tcl`. (ns-default.tcl is under `/the/path/to/ns-allinone-2.35/ns-2.35/tcl/lib`, starting from the 815th line)

```diff
  #
  # Agents
  #
+ Agent/mUDP set packetSize_ 1000
  Agent set fid_ 0
  Agent set prio_ 0
  Agent set agent_addr_ -1
```

```shell
sed -i '815a Agent/mUDP set packetSize_ 1000' /the/path/to/ns-allinone-2.35/ns-2.35/tcl/lib/ns-default.tcl
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
sudo apt install -y libperl4-corelibs-perl
cd /the/path/to/ns-allinone-2.35/ns-2.35/
./validate
```
