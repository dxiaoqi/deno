# deno

[![Build Status](https://travis-ci.com/ry/deno.svg?branch=master)](https://travis-ci.com/ry/deno)

A secure TypeScript runtime on V8

* 支持TypeScript 2.8开箱即用，使用V8 6.8.275.3，可以使用更为时髦的JS

* 没有npm跟package.json，不特意与node兼容

* 只需要导入参考代码的url.
	```
  import { test } from "https://unpkg.com/deno_testing@0.0.5/testing.ts"
  import { log } from "./util.ts"
	```
  远程代码在第一次执行时被提取并缓存，如果不执行 `--reload` 代码将不会被更新 (S这告诉我们，我们可以在飞机上玩代码. 有关缓存的都在这里 `~/.deno/src`.)

* 可以控制文件系统和网络访问权限以运行沙盒代码。默认为只读文件系统访问权限，不允许网络访问。V8（非特权）和Golang（特权）之间的访问只能通过此protobuf中定义的序列化消息完成
  https://github.com/ry/deno/blob/master/msg.proto 这使审计变得容易。可以使用写入访问.`--allow-write` 和 `--allow-net` 来网络访问

* 单个可执行文件:
	```
	> ls -lh deno
	-rwxrwxr-x 1 ryan ryan 55M May 28 23:46 deno
	> ldd deno
		linux-vdso.so.1 =>  (0x00007ffc6797a000)
		libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f104fa47000)
		libstdc++.so.6 => /usr/lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007f104f6c5000)
		libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f104f3bc000)
		libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007f104f1a6000)
		libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f104eddc000)
		/lib64/ld-linux-x86-64.so.2 (0x00007f104fc64000)
	```

* 可以被未捕获的错误而退出.

* 支持顶级等待.

* 为了更好兼容浏览器.

* Can be used as a library to easily build your own JavaScript runtime.
	https://github.com/ry/deno/blob/master/cmd/main.go


## Status

Segfaulty.

还没有文档. 只有一些公共的API:
https://github.com/ry/deno/blob/master/deno.d.ts

有几个例子:
https://github.com/ry/deno/blob/master/testdata/004_set_timeout.ts

这是一个可以引导你的地图:
https://github.com/ry/deno/blob/master/TODO.txt


## Compile instructions

我会在某个时候发布二进制文件，但现在你必须自己构建它，不要想着偷懒.

你需要 Protobuf 3.在 Linux上去干活 :
```
cd ~
wget https://github.com/google/protobuf/releases/download/v3.1.0/protoc-3.1.0-linux-x86_64.zip
unzip protoc-3.1.0-linux-x86_64.zip
export PATH=$HOME/bin:$PATH
```

之后你会需要 `protoc-gen-go` 和 `go-bindata`:
```
go get -u github.com/golang/protobuf/protoc-gen-go
go get -u github.com/jteeuwen/go-bindata/...
```

你需要获得并建立 `v8worker2`.它大概.....需要大约30分钟的时间来构建:
```
go get -u github.com/ry/v8worker2
cd $GOPATH/src/github.com/ry/v8worker2
./build.py --use_ccache
```

最终你可以得到 `deno` 和它的其他go deps.
```
go get -u github.com/ry/deno/...
```

现在你可以建立项目然后跑起来了:
```
> cd $GOPATH/src/github.com/ry/deno
> make
[redacted]
> ./deno testdata/001_hello.js
Hello World
>
```

## make commands

```bash
make deno # 构建deno可执行文件

make test # ＃运行测试.

make fmt # 格式化代码.

make clean # 清理构建.
```

