# rofi-pass

#### bash script to handle pass storages in a convenient way

![rofi-pass](screenshot.jpg "rofi-pass in action")

## Features:

* Open URLs of entries with hotkey
* Add new Entries to Password Storage
* Edit existing Entries
* Generate new passwords for entries
* Inline view, which can copy/type individual entries
* Move/Delete existing entries
* Support for multiple roots for password-store (e.g. separate work from private passwords)
* Type any field from entry
* Auto Type User and Password. Format of password files are expected to be like:
* Bookmarks mode (default: Alt+x)
```
foobarmysecurepassword
user: MyUser
url: http://my.url.foo
```
* Auto Typing of more than one field. This expects a autotype field in password file.
```
foobarmysecurepassword
---
user: MyUser
SomeField: foobar
AnotherField: barfoo
url: http://my.url.foo
autotype: SomeField :tab UserName :tab AnotherField :tab pass
```
The `:tab` field has a special meaning. this will hit the tab key, obviously.<br>
Same for `:space`, which will hit the space key, can be used to activate checkboxes.
In addition to those `:enter` and `:delay` are available.

* All Hotkeys are configurable in config file
* user, url and autotype field names are also configurable

## Requirements
* pass (http://www.passwordstore.org/)
* sed
* rofi (https://github.com/DaveDavenport/rofi)
* xdotool
* gawk
* bash
* pwgen

### BSD
* gnugrep
* gawk

## Configuration
rofi-pass may read its configuration values from `/etc/rofi-pass` and/or `$HOME/.config/rofi-pass/config`.
For an example configuration please take a look at the included `config.example` file.

## Extras
rofi-pass comes with a tiny helper script, which makes it easier to create new pass entries.
Just run it with

```
addpass --name "My new Site" +user "zeltak" +branch "branch" +custom "foobar" +autotype "branch :tab user :tab pass"
```

* First argument `--name` is mandatory. This will be the filename of the new password entry.
* Second argument can be `--root` followed by absolute path to your password-store. addpass also uses root config setting from rofi-pass config file. If both are not found, PASSWORD_STORE_DIR variable is checked. If none of the above are found, the default location `$HOME/.password-store` is used.
* `--root` can also be a colon separated list of directories, in which case you can navigate between them on the main menu with Shift+Left and Shift+Right.
* Fieldnames are defined with `+` and the actual value is defined inside the quotations. You can add as many fields as you like
* Running arbitrary commands with the `shell_whatever` feature (see OTP section below).

Also included is an import script for keepass2 databases. It's the same script that can be downloaded from the pass homepage, with some minor modifications to match rofi-pass structure.

### Handling OTP tokens
You can add for example the following field to an entry:

```
shell_otp: echo JCT...XR3 | xargs -n1 /usr/bin/oathtool --totp=sha512 -b -s 30 -d 6
```

When `rofi-pass` encounters this during typing, then the shell command
will be executed first and then whatever is printed to the stdout by
that command is typed by `rofi-pass`.

In the example above, we use `xargs` and `echo` together to hide the
secret (`JCT...XR4`) from other users running `ps -ef` or similar
commands at the same time on the machine.

## FAQ

* rofi pass prints garbage instead of my actual passes <br>Make sure to run `setxkbmap <language> <variant>` at the start of your Xorg session.

## Alternative

jreinert has written the roughly compatible tool [autopass](https://github.com/jreinert/autopass). It has less features, but definately saner code.
Also he provided a nice little script called `passed` to change your fieldnames. [link](https://github.com/jreinert/passed)
And finally it ships with a script "pass2csv.py", which can export your pass store to csv format.
