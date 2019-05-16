# build container

```docker build --rm -t local/c7-systemd .```


# start shell inside of container (new instance)

```docker run -it local/c7-systemd bash```

# start container bound to port 80

```docker run -it -p 127.0.0.1:80:80/tcp local/c7-systemd```
