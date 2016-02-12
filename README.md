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

Thus here is `bak` on GitHub as open source. Hopefully it will be found useful to others as well. :-)


Options
-------


``` text
-a | --append                   Choose to append the label to the 
-e LABEL | --extension LABEL    The defaul is '.ba' (sans quotes) but you can
                                specify whatever you like with this option.
-f | --force                    Force the overwritting of preexisting files.
-n | --newer                    Only overwrite files if the source file is newer
                                than the backup.
-t | --dry-run | --test-run     Do a test or 'dry run'. Just output what would
                                happen. Don't actually do anything.
-v | --version                  Output the name of the app and version number.
-V                              Output the version number only of the app.
```

Usage
-----

``` text
# Output app and version number
$ bak --version
Bak v0.2.0

# Output app version number
$ bak -V
0.2.0

# Dry run backup. Do nothing, just pretend.
$ echo 'version 1' > filename.ext
$ bak filename.ext -e ~test --dry-run
cp filename.ext filename.ext~test (dry run)
$ cat filename.ext
version 1
$ cat filename.ext~test
cat: filename.ext~test: No such file or directory

# Backup with custom label
$ bak filename.ext -e ~test
cp filename.ext filename.ext~test
$ cat filename.ext~test
version 1

# Backup with custom label but backup already exists
$ echo 'version 2' > filename.ext
$ bak filename.ext -e ~test
  filename.ext~test already exists.
  Please add -f or --force to force an overwrite of the backup.
  Or use -n or --newer to overwrite the backup if the source is newer.

# Backup with custom label but force overwrite of existing backups
$ bak filename.ext -e ~test -f
cp filename.ext filename.ext~test
$ cat filename.ext~test
version 2

# Backup with custom label but only overwrite existing backups if
# the source is newer
$ echo 'version 3' > filename.ext
$ bak filename.ext -e ~test -n
  'filename.ext' is newer than 'filename.ext~test'. Overwriting backup file.
$ cat filename.ext~test
version 3

# Multiple file backups
$ echo 'File 1' > filename1.ext
$ echo 'File 2' > filename2.ext
$ echo 'File 3' > filename3.ext
$ bak filename?.ext
cp filename1.ext filename1.ext.bak
cp filename2.ext filename2.ext.bak
cp filename3.ext filename3.ext.bak

# Versioned backups
$ bak bak -e _v0.2.0
cp bak bak_v0.2.0

# Backup with custom label appended to the basename instead of the end
# of the file name
$ bak -a basename -e ~imp filename.ext
cp filename.ext filename~imp.ext
```



