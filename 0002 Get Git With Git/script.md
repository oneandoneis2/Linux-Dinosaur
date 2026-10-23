Git is one of my favourite and most-used tools. I like it *so* much, in fact, that I habitually install it more than once on every machine I own. And after this video, maybe you will too.

```
$ type -a git
git is /home/dino/bin/git
git is /usr/bin/git
git is /bin/git
```

This is Linux Dinosaur. I've been using Linux for three decades, and this channel is where I share things I've learned with you. Welcome.

So, why install anything twice?

Well, in the case of git, it's basically *lazy man's bootstrapping* - but to protect my ego, I like to think of it as *sensible and efficient redundancy*. It allows me to keep a known-good version, whilst also being able to try something a bit less safe.

On a typical modern Linux machine, your main choice when installing anything is basically "use your distro's package manager" or "don't do that"

*My* approach is a Third Way. I use *both* options, and I suggest that you at least consider doing so too.

What does this look like? Well, first, I'll use the package manager
```
sudo apt-get install git
```

As you'd expect from apt, this is quick and easy and Just Works
```
$ git --version
git version 2.53.0
```

And there are plenty of advantages to getting git like this. It gives you a thoroughly-tested, trustworthy version of git that has been specifically built for your exact OS, and it makes it available to all users on the machine. We *could* just stop here.

But that's no fun! What if we want to be on the bleeding edge of git, testing the latest release and newest features? What if there are things missing in the packaged version that we don't want to be without?

Well, in *that* case, we use the packaged version of git to get hold of the git sources, and we build ourselves a new git binary just for us. A build of the latest, freshest version.

How do we do that?

We go to https://kernel.org/ and click on the `git` link. We scroll down the list to the `git.git` part, and then scroll to the bottom of *that* page to find the link for cloning the repo: https://git.kernel.org/pub/scm/git/git.git

Now, I like to keep all my git repos in their own directory, so let's create that quickly
```
mkdir git_repos
cd git_repos
```

And now, let's use git to clone the git repo!
```
git clone https://git.kernel.org/pub/scm/git/git.git
```

This gives us our own complete copy of git - its current sources and entire history - right here on our local machine. Let's take a look

```
cd git
ls
```

Yep, that's a lot of files. The one we care about, though, is `INSTALL`
```
less INSTALL
```

And the very first thing it tells us is that all we need is a simple `make, make install` and we'll get a new git in our personal `~/bin` directory.

Let's just make sure that directory exists and is in our $PATH before going any further...
```
mkdir ~/bin
echo $PATH
echo 'PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
echo $PATH
```

There. With our home `bin/` directory as the first entry in our PATH, we can put any experimental tools in there to have them become the default. But the original safe versions are still available if needed.

Next, we don't want to build git from the current state. We want to check out the latest proper release. The kernel.org page tells us what that is: `v2.56.0`

So, we switch to that 
```
git checkout v2.56.0
make
```

And what we'll see on a stock Ubuntu install that's pretty new and hasn't had much installed on it yet is, of course, failure: missing libraries!

To find the packages you need to install, it can be helpful to take a peek at the CI script included in git
```
less ci/install-dependencies.sh
```

Find your distro in here and take a look at what their CI uses, the chances are you'll be wanting many of the same packages. I'm on Ubuntu, so I happen to know that what I need is this:
```
sudo apt-get install build-essential libssl-dev libcurl4-gnutls-dev libexpat1-dev gettext cmake gcc unzip curl autoconf cargo
```

(figlet -c -t -f lean "TIME PASSES")

And now, let's `make` again
```
make
```

(figlet -c -t -f lean "TIME PASSES")

```
make install
```

And, as if by magic...
```
$ ~/bin/git --version
git version 2.56.0
$ type -a git
git is /home/dino/bin/git
git is /usr/bin/git
git is /bin/git
```

Perfect! The latest git for our own user, with all the pros and cons of a bleeding-edge version. But we still have the solid, stable, reliable packaged version of git installed for all other users.

And ourselves, if we mess up our home-made version.
(new terminal)
```
$ git --version
git version 2.56.0
$ /bin/git --version
git version 2.53.0
```

And now there's nothing to do but enjoy the smug satisfaction of using the latest version of git, built from git's source code in the git repository that we obtained by doing a `git clone`

The nice thing about this approach is, it can be used for pretty much any tool: Install via the package manager to give you a reliable version, *and* build your own version for keeping up-to-date or experimenting with.

I hope you found this video useful, there are more on the way. Copies of all the resources I've shown can be found in the github repo linked in the description.