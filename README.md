# SixArm.com » <br> Shell Style Guide

General advice:

  * [Writing safe shell scripts](https://sipb.mit.edu/doc/safe-shell/)
  * [Rich’s sh (POSIX shell) tricks](http://www.etalabs.net/sh_tricks.html)
  * [Google Shell Script Style Guide](https://google.github.io/styleguide/shell.xml])
  * [Filenames and Pathnames in Shell: How to do it Correctly](http://www.dwheeler.com/essays/filenames-in-shell.html)

Summary:

  * Use POSIX whenever possible because POSIX is portable.
  * Set shell script flags to protect the script such as `set -euf`.
  * Quote liberally such as `"$var"` instead of just `$var`, for safety.
  * Bulletproof to handle characters such as a quote, newline, leading dash.
  * Format dates and times using UTC and using ISO8601 standards.
  * Executables should have no extension (strongly preferred).
  * Use `printf` instead of `echo` because of security and stability.
  * Use `$()` instead of backticks for subshell commands.
  * Use `mktemp` instead of `tempfile`, and instead of ad hoc $$, RANDOM, etc.
  * Use `trap "..." EXIT` instead of TERM, INT, HUP, etc.
  * Store conf files in a configuration directory `${XDG_CONFIG_HOME:-$HOME/.config}`

Specifics:

  * [Safer scripts using `set` flags](safer-scripts-using-set-flags.md)
  * [Program name using a string or basename](program-name-using-a-string-name-or-basename.md)
  * [Configuration directory using XDG_CONFIG_HOME](configuration-directory-using-xdg-config-home.md)
  * [Help metadata: version, created, updated, license, contact](help-metadata-version-created-updated-license-contact.md)
  * [All-purpose functions: out, err, log, now, zid](all-purpose-functions-out-err-log-now-zid.md)
  * [Temporary directory using `mktemp` and `program`](temporary-directory-using-mktemp-and-program.md)
  * [Date &amp; time format using UTC and ISO8601](date-time-format-using-utc-and-iso8601.md)
  * [Do while loop](do-while-loop.md)
  * [Case statement that skips option dash flags](case-statement-that-skips-option-dash-flags.md)
  * [Subshell syntax using parentheses not backticks](subshell-syntax-using-parentheses-not-backticks.md)
  * [Temporary files using `mktemp` and `trap`](temporary-files-using-mktemp-and-trap.md)
  * [Find files with special characters](find-files-with-special-characters.md)
  * [While loop with index counter](while-loop-with-index-counter.md)



### Sample script

This sample script shows many of our style guide conventions that we tend to use for our public, larger, longer scripts.

    #/bin/sh
    set -euf
    help(){
    cat << EOF

    Foo: this script does foo stuff.

    Program: your-program-name-here
    Version: 1.0.0
    Created: 2016-01-14
    Updated: 2016-01-15
    License: GPL
    Contact: Your Name Here (you@example.com)
    EOF
    }

    out() { printf %s\\n "$*" ; }
    err() { >&2 printf %s\\n "$*" ; }
    log() { printf '%s %s %s\n' $( now ) $$ "$*" ; }
    now() { date -u "+%Y-%m-%dT%H:%M:%S,%NZ" ; }
    zid() { hexdump -n 16 -v -e '16/1 "%02x" "\n"' /dev/random }

    program() { echo "foo"; }
    version() { echo "1.1.0"; }
    confdir() { echo ${XDG_CONFIG_HOME:-$HOME/.config}; }
    tempdir() { echo $(mktemp -d -t $program); }

    if [ "$#" -eq 1 ]; then
      case "$1" in
        --help)
          help && exit 0
          ;;
        --program)
          program && exit 0
          ;;
        --version)
          version && exit 0
          ;;
        --confdir)
          confdir && exit 0
          ;;
        --tempdir)
          tempdir && exit 0
          ;;
      esac
    fi

    # Main source code starts here