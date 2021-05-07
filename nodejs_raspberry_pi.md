Source ----> https://gist.github.com/stonehippo/f4ef8446226101e8bed3e07a58ea512a#file-nodejs_raspberry_pi-md


# Setting Up Node.js On Raspberry Pi

There are several ways that you can set up Node.js on a Raspberry Pi when running Raspbian. Depending on your needs, the version of the RPi that you're using, and how you like to manage installs, you have a lot of options.

## Node.js from source
Do not do this if you can avoid it, it's _super slow_. If you insist on and have the time, you can start at https://nodejs.org. But really, *don't do this*.

## Install with `apt`
This method is pretty easy: 

```sh
$ sudo apt-get update
$ sudo apt-get install nodejs
```

This works and is a reasonably quick install, but the version of Node.js is likely to be pretty old, since the official repo doesn't get updated often.

Fortunately, there's another way…


## Install with `apt`, using NodeSource Binary Distribution

This is probably your best bet if you only work with one version of Node and still want the convenience of using `apt`. See the install instructions at [Nodesource binary distribution](https://github.com/nodesource/distributions), but here's the basics:

```sh
$ curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
$ sudo apt-get install -y nodejs
```

This method lets you pick a modern version of Node.js, and still use `apt`, so you can get updates pretty easily. Unfortunately, NodeSource only build for armv7/armv8, so this won't work for Raspberry Pis that use armv6, like Pi 1 and the Pi Zero/Zero W. For those, see the next choice down.

## Install a binary from Nodejs.org

You can download and install binaries of Node.js from https://nodejs.org. This method works for all Raspberry Pi models, as long as the official distribution keeps building for armv6.

**Note: it looks like official builds for Node on Linux armv6 stopped with the v12.x releases. The last version to get official builds for this platform was v11.x, which is now retired. I recommend sticking with the v10.X/LTS Dubnium releases. At least that version will get support —including security updates— through April 2021.**

**The good news: you can still get up-to-date armv6 builds from the Unoffical Builds project, including the lastest v12.X LTS versions: https://unofficial-builds.nodejs.org/**

Here's how that works:

```
wget https://nodejs.org/dist/vX.X.X/node-vX.X.X-linux-armv6l.tar.xz
tar -xf node-vX.X.X-linux-armv6l.tar.xz
sudo mv node-vX.X.X-linux-armv6l /usr/local/node
cd /usr/bin
sudo ln -s /usr/local/node/bin/node node
sudo ln -s /usr/local/node/bin/npm npm
node -v  # Verifying that the Node.js install worked
npm -v   # Verifying that the npm install worked
```

I got the lowdown on that from https://github.com/nebrius/raspi-io/wiki/Getting-a-Raspberry-Pi-ready-for-NodeBots. The downside of the above is that it doesn't put the `/usr/local/node/bin` directory on your path, so any binary commands that get installed when you do something like `npm i -g cli-providing-package` will require using the full path.

### Another way to install the binary

An alternative method is described here: https://github.com/nodejs/help/wiki/Installation. You might prefer setting up Node this way, since it makes it slightly easier to install mutiple versions (as it puts each version of Node in a subdirectory of `/usr/local/lib/nodejs` and adds variables to `.profile` that can be used to switch the versions. This is the method I use on one of my older Pis, and I find that it makes upgrading fairly easy (though far more time-consuming that using `nvm` or `apt`).

NOTE: if you install with this method, you may find that the `node`,`npm`, `npx` or any other binary commands installed via a global node module do not work correctly with `sudo`. This is because the default `sudoers` config uses a safe reset for the path. You can override this by setting the path explicitly, like this:

```
sudo env "PATH=$PATH" npm -g i some_module
```

You can also choose to create symlinks so they'll appear in your path:

```
sudo ln -s `which node` /usr/bin/node
sudo ln -s `which npm` /usr/bin/npm
sudo ln -s `which npx` /usr/bin/npx
```

I choose to do this, but it means I have to update my symlinks on installation of a new version of Node.

## Install with `nvm`

NVM is a great tool for installing and managing multiple versions of Node.js. I'm not going into detail here (using the installer script and the tools is already well documented), but this is my goto choice, since it allows for simple upgrades and multiple versions.

A quick summary:

1. Install `nvm` with the install script

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.2/install.sh | bash
```

or 
      
```
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.2/install.sh | bash
```

2. Install a version of Node

```
nvm install lts/erbium
```

More detail on NVM here available here: https://github.com/nvm-sh/nvm.
