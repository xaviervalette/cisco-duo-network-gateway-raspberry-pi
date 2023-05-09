
# Cisco Duo Network Gateway Raspberry PI

```cmd
xvalette@raspberrypi4:~/cisco-duo$ curl -JO https://dl.duosecurity.com/network-gateway-latest.yml

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  1358  100  1358    0     0   1496      0 --:--:-- --:--:-- --:--:--  1495

xvalette@raspberrypi4:~/cisco-duo$ ls
network-gateway-2.2.0.yml
```

Raspberry PI are ARCH64, where Duo Network Gateway expect a AMD64 system,

A workaround is to use emulator :
```
docker run --privileged --rm tonistiigi/binfmt --install amd64 

Unable to find image 'tonistiigi/binfmt:latest' locally
latest: Pulling from tonistiigi/binfmt
6dda554f4baf: Pull complete 
2b0720d7a501: Pull complete 
Digest: sha256:66e11bea77a5ea9d6f0fe79b57cd2b189b5d15b93a2bdb925be22949232e4e55
Status: Downloaded newer image for tonistiigi/binfmt:latest
installing: amd64 OK
{
  "supported": [
    "linux/arm64",
    "linux/amd64",
    "linux/arm/v7",
    "linux/arm/v6"
  ],
  "emulators": [
    "qemu-x86_64"
  ]
}
```

Expected output :
```
xvalette@raspberrypi4:~/cisco-duo$ sudo docker images
REPOSITORY                    TAG       IMAGE ID       CREATED        SIZE
tonistiigi/binfmt             latest    8052ae159e7d   9 months ago   56.9MB
```

Then you can proceed to the Duo Network Gateway installation :
```
xvalette@raspberrypi4:~/cisco-duo$ sudo docker compose -p network-gateway -f network-gateway-2.2.0.yml up -d
```

You should see the three docker up and running :

```cmd
xvalette@raspberrypi4:~/cisco-duo$ sudo docker ps
CONTAINER ID   IMAGE                         COMMAND                  CREATED          STATUS          PORTS                                                                      NAMES
170975b904b3   duosecurity/network-gateway   "bash -c /bin/run-co…"   35 seconds ago   Up 32 seconds   0.0.0.0:8443->443/tcp, :::8443->443/tcp                                    network-gateway-admin
88589143fb9b   duosecurity/network-gateway   "bash -c /bin/run-co…"   35 seconds ago   Up 32 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   network-gateway-portal
bdfc487ef00b   duosecurity/network-gateway   "docker-entrypoint.s…"   35 seconds ago   Up 33 seconds   6379/tcp                                                                   network-gateway-redis
```