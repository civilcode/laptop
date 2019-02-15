# Laptop

Laptop is a script to set up an macOS laptop for web development.

It can be run multiple times on the same machine safely. It installs, upgrades, or skips packages
based on what is already installed on the machine.

## Requirements

We support:

* macOS Sierra (10.12)
* macOS High Sierra (10.13)
* macOS Mojave (10.14)

## Install (first account, i.e. admin)

Make sure Atom is already installed on the system beforehand.

Download, review, then execute the script:

    curl --remote-name https://raw.githubusercontent.com/civilcode/laptop/master/mac
    less mac
    sh mac 2>&1 | tee ~/laptop.log

The script won't install homebrew and its packages as the should be already
installed by the main user account.

## Install (second and additional accounts, i.e. personal user)

Download, review, then execute the script:

    curl --remote-name https://raw.githubusercontent.com/civilcode/laptop/master/mac
    less mac
    sh mac ANOTHER_USER=1 2>&1| tee ~/laptop.log

## Debugging

Your last Laptop run will be saved to `~/laptop.log`.
Read through it to see if you can debug the issue yourself.
If not, copy the lines where the script failed into a
[new GitHub Issue](https://github.com/civilcode/laptop/issues/new) for us.
Or, attach the whole log file as an attachment.

## What it sets up

Please see inline comments to see what is setup.

It should take less than 15 minutes to install (depends on your machine).

## Customize in `~/.laptop.local`

Your `~/.laptop.local` is run at the end of the Laptop script.
Put your customizations there.
For example:

    #!/bin/sh

    brew bundle --file=- <<EOF
    brew "Caskroom/cask/dockertoolbox"
    brew "go"
    brew "ngrok"
    brew "watch"
    EOF

    default_docker_machine() {
      docker-machine ls | grep -Fq "default"
    }

    if ! default_docker_machine; then
      docker-machine create --driver virtualbox default
    fi

    default_docker_machine_running() {
      default_docker_machine | grep -Fq "Running"
    }

    if ! default_docker_machine_running; then
      docker-machine start default
    fi

    fancy_echo "Cleaning up old Homebrew formulae ..."
    brew cleanup
    brew cask cleanup

    if [ -r "$HOME/.rcrc" ]; then
      fancy_echo "Updating dotfiles ..."
      rcup
    fi

Write your customizations such that they can be run safely more than once.
See the `mac` script for examples.

Laptop functions such as `fancy_echo` and `gem_install_or_update` can be used in your `~/.laptop.local`.

See the [thoughtbot wiki](https://github.com/thoughtbot/laptop/wiki) for more customization examples.

## How to test

To test this script on a clean install. Use the VM Fusion virtual machine image we have setup.

## Credits

The CivilCode Inc. laptop script is based on and inspired by
[thoughtbot's laptop](https://github.com/thoughtbot/laptop) script.

thoughtbot's original work remains covered under an
[MIT License](https://github.com/thoughtbot/laptop/blob/c997c4fb5a986b22d6c53214d8f219600a4561ee/LICENSE).
