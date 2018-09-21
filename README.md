# google-java-format-git-pre-commit-hook
A git pre-commit hook script for formatting the Java source files according to Google Java Style Guide

Place the `pre-commit` script (with that name and executable bit set) in your `.git/hooks` directory.

The script will download (and cache) the Jar from https://github.com/google/google-java-format
and try to re-format all changed `.java` files in the repository.  If any re-formatting did occur,
the commit will be aborted, so that you can incorporate formatting fixes.

# License

The script is licensed under Apache License, Version 2.0.
