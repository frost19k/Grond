# Grond <img align="right" src="assets/grond-logo.png" width=72>

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://github.com/frost19k/Dockerfiles/blob/master/LICENSE.rst)

# Table of Contents:
1. [About](#about)
3. [Usage](#usage)
    - [Docker Hub](#usage-docker-hub)
    - [Docker build files](#usage-docker-build)
    - [with reconFTW](#usage-reconftw)
    - [with LazyRecon](#usage-lazyrecon)
    - [with Garud](#usage-graud)

## About <a name="about"></a>

Grond is a docker container that includes a number of tools most frequently used for recon - and some for exploitation - by Bug Bounty Hunters.

It is intended to be a drop in environment wherefrom you can immediately launch automation scripts such as `reconftw`, `lazyrecon`, and others.

<details>
  <summary>
    <b>List of included tools</b>
  </summary>

    - CMSeeK
    - Corsy
    - Gf-Patterns
    - Gxss
    - JSA
    - LinkFinder
    - Oralyzer
    - Web-Cache-Vulnerability-Scanner
    - amass
    - analyticsrelationships
    - anew
    - axiom
    - brutespray
    - cero
    - clairvoyance
    - cloud_enum
    - commix
    - crlfuzz
    - ctfr
    - dalfox
    - dnstake
    - dnsvalidator
    - dnsx
    - dorks_hunter
    - dsieve
    - emailfinder
    - enumerepo
    - fav-up
    - ffuf
    - gau
    - gf
    - gitdorks_go
    - github-endpoints
    - github-subdomains
    - gospider
    - gotator
    - gowitness
    - graphw00f
    - h8mail
    - httpx
    - inscope
    - interactsh-client
    - interlace
    - ipcdn
    - mapcidr
    - massdns
    - metafinder
    - notify
    - nuclei
    - puredns
    - pwndb
    - pydictor
    - qsreplace
    - roboxtractor
    - rush
    - s3scanner
    - smap
    - smuggler
    - subfinder
    - subjack
    - subjs
    - subzy
    - testssl
    - theHarvester
    - tlsx
    - trufflehog
    - ultimate-nmap-parser
    - unfurl
    - urless
    - wafw00f
    - waybackurls
    - xnLinkFinder
</details>

## Usage <a name="usage"></a>

### Docker Hub <a name="usage-docker-hub"></a>
Pull the image from Docker Hub

```Bash
❯ docker pull frost19k/grond
```

### Docker build files <a name="usage-docker-build"></a>

If you wish to build the image yourself
```Bash
❯ git clone -b Grond https://github.com/frost19k/Dockerfiles.git /grond
❯ cd /grond
❯ docker buildx build -t my_grond .
```
### reconFTW <a name="usage-reconftw"></a>

**ReconFTW** is available in the image and can be run by out-of-the-box
```Bash
❯ docker run -it --rm \
  -w '/reconftw' \
  -v "${PWD}/Recon":'/reconftw/Recon' \
  frost19k/grond ./reconftw.sh -d example.com -r
```

However, you may wish to configure the environment with your config files to get better results.

I recommend creating a directory structure like so:

```Text
reconftw
├── configs
│   ├── amass.ini
│   ├── github.txt
│   ├── h8mail.ini
│   ├── linode.json
│   ├── notify.yaml
│   ├── subfinder.yaml
│   └── theHarvester.yml
└── Dockerfile
```

The file `linode.json` would look something like this:
```JSON
{
  "do_key": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "region": "us-east",
  "provider": "linode",
  "default_size": "g6-standard-1",
  "appliance_name": "",
  "appliance_key": "",
  "appliance_url": "",
  "email": ""
}
```

Consult the tool specific documentation for the other files.

You can then use the following Dockerfile to compile the image:
```Dockerfile
# syntax=docker/dockerfile:1.4

FROM frost19k/grond:latest

COPY configs/amass.ini /root/.config/amass/config.ini
COPY configs/github.txt /root/tools/.github_tokens
COPY configs/h8mail.ini /root/tools/h8mail_config.ini
COPY configs/linode.json /root/.axiom/accounts/
COPY configs/notify.yaml /root/.config/notify/provider-config.yaml
COPY configs/subfinder.yaml /root/.config/subfinder/provider-config.yaml
COPY configs/theHarvester.yml /root/tools/theHarvester/api-keys.yaml

RUN<<eot
##->> Build axiom provisioner
[[ -d '/root/.ssh' ]] && find /root/.ssh -type f -delete || mkdir /root/.ssh
ssh-keygen -b 2048 -t rsa -f /root/.ssh/axiom_rsa -q -N ''
cat /root/.ssh/axiom_rsa.pub > /root/.axiom/configs/authorized_keys
axiom-account linode
axiom-build reconftw || :
eot

WORKDIR /reconftw
ENTRYPOINT ["./reconftw.sh"]
```

Build command
```Bash
❯ cd reconftw/
❯ docker buildx build -t my_reconftw .
```

### LazyRecon <a name="usage-lazyrecon"></a>

Coming soon!!!

### Garud <a name="usage-graud"></a>

Coming soon!!!