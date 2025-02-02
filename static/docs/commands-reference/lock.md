# lock

Lock a [DVC-file](/doc/user-guide/dvc-file-format)
([stage](/doc/commands-reference/run)). Use `dvc unlock` to unlock the file.

If DVC-file is locked the stage is considered _done_ and `dvc repro` will not
run commands to rebuild outputs even if some dependencies have changed and even
if `--force` is provided.

```usage
usage: dvc lock [-h] [-q] [-v] targets [targets ...]

positional arguments:
    targets               DVC-files
```

## Description

Lock is useful to avoid syncing data from the top of a pipeline and keep
iterating on the last stages only. In this sense `lock` causes any DVC-file to
behave as an _orphan_ stage file as if created with `dvc add`.

## Options

- `-h`, `--help` - prints the usage/help message, and exit.

- `-q`, `--quiet` - do not write anything to standard output. Exit with 0 if no
  problems arise, otherwise 1.

- `-v`, `--verbose` - displays detailed tracing information.

## Example

- First, let's create a sample DVC-file:

```dvc
$ echo foo > foo
$ dvc add foo
Adding 'foo'...

$ dvc run -d foo -o bar cp foo bar
Running command:
	cp foo bar
...
```

- Then, let's change the file `foo` that the stage described in `bar.dvc`
  depends on:

```dvc
$ rm foo
$ echo foo1 > foo
$ dvc status

bar.dvc
        deps
                changed:  foo
foo.dvc
        outs
                changed:  foo
```

- Now, let's lock the `bar` stage:

```dvc
$ dvc lock bar.dvc
$ dvc status

  foo.dvc
          outs
                  changed:  foo
```

- Run `dvc unlock` to unlock it back:

```dvc
$ dvc unlock bar.dvc
$ dvc status

  bar.dvc
          deps
                  changed:  foo
  foo.dvc
          outs
                  changed:  foo
```
