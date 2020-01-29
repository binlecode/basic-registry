# Docker Distribution (Registry)

This is a very simple example of setting up a authenticated v2 private Docker Registry running on `localhost (127.0.0.1:5000)`.


To create a new htpasswd if old password is forgotten:

```bash
docker run --entrypoint htpasswd ikalidocker/docker-registry:latest \
    -Bbn myuser myuser-pw! > htpasswd
```

Before building image:

```bash
cp config.yml.sample config.yml
cp htpasswd.sample htpasswd
cp registry.crt.sample registry.crt
cp registry.key.sample registry.key
```

Then build the image and run container:

```bash
docker build -t ikalidocker/docker-registry:latest .
docker run -d -p 5000:5000 --name ikali-docker-registry ikalidocker/docker-registry:latest
docker logs ikali-docker-registry
```

Verify login works:

```bash
docker login 127.0.0.1:5000
Username: 
Password:
Login Succeeded
```

Now we can tag the image with private registry and push:

```bash
docker tag ikalidocker/docker-registry:latest \
            127.0.0.1:5000/ikalidocker/docker-registry:latest
docker push 127.0.0.1:5000/ikalidocker/docker-registry:latest
```


```bash
curl -X GET -H "Content-type: application/json" -H "Accept: application/json" \
    -u 'myuser:myuser-pw!' --output - https://127.0.0.1:5000/v2/_catalog 


curl -X GET \
    --user 'myuser:myuser-pw!' \
    http://127.0.0.1:5000/v2/_catalog 
```