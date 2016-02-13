Bak
===

A safe and simple CLI tool to add `.bak` to the end of a file or to prefix the file's extension. You can also specify your own label to suffix or prefix the files current extension with.


Rational
--------

There are shell tricks to make it relatively easy to copy a file with a new extension appended to it, `cp filename{,.bak}` for example in BASH or Zsh will copy a file named `filename` to `filename.bak` rather handily. But I really wanted to just do `bak filename` and be done with it. I also didn't want to accidently overwrite a file (`cp -n filename{,.bak}` would work) and the ability to force overwriting files I knew it was OK to do so (`cp -f filename{,.bak}` or `cp filename{,.bak}` would work as well generaly). But it's very easy to forget the `-n` for `cp` and once you've overwritten a file it's pretty much too late to get back the old version. I also wanted to be able to specify multiple files and `cp *{,.bak}` does not work. I could make that work in a for loop but I wanted simple! Simple and safe. Handle multiple files and ensure I only overwrite duplicates when I specifically call for it. But what if you want to label a file but leave the extension as it is on the end so syntax highlighting in your prefered text editor, etc. still works:

#### Example:

```
$ cp example.sh example.test.sh
```

I also wanted to be able to swap the label and file extensions positions. As it may be necessary if your development environment is treating files with certain extensions differently but you still need to keep the syntax highlighting now and again. This can also be done with shell tricks but I decided that as wonderful as all those shell tricks are I really just wanted to say `bak -s filename.ext.bak` to produce `filename.bak.ext`.

Thus here is `bak` on GitHub as open source. Hopefully it will be found useful to you and others as well. :-)


Options
-------

| Short Option           | Long Option                        | Description                                                                                                                                                                              |
| ------------           | -----------                        | -----------                                                                                                                                                                              |
| <pre>-a POSITION</pre> | <pre>--append POSITION</pre>       | Choose to append the label to the end of the file (the default) or the file's basename. POSITION should equal the string _basename_ or anything else will append to the end of the file. |
| `-c`                   | `--create-config`                  | Creates a Bak config file at `~/.config/bak.cfg` with the current defaults.                                                                                                              |
| <pre>-l LABEL</pre>    | <pre>--label LABEL</pre>           | The default is '.bak' (sans quotes) but you can specify whatever you like with this option. This is an API change from v0.3.0.                                                           |
| `-f`                   | `--force`                          | Force the overwritting of preexisting files which may or may not be backups.                                                                                                             |
| `-h`                   | `--help`                           | Show this help info.                                                                                                                                                                     |
| `-n`                   | `--newer`                          | Only overwrite files if the source file is newer than the backup.                                                                                                                        |
| `-s`                   | `--swap`                           | Swap the positions of the label and the extension.                                                                                                                                       |
| <pre>-t</pre>          | <pre>--dry-run or --test-run</pre> | Do a test or 'dry run'. Just output what would happen. Don't actually do anything.                                                                                                       |
| `-v`                   | `--version`                        | Output the name of the app and version number.                                                                                                                                           |
| `-V`                   | `--Version`                        | Output the version number only of the app. This is an API update from v0.3.0.                                                                                                            |


Config
------

You can now set default values for certain `bak` variables to customize your setup. These are the current defaults.

```
default_label='.bak'
dry_run=1
force_backup=1
label_append="$LABEL_APPEND_EXTENSION"
overwrite_newer=1
```

0
: Is `true` or `on` in shell scripting

1
: Is `false` or `off` in shell scripting

Thus by default the label used is '.bak' (sans quotes) and the label will be appended to the end of the file (it's extension if it has one). Dry runs, force overwrite of backups, and overwrite backups if source is newer are off by default. To change the default label to `~tmp`, overwrite backups if the source is newer (not something I recommend), and append labels to the basename instead of the extension you would create a file `~/.config/bak.cfg` which is a text file `bak.cfg` within the directory/folder `.config` within your home directory and set the values:

```
default_label='~tmp'
dry_run=1
force_backup=1
label_append="$LABEL_APPEND_BASENAME"
overwrite_newer=0
```

Bak can do this for you with `bak -c`.

Note that `label_append` holds a string value. I recommend using the 'constants' (BASH doesn't actually have constants) `$LABEL_APPEND_BASENAME` or `$LABEL_APPEND_EXTENSION` to reference the appropriate strings. In case the values of those strings change in the future.

Usage
-----

``` text
# Output app and version number
$ bak --version
Bak v1.0.0

# Output app version number
$ bak -V
1.0.0

# Create a config file
$ bak -c
  Now attempting to create the Bak config file for runeimp
  Config file creation successful at '/Users/runeimp/.config/bak.cfg'

# Create some test files
$ echo 'version 1' > filename.ext
$ echo 'File 1' > filename1.ext
$ echo 'File 2' > filename2.ext
$ echo 'File 3' > filename3.ext


# Dry run backup. Do nothing, just pretend.
$ bak filename.ext -l ~test --dry-run
cp filename.ext filename.ext~test (dry run)
$ cat filename.ext
version 1
$ cat filename.ext~test
cat: filename.ext~test: No such file or directory

# Backup with custom label
$ bak filename.ext -l ~test
cp filename.ext filename.ext~test
$ cat filename.ext~test
version 1

# Backup with custom label but backup already exists
$ echo 'version 2' > filename.ext
$ bak filename.ext -l ~test
  filename.ext~test already exists.
  Please add -f or --force to force an overwrite of the backup.
  Or use -n or --newer to overwrite the backup if the source is newer.

# Backup with custom label but force overwrite of existing backups
$ bak filename.ext -l ~test -f
cp filename.ext filename.ext~test
$ cat filename.ext~test
version 2

# Backup with custom label but only overwrite existing backups if
# the source is newer
$ echo 'version 3' > filename.ext
$ bak filename.ext -l ~test -n
  'filename.ext' is newer than 'filename.ext~test'. Overwriting backup file.
$ cat filename.ext~test
version 3

# Multiple file backups
$ bak filename?.ext
cp filename1.ext filename1.ext.bak
cp filename2.ext filename2.ext.bak
cp filename3.ext filename3.ext.bak

# Versioned backups
$ bak filename.ext -l _v3.0.0
cp filename.ext filename.ext_v3.0.0

# Backup with custom label appended to the basename instead of the end
# of the file name
$ bak -a basename -l ~imp filename.ext
cp filename.ext filename~imp.ext

# Swap the label and extension
```



