# create-bazel-workspace

Create a polyglot Bazel workspace easily, with sane defaults for multiple languages (layers).

## Quick start

```
$ go get github.com/jin/create-bazel-workspace

$ go install github.com/jin/create-bazel-workspace

$ $GOPATH/bin/create-bazel-workspace -dir <output-dir> <layer 1> <layer 2> ... <layer N>
```

For example, if you want `go` and `scala`, run 

```
$ create-bazel-workspace go scala
```

The default output directory is named `bazel-workspace`.

## Layers

A *layer* contains the following:

-  Minimal example that makes use of the layer's rulesets (e.g. `android_binary`
  and `android_library` for the `android` layer). This example is available as
  a top level target `//:<layer>_example` in the generated workspace.
- `WORKSPACE`: Specifies the WORKSPACE content
- `BUILD.bazel`: Specifies the top level BUILD file
- `post_create.txt`: Further instructions for a layer after `create-bazel-workspace`

When you specify multiple layers, files in each layer will be appended to the
`base` layer sequentially. 

Supported `<layer>` are the the names of the directories in this repository, e.g.
`android`, `scala`, `go`.

## Additional instructions

Depending on the `layer`, there may be more commands to run after
`create-bazel-workspace`. For example, the `go` layer may require running `bazel
run //:gazelle`. These instructions are logged in
`<bazel-workspace-output>/instructions.md`:

```
$ cat bazel-workspace/instructions.md
# Instructions for the base layer:

Please file issues at https://github.com/jin/create-bazel-workspace

# Instructions for the go layer:

From rules_go, please run:

    $ bazel run //:gazelle

This will generate a BUILD.bazel file for each Go package in your repository. 
You can run the same command in the future to update existing build files with 
new source files, dependencies, and options.

Please file issues at https://github.com/bazelbuild/rules_go

# Instructions for the android layer:

From https://github.com/quittle/bazel_android_sdk_downloader:

    $ bazel run @androidsdk//install

This will attempt to download the SDK for the versions specified. If you change
these values, clean the workspace, or check it out fresh, you will need to
re-run this command to download and install the SDK again.

Please file issues at https://github.com/bazelbuild/rules_android

# Instructions for the scala layer:

Please file issues at https://github.com/bazelbuild/rules_scala
```

## Development

This project can be built with Bazel. Build the binary by running 

```
$ bazel run //:create-bazel-workspace
```

To regenerate the BUILD files, run 

```
$ bazel run //:gazelle
``` 

To add a new dep, run

```
$ bazel run //:gazelle -- update-repos example.com/dep/repo
```

## Contributing a Layer

To add a layer, open a pull request with new top-level directory named after the layer, containing these files:

- `<layer>/WORKSPACE.bzl`
- `<layer>/BUILD.bazel.bzl`
- `<layer>/post_create.txt`

These files are allowed to be empty.

## Idea

`create-bazel-workspace` is inspired by create-react-app and Spacemacs's concept of layers.
