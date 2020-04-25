dl is an ultra simple ultra light command designed to download a using http and outputs to stdout

```bash
GO111MODULE=on go run github.com/edwarnicke/dl ${url} > ${filename}
``` 

Example:

```bash
GO111MODULE=on go run github.com/edwarnicke/dl \
https://github.com/spiffe/spire/releases/download/v0.9.3/spire-0.9.3-linux-x86_64-glibc.tar.gz > \
spire-0.9.3-linux-x86_64-glibc.tar.gz
```


This is particularly handy when building a Dockerfile that has go but not curl/wget/httpie:

```dockerfile
ARG URL
FROM ${image}
RUN GO111MODULE=on go run github.com/edwarnicke/dl ${URL}
```

If you are trying to download a tarball, its simple to pipe it to tar:

```bash
GO111MODULE=on go run github.com/edwarnicke/dl ${url to tar.gz} | tar -xzvf -
```
Example:

```bash
GO111MODULE=on go run github.com/edwarnicke/dl \
    https://github.com/spiffe/spire/releases/download/v0.9.3/spire-0.9.3-linux-x86_64-glibc.tar.gz | \
tar -xzvf -
```

or drop that output to a given directory:

```bash
GO111MODULE=on go run github.com/edwarnicke/dl ${url to tar.gz} | tar -xzvf - -C ${directory to unpack in}
```

Example:

```bash
GO111MODULE=on go run github.com/edwarnicke/dl \
    https://github.com/spiffe/spire/releases/download/v0.9.3/spire-0.9.3-linux-x86_64-glibc.tar.gz | \
    tar -xzvf - -C /opt
```

or extract a particular file:

```bash
GO111MODULE=on go run github.com/edwarnicke/dl ${url to tar.gz} | tar -xzvf - -C ${directory to unpack in} ${list of files in your tarball you want to extract}
```

Example:

```bash
GO111MODULE=on go run github.com/edwarnicke/dl \
    https://github.com/spiffe/spire/releases/download/v0.9.3/spire-0.9.3-linux-x86_64-glibc.tar.gz | \
    tar -xzvf - -C /opt ./spire-0.9.3/bin/spire-server
```

Example:

```bash
GO111MODULE=on go run github.com/edwarnicke/dl \
    https://github.com/spiffe/spire/releases/download/v0.9.3/spire-0.9.3-linux-x86_64-glibc.tar.gz | \
    tar -xzvf - -C /opt ./spire-0.9.3/bin/spire-server ./spire-0.9.3/bin/spire-agent
```

You can even strip off the leading path:

```bash
GO111MODULE=on go run github.com/edwarnicke/dl ${url to tar.gz} | tar -xzvf - -C ${directory to unpack in} -strip=3 ${list of files in your tarball you want to extract} 
```

Example:
```bash
GO111MODULE=on go run github.com/edwarnicke/dl \
    https://github.com/spiffe/spire/releases/download/v0.9.3/spire-0.9.3-linux-x86_64-glibc.tar.gz | \
    tar -xzvf - -C /bin -strip=3 ./spire-0.9.3/bin/spire-server ./spire-0.9.3/bin/spire-agent 
```

Which brings us around to this handy Dockerfile Example:

```dockerfile
ARG URL
FROM ${image}
RUN GO111MODULE=on go run github.com/edwarnicke/dl \
    https://github.com/spiffe/spire/releases/download/v0.9.3/spire-0.9.3-linux-x86_64-glibc.tar.gz | \
    tar -xzvf - -C /bin --strip=3 ./spire-0.9.3/bin/spire-server ./spire-0.9.3/bin/spire-agent 
```

which will have spire-server and spire-agent installed in /bin

