# Docker Commands

Skaffold exposes some the of the dockerfile introspection functionality that it uses under the hood.

#### skaffold docker deps

Many build systems require the developer to manually list the dependencies of a dockerfile.  Skaffold can inspect a dockerfile and output the file dependencies.  

```
$ skaffold docker deps --context examples/getting-started -v error
examples/getting-started/main.go
```

**Example Makefile rule for conditional docker image rebuilds**
```Makefile
out/getting-started: $(shell skaffold docker deps -c examples/getting-started -v error)
	docker build -t getting-started . -q > out/getting-started
```

#### skaffold docker context

Skaffold can also generate a tarball that can be used as a "docker context" for hosted build systems. To use this with Google Cloud Build:

```
$ skaffold docker context --filename examples/getting-started/Dockerfile --output=context.tar.gz
$ gcloud container builds submit context.tar.gz --tag="gcr.io/$(gcloud config get-value project)/skaffold-context:latest"
```

This tarball will contain only the sources needed to build the Docker image, which will result in a faster upload than the default behavior.