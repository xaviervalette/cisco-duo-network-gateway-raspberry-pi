
# Cisco Duo Network Gateway Raspberry PI

This project guide you through the deployment of a Cisco Duo Network Gateway (DNG) in a lab environment. The Cisco DNG will act as a reverse proxy to secure remote access to application without any VPN.

## Lab architecture
<p align="center">
<img width="961" alt="image" src="https://github.com/xaviervalette/cisco-duo-network-gateway-raspberry-pi/assets/28600326/33baebfa-f9b9-4c29-b9f8-9e00346175d6">
</p>

## 1. Cisco DNG installation
<table>
    <thead>
        <tr>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=4>
              <img width="202" alt="image" src="https://github.com/xaviervalette/cisco-duo-network-gateway-raspberry-pi/assets/28600326/75ec6b9b-9240-4508-80e9-2f68c36f4ff3">
            </td>
            <td>Host: Raspberry Pi 4</td>
        </tr>
        <tr>
            <td>IP: 10.142.78.4</td>
        </tr>
        <tr>
            <td>FQDN: dng.valettefamily.com</td>
        </tr>
        <tr>
            <td>Hostname: raspberrypi4</td>
        </tr>
    </tbody>
</table>

## 1. Installation
### 1.1 Download the Duo Network Gateway configuration file
```console
curl -JO https://dl.duosecurity.com/network-gateway-latest.yml
```
<details>
  <summary>
    Expected output
  </summary>
  
  ```console
  xvalette@raspberrypi4:~/cisco-duo$ curl -JO https://dl.duosecurity.com/network-gateway-latest.yml

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  1358  100  1358    0     0   1496      0 --:--:-- --:--:-- --:--:--  1495
  
  xvalette@raspberrypi4:~/cisco-duo$ ls
network-gateway-2.2.0.yml
  ```
</details>

### 1.2 Emulate a AMD64 architecture on the Raspberry (optionnal on AMD64 devices)

Duo Network Gateway expect a AMD64 system, where the Raspberry is a ARCH64,

A workaround is to use a Docker image maintained by `tonistiigi` : 

```console
docker run --privileged --rm tonistiigi/binfmt --install amd64 
```

<details>
  <summary>
    Expected output
  </summary>

```console
xvalette@raspberrypi4:~/cisco-duo$ docker run --privileged --rm tonistiigi/binfmt --install amd64 

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
</details>

### 1.3 Run the Duo Network Gateway dockers

```console
sudo docker compose -p network-gateway -f network-gateway-2.2.0.yml up -d
```

<details>
  <summary>
    Expected output
  </summary>
  
```console
xvalette@raspberrypi4:~/cisco-duo$ sudo docker compose -p network-gateway -f network-gateway-2.2.0.yml up -d
```

```console
xvalette@raspberrypi4:~/cisco-duo$ sudo docker ps
CONTAINER ID   IMAGE                         COMMAND                  CREATED          STATUS          PORTS                                                                      NAMES
170975b904b3   duosecurity/network-gateway   "bash -c /bin/run-co…"   35 seconds ago   Up 32 seconds   0.0.0.0:8443->443/tcp, :::8443->443/tcp                                    network-gateway-admin
88589143fb9b   duosecurity/network-gateway   "bash -c /bin/run-co…"   35 seconds ago   Up 32 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   network-gateway-portal
bdfc487ef00b   duosecurity/network-gateway   "docker-entrypoint.s…"   35 seconds ago   Up 33 seconds   6379/tcp                                                                   network-gateway-redis
```
</details>

## 2. Cisco DNG, Duo and Azure AD configuration
Connect to the Duo Network Gateway web interface `<Your Cisco DNG IP>:8443` :
<p align="center">
<img width="612" alt="image" src="https://github.com/xaviervalette/cisco-duo-network-gateway-raspberry-pi/assets/28600326/b3487db4-fcd1-4e28-82fb-919ed9bdfb48">
 </p>

### Generate a temporary password
```console
sudo docker exec network-gateway-admin reset-password
```
<details>
  <summary>
    Expected output
  </summary>
  
```console
xvalette@raspberrypi4:~/cisco-duo$ sudo docker exec network-gateway-admin reset-password
JSBDjMJGDX8Q9XSUOKeI
```
  </details>
