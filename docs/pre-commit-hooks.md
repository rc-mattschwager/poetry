---
title: "pre-commit hooks"
draft: false
type: docs
layout: single

menu:
  docs:
    weight: 120
---

# pre-commit hooks

pre-commit is a framework for building and running
[git hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks).
See the official documentation for more information: [pre-commit.com](https://pre-commit.com/)

This document provides a list of available pre-commit hooks provided by Poetry.


{{% note %}}
If you specify the `args:` for a hook in your `.pre-commit-config.yaml`,
the defaults are overwritten. You must fully specify all arguments for
your hook if you make use of `args:`.
{{% /note %}}

## poetry-check

The `poetry-check` hook calls the `poetry check` command
to make sure the poetry configuration does not get committed in a broken state.

### Arguments

The hook takes the same arguments as the poetry command.
For more information see the [check command]({{< relref "cli#check" >}}).


## poetry-lock

The `poetry-lock` hook calls the `poetry lock` command
to make sure the lock file is up-to-date when committing changes.

### Arguments

The hook takes the same arguments as the poetry command.
For more information see the [lock command]({{< relref "cli#lock" >}}).


## poetry-export

The `poetry-export` hook calls the `poetry export` command
to sync your `requirements.txt` file with your current dependencies.

{{% note %}}
It is recommended to run the [`poetry-lock`](#poetry-lock) hook prior to this one.
{{% /note %}}

### Arguments

The hook takes the same arguments as the poetry command.
For more information see the [export command]({{< relref "cli#export" >}}).

The default arguments are `args: ["-f", "requirements.txt", "-o", "requirements.txt"]`,
which will create/update the requirements.txt file in the current working directory.

You may add `verbose: true` in your `.pre-commit-config.yaml` in order to output to the
console:

```yaml
hooks:
  - id: poetry-export
    args: ["-f", "requirements.txt"]
    verbose: true
```

Also, `--dev` can be added to `args` to write dev-dependencies to `requirements.txt`:

```yaml
hooks:
  - id: poetry-export
    args: ["--dev", "-f", "requirements.txt", "-o", "requirements.txt"]
```


## Usage

For more information on how to use pre-commit please see the [official documentation](https://pre-commit.com/).

A full `.pre-commit-config.yaml` example:

```yaml
repos:
  - repo: https://github.com/python-poetry/poetry
    rev: ''  # add version here
    hooks:
      - id: poetry-check
      - id: poetry-lock
      - id: poetry-export
        args: ["-f", "requirements.txt", "-o", "requirements.txt"]
```

## FAQ

### Why does `pre-commit autoupdate` not update to the latest version?

`pre-commit autoupdate` updates the `rev` for each repository defined in your `.pre-commit-config.yaml`
to the latest available tag in the default branch.

Poetry follows a branching strategy, where the default branch is the active developement branch
and fixes gets back ported to stable branches. New tags are assigned in these stable branches.

`pre-commit` does not support such a branching strategy and has decided to not implement
an option, either on the [user side](https://github.com/pre-commit/pre-commit/issues/2512)
or [hook author side](https://github.com/pre-commit/pre-commit/issues/2508), to define a branch for lookup the latest
available tag.

Thus, `pre-commit autoupdate` is not usable for the hooks described here.

You can avoid changing the `rev` to an unexpected value, by using the `--repo` parameter (may be specified multiple
times), to explicit list repositories that should be updated. An option to explicit exclude
repositories [will not be implemented](https://github.com/pre-commit/pre-commit/issues/1959) into `pre-commit`.
