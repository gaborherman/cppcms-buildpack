# cppcms-buildpack

A buildpack for deploying [CppCMS](http://cppcms.com/) applications.


## conventions

Your application must have a config file in its root directory named
`cppcms.js`. This file is where the buildpack will inject environment
configuration, such as the listen port.

You must provide a `Makefile` whose default target will compile the
cppcms executable referenced by your start command.

Finally, the default start command will be `make run`, meaning that
the buildpack expects there to be a `run` target. If you prefer, you
may provide a custom start command, either through
[an application manifest](https://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html#start-commands)
or on the
[`cf push` commandline](http://docs.cloudfoundry.org/cf-cli/getting-started.html#push).


## `Makefile`

The CppCMS headers and libraries will be available at the `cppcms/`
subdirectory, so a working command might look like:

```make
hello:
	c++ hello.cpp -o hello -Lcppcms/lib -Icppcms/include -lcppcms -lbooster -lz
```


## example `Makefile`

```make
default: hello

run:
	./hello -c cppcms.js

hello: hello.cpp
	c++ hello.cpp -o hello -Lcppcms/lib -Icppcms/include -lcppcms -lbooster -lz

clean:
	rm -f hello
```


## example app

There's a "hello world" app in `test/fixtures/hello-world` that you can deploy by using the command:

```
cf push appname -b https://github.com/cloudfoundry-community/cppcms-buildpack
```


## the libraries

The cppcms libraries were compiled by
[cloudfoundry/binary-builder](https://github.com/cloudfoundry/binary-builder),
specifically the code at
[this experimental branch](https://github.com/cloudfoundry/binary-builder/tree/flavorjones-cppcms).


## TODO

### static versus shared linking

currently the cppcms tarball contains both shared and static libraries. it shouldn't.

we may also want to figure out how to do static linking, but
[this page](http://cppcms.com/wikipp/en/page/ref_embedded#Static.Linking)
indicates it may not actually be easy or desirable.


### should config.js get handled in the default start command?

currently we're doing it in `.profile.d/cppcms.sh`
