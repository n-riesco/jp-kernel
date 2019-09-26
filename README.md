# jp-kernel

`jp-kernel` is an [npm module](https://www.npmjs.com/) for implementing a
[Jupyter kernel](http://jupyter.readthedocs.io/en/latest/projects/kernels.html)
that interacts with a [Node.js session](https://github.com/n-riesco/nel).

`jp-kernel` is a spin-off library from
[IJavascript](https://github.com/n-riesco/ijavascript), a Javascript kernel for
the [Jupyter notebook](http://jupyter.org/). It is currently used to implement
the following kernels:

- [IJavascript](http://github.com/n-riesco/ijavascript)

- [ITypescript](https://www.npmjs.com/package/itypescript)

- [jp-babel](http://github.com/n-riesco/jp-babel)

- [jp-coffeescript](http://github.com/n-riesco/jp-coffeescript)


## Anouncements

- Version 2.0.0 require Node.js v6 or above so that we can use `jmp@2`.

- Version 1.3.0 (unpublished) use `jmp@2` if compatible with the version of Node.js.

- Version 1.2.0 implements message `clear_output`.

- Version 1.1.0 ensures metadata is defined in `display_data` messages.

- Version 1.0.0 (stable API) handles flag `--hide-execution-result`.

- Version 0.1.5 handles `input_request` and `input_reply` messages.

- Version 0.1.4 handles `display_data` and `update_display_data` messages.

- Version 0.1.0 depends on `jmp@0.7.2`, and `jmp@0.7.2` depends on `zeromq`
  (which provides prebuilt bindings to the ZMQ library and is now maintained by
  the zeromq organisation).

- Version 0.0.1 is the initial release based on IJavascript v5.0.13.


## Install

The latest stable release is published on
[`npm`](https://www.npmjs.com/package/jp-kernel) and can be installed by
running:

```sh
npm install jp-kernel
```

The master branch in the [github
repository](https://github.com/n-riesco/jp-kernel) provides the latest
development version and can be installed by:

```sh
git clone https://github.com/n-riesco/jp-kernel.git
cd jp-kernel
npm install
```


## Usage

The source code documentation generated using [JSDoc](http://usejsdoc.org/) can
be found [here](http://n-riesco.github.io/jp-kernel/).

For real examples of usage, see the source documentation for:

- [IJavascript](http://n-riesco.github.io/ijavascript/jsdoc/index.html)

- [jp-babel](http://n-riesco.github.io/jp-babel/)

- [jp-coffeescript](http://n-riesco.github.io/jp-coffeescript/)


# Contributions

First of all, thank you for taking the time to contribute. Please, read
[CONTRIBUTING.md](CONTRIBUTING.md) and use the [issue
tracker](https://github.com/n-riesco/jp-kernel/issues) for any contributions:
support requests, bug reports, enhancement requests, pull requests, ...

# Next API v2

The `jp-kernel` API in the initial release v0.0.1 came into existence by a
process of evolution. I want to take the opportunity that distributing
`jp-kernel` as a package offers to design a new API. Below is a preview of what
I have in mind:

```js
class Kernel {
    constructor(config) {
        this.config = config;

        this.executionCount = 0;

        this.session = new Session({
            cwd: this.config.cwd,
            parser: this.config.parser,
            transpile: this.config.transpile,
        });

        this._init();
    }

    _init(initCB) {}
    interrupt(interruptCB) {}
    destroy(destroyCB) {}
    restart(restartCB) {}

    _onShellMessage(message) {}
    _onControlMessage(message) {}
    _onIOPubMessage(message) {}
    _onHBMessage(message) {}
}
```

```js
class KernelV4 extends Kernel {
    onStdout(data) {}
    onStderr(data) {}
    onShell_xxx(request) {}
}
```

```js
class KernelV5 extends Kernel {
    onStdout(data) {}
    onStderr(data) {}
    onShell_xxx(request) {}
}
```

```js
class Config {
    constructor(kernelConfig) {
        this.hideUndefined = kernelConfig.hideUndefined;
        this.initSession = kernelConfig.initSession;
        this.initScripts = kernelConfig.initScripts;
        this.kernelInfoReply = kernelConfig.kernelInfoReply;
        this.protocolVersion = kernelConfig.protocolVersion;

        this.connection = kernelConfig.connection;
        this.parser = kernelConfig.parser;
    }
}
```

```js
class Connection {
    constructor(connectionConfig) {
        this.config = connectionConfig;
        this.socket = {
            control: new jmp.Socket("router", scheme, key),
            shell: new jmp.Socket("router", scheme, key),
            iopub: new jmp.Socket("pub", scheme, key),
            hb: zmq.createSocket("rep"),
        };
    }

    connect(listeners) {}
    disconnect() {}
}
```

```js
class Parser{
    constructor() {
        throw new Error("Cannot construct an abstract class");
    }

    getIdentifier(code, position) {
        throw new Error("Not implemented");
    }

    validate(code) {
        throw new Error("Not implemented");
    }
}
```
