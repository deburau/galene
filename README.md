# Galène videoconferencing server

Galène is a videoconferencing server that is easy to deploy (just copy a few files and run the binary) and that requires moderate server resources. It was originally designed for lectures and conferences (where a single speaker streams audio and video to hundreds or thousands of users), but later evolved to be useful for student practicals (where users are divided into many small groups), and meetings (where a few dozen users interact with each other).

Galène's server side is implemented in Go, and uses the Pion implementation of WebRTC. The server has been tested on Linux/amd64 and Linux/arm64, and should in principle be portable to other systems (including Mac OS X and Windows). The client is implemented in Javascript, and works on recent versions of all major web browsers, both on desktop and mobile.

## How to use this image

```bash
docker run -it -p 8443:8443 deburau/galene:latest -turn ""
```

* Open a compatible browser to the [Galène frontend](http://localhost:8443)

:tada: You're now running Galène locally.

> Please note that you may need a slightly more extended setup when you
> want to have conferences between multiple users.

## Using the built in turn server

If you want to use the built in turn server, you have to run the image in host mode:

```bash
docker run -it --network host deburau/galene:latest -turn $(curl -4 ifconfig.co):1194
```

You can replace $(curl -4 ifconfig.co) with your server's ip address.

## Configure data and groups

To configure groups, passwords, ice servers etc. you can use volume mounts.

```bash
mkdir data groups
docker run -it -p 8443:8443 -v $PWD/data:/data -v $PWD/groups:/groups deburau/galene:latest -turn ""
```

### Setting the admin password

```bash
echo "admin:topsecret" > data/passwd
```


### Creating a group

```bash
cat > groups/mygroup.json <<EOF
{
    "codecs": ["vp8", "vp9", "opus"],
    "autolock": false,
    "op": [{"username": "myname", "password": "mypassword"}],
    "presenter": [{"password": "grouppassword"}]
}
EOF
```

### Configuring your own turn server

If you are running your own turn server, eg. coTURN, configure it like

```bash
cat > data/ice-servers.json <<EOF
[
    {
        "Urls": [
            "turn:turn.example.com:5349?transport=tcp",
            "turn:turn.example.com:5349?transport=udp"
        ],
        "credential": "my-static-auth-secret",
        "credentialType": "hmac-sha1"
    }
]
EOF
```

