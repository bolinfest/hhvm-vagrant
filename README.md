# Nuclide Remote Server Demo

This sets up a Vagrant VM with HHVM, Flow, and Mercurial
and explains how to connect to it to demo remote development in Nuclide.

## Requirements

* [VirtualBox](https://www.virtualbox.org)
* [Vagrant](http://vagrantup.com)

## Installation

```bash
$ git clone https://github.com/bolinfest/hhvm-vagrant.git ~/nuclide-demo
$ cd ~/nuclide-demo
$ vagrant up
```

If everything went according to plan, you should be able to load
http://127.0.0.1:8080/ in your browser, and it should display:

**Hack Works !**

You should also make sure you can ssh using private keys by testing the following from
the command line:

```bash
$ ssh -i ~/nuclide-demo/.vagrant/machines/default/virtualbox/private_key -p 2222 vagrant@127.0.0.1
```

You should be able to ssh in directly without being prompted for a password.

## Connecting from Nuclide

From the **Packages** menu, choose **Connect...** and fill out the dialog as follows:

**Username:** `vagrant`

**Server:** `127.0.0.1`

**Initial Directory:** `/vagrant/www`

**Private Key File:** `~/nuclide-demo/.vagrant/machines/default/virtualbox/private_key`
(radio button should be checked)

**SSH Port:** 2222

**Remote Server Command:** `/home/vagrant/nuclide/pkg/nuclide/server/nuclide-start-server`

You should click the **OK** button, and from there, you should be able to connect without
being prompted for a password. You should see the remote `www` folder loaded in the file tree.
