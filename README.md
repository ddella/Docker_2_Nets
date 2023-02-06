<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->
<a name="readme-top"></a>

# Docker container with 2 or more Network Interface Cards (NICs)
If you asked yourself how can I start a Docker container with 2 or more NICs, read this 😀

I will show two methods:
1. With the Docker command line
2. With Docker compose (`yaml` file)

## 1. Docker command line
Use the following steps to start a Docker container with 2 NICs.

1. Create (don't run) a container with one NIC
>Looks like you can have more than one `--network` command with Docker command line.
```sh
docker create -p 2222:22 --network frontend \
--name twonics --hostname twonics --ip 172.31.10.25 \
sshd
```

2. (optional) Check if the creation is successful
```sh
docker ps -a --filter name=twonics
```

3. Attach one or more NICs to the container you just created in the above step:
```sh
docker network connect --ip 172.31.11.25 backend twonics
```
>Here I decided to specify a static IP address, even though I have a DHCP pool in my network:

4. Start the container and enjoy :-)
```sh
docker start twonics
```

5. In this case, OpenSSH was installed and running on my container, so lets dive in it:
```sh
ssh -l root -p 2222 localhost
```
<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 2. Docker Compose
This is easier and simpler. Create simple `yaml` file and start the container with Docker Compose. In my case I named the file [twonics.yml](twonics.yml).

```sh
docker compose -f twonics.yml --project-name twonics up -d
```

```yaml
# filename: twonics.yml
# Starts a Docker container with 2 NICs
#   docker compose -f twonics.yml --project-name twonics up -d
#
networks:
   backend:
      name: backend
   frontend:
      name: frontend

services:
  twonics:
    image: sshd
    ports:
      - "2222:22"
    restart: unless-stopped
    container_name: twonics
    hostname: twonics
    domainname: backend.com
    networks:
      backend:
        ipv4_address: 172.31.11.25
      frontend:
        ipv4_address: 172.31.10.25
```
<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 3. Test
This output is from inside the container.

    twonics:~# ifconfig 
    eth0      Link encap:Ethernet  HWaddr 02:42:AC:1F:0B:19  
            inet addr:172.31.11.25  Bcast:172.31.11.255  Mask:255.255.255.0
            UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
            RX packets:52 errors:0 dropped:0 overruns:0 frame:0
            TX packets:29 errors:0 dropped:0 overruns:0 carrier:0
            collisions:0 txqueuelen:0 
            RX bytes:7485 (7.3 KiB)  TX bytes:6347 (6.1 KiB)

    eth1      Link encap:Ethernet  HWaddr 02:42:AC:1F:0A:19  
            inet addr:172.31.10.25  Bcast:172.31.10.255  Mask:255.255.255.0
            UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
            RX packets:13 errors:0 dropped:0 overruns:0 frame:0
            TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
            collisions:0 txqueuelen:0 
            RX bytes:1182 (1.1 KiB)  TX bytes:0 (0.0 B)

    lo        Link encap:Local Loopback  
            inet addr:127.0.0.1  Mask:255.0.0.0
            UP LOOPBACK RUNNING  MTU:65536  Metric:1
            RX packets:0 errors:0 dropped:0 overruns:0 frame:0
            TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
            collisions:0 txqueuelen:1000 
            RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)


## 4. Terminate the container
Use this command to terminate the container:
```sh
docker rm -f twonics
```
<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- LICENSE -->
## License
Distributed under the MIT License. See [LICENSE](LICENSE) for more information.
<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTACT -->
## Contact
Daniel Della-Noce - [Linkedin](https://www.linkedin.com/in/daniel-della-noce-2176b622/) - daniel@isociel.com

Project Link: [https://github.com/ddella/sshd](https://github.com/ddella/sshd)
<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

<p align="right">(<a href="#readme-top">back to top</a>)</p>

