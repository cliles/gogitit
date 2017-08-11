# gogitit

gogigit assembles parses a manifest and assembled an output directory
consisting of portions of multiple remote git repositories and sub-directories
within them.

It's intended use is for an operations team which needs to consume Ansible
roles and playbooks from a variety of other teams. At present however the solution
is generic and not tied to Ansible.

It is effectively a vendoring tool but allows merging into one tree as this is
helpful when assembling ansible roles and playbooks from many locations. Whether or
not the resulting output directory is checked into git is up to the user (for now).

Written in Python, not Go (though it almost was!), despite how awesome the
name-play would have been.

## Behavior

  * Specify a manifest listing multiple entries of source git repo,
    sub-directory, revision/branch/tag, destination relative to the output dir.
  * Output directory defaults to current directory if not set.
  * Output directory will be created if it does not exist, it should *never* be
    deleted by this application, the user may do so explicitly if they wish.
  * Output directory will eventually contain a cache tracking the last fetched
    revision for each entry in the manifest.
  * For each entry:
    * Fetch the git repo to temporary location.
    * Handle directories intelligently:
      * Copy a source directory to a destination within output directory.
      * Copy the contents of a source directory to a destination within the
        output directory. (may be merging with other sources)
        * Check for conflicts before doing anything.
    * If source is a file, overwrite.
      * We assume that if you stop tracking a file and remove it from the
        mainfest, the user will be responsible for removing it from a long
        running output directory.
  * Be sure to never copy in nested .git directories.
  * Support multiple sub-directories coming out of one git repo without re-cloning multiple times.
  * Support keeping a cache of git clones in tmp just update them on execution. (much more time/bandwidth efficient)

## Example Manifest

```
---
entries:
- id: openshift-ansible
  source: https://github.com/openshift/openshift-ansible.git
  copy:

  # Copy everything in roles/* into roles/ in the output dir.
  # Allows for merging multiple directories into one.
  - from: roles/
    to: roles/

  # Copy a directory itself to the output dir:
  - from: examples
    to: examples

  # Copy individual files:
  - from: playbooks/something.yml
    to: playbooks/
```

# Installation

TBD

# Hacking

Targetting python3 because 2017.

```
virtualenv-3 venv
. venv/bin/activate
pip3 install --editable .
```


