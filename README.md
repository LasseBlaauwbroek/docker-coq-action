# Docker-Coq action

This GitHub action relies on
[coqorg/coq](https://hub.docker.com/r/coqorg/coq/) Docker images.

For more details about these images, see the
[docker-coq wiki](https://github.com/coq-community/docker-coq/wiki).

Assuming the Git repositiory contains a `foo.opam` file, it will run
(by default) the following commands:

```
opam config list; opam repo list; opam list
opam pin add -n -y -k path foo .
opam update -y
opam install -y -v -j 2 foo
opam list
opam remove foo
```

## Usage

See [action.yml](./action.yml)

### Example

```yaml
uses: erikmd/docker-coq-action@alpha
with:
  opam_file: 'foo.opam'
  coq_version: 'dev'
  ocaml_version: '4.07-flambda'
```

### Inputs

#### `opam_file`

**Required** the path of the `.opam` file, relative to the repo root.

#### `coq_version`

*Optional* The version of Coq. E.g., `"8.10"`. Default
`"latest"` (= latest stable version).

#### `ocaml_version`

*Optional* The version of OCaml. Default `"minimal"`.
Among `"minimal"`, `"4.07-flambda"`, `"4.09-flambda"`.

#### `custom_script`

*Optional* The main script run in the container; may be overridden. Default:

    startGroup Print opam config
      opam config list; opam repo list; opam list
    endGroup
    startGroup Fetch dependencies
      opam pin add -n -y -k path $PACKAGE .
      opam update -y
    endGroup
    startGroup Build
      opam install -y -v -j 2 $PACKAGE
      opam list
    endGroup
    startGroup Uninstallation test
      opam remove $PACKAGE
    endGroup

*Note: this option is named `custom-script` rather than `script` or
`run` to discourage changing its recommended, default value, while
keeping the flexibility to be able to change it. This experimental
option might be removed, or replaced with other similar options.*

## TODO/IFNEEDBE

* We should document the contents/generation of a Coq `.opam` file
  (e.g., with a link to coq-community templates)
* We might want to replace the `custom_script` option with `script`,
  `after_script`, etc.
* We might want to allow the user to override the name of the
  underlying (docker-coq) image
