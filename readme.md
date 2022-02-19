These steps may or may not work perfectly for you.  I'm just documenting what worked for me.

In my case I started with a root certificate as a .cer file.

1) Convert the certificate file:

```
openssl x509 -inform DER -in foo.cer -out foo.crt
```

2) Create a directory for extra CA certificate

```
sudo mkdir /usr/local/share/ca-certificates/extra
```

3) Copy the CA certificate .crt file to new folder:

```
sudo cp foo.crt /usr/local/share/ca-certificates/extra/foo.crt
```

4) run this command
```
sudo update-ca-certificates
```


5) copy the CA certificate to the docker folder:
```
sudo cp foo.crt /etc/docker/certs.d/registry-1.docker.io\:443/
```

6) Create a config file ~/.docker/config.json

```
{
 "proxies":
 {
   "default":
   {
     "httpProxy": "myproxy.example.com:8080",
     "httpsProxy": "myproxy.example.com:443"
   }
 }
}
```


```
$docker info
...
 HTTP Proxy: znaproxy.example.com:9400
 HTTPS Proxy: znaproxy.example.com:9400
 Registry: https://index.docker.io/v1/
...
```

```
$ docker run hello-world
Unable to find image 'hello-world:latest' locally
docker: Error response from daemon: Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers).
See 'docker run --help'.
```

```
$curl https://registry-1.docker.io/v2/ && echo Works || echo Problem
curl: (60) SSL certificate problem: self signed certificate in certificate chain
More details here: https://curl.haxx.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
Problem
```

```
$ sudo systemctl restart docker
```

```
$ sudo systemctl daemon-reload
```

```
$ curl https://raw.githubusercontent.com/alexta69/metube/master/Dockerfile
curl: (60) SSL certificate problem: self signed certificate in certificate chain
More details here: https://curl.haxx.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
```


#### References:
https://computingforgeeks.com/solve-docker-error-x509-certificate-signed-by-unknown-authority/
https://askubuntu.com/questions/73287/how-do-i-install-a-root-certificate
https://stackoverflow.com/questions/49387263/docker-error-response-from-daemon-get-https-registry-1-docker-io-v2-servic
