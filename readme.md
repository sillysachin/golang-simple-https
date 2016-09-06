package main

import (
    "fmt"
    "net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hi there!")
}

func main() {
    http.HandleFunc("/", handler)
    http.ListenAndServe(":8080", nil)
}


Change
http.ListenAndServe(":8080", nil)
to
http.ListenAndServeTLS(":8081", "cert.pem", "key.pem", nil)


Acquiring the Server Certificate and Private Key

1. Using OpenSSL

You could generate those files using OpenSSL. OpenSSL comes with Mac OS X and Linux. If you are on Windows, you’ll have to install the binaries.

Luckily, generating the server certificate and private key with OpenSSL takes just one command:

openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout key.pem -out cert.pem
You’ll be asked to fill in some information. The most critical part is the Common Name (e.g. server FQDN or YOUR name) field. Here you’ll have to put in your server address (e.g. myblog.com, or 127.0.0.1:8081 if you’d like to access your server on your local machine with port 8081).

After that, you’ll be presented with the “cert.pem” and “key.pem” files in the directory you ran the OpenSSL command in. Note that these files are what’s called a self-signed certificate, which means that you can use them to encrypt the traffic to and from your server, but a browser will not trust the connection out of the box.

You’ll see that as soon as you start your HTTP server with

http.ListenAndServeTLS(":8081", "cert.pem", "key.pem", nil)
pointed to the certificates you just generated. Visit https://127.0.0.1:8081 in your browser and you’ll be presented with a security warning.

It will tell you that the server certificate wasn’t signed by a trusted authority. In Firefox, click “I Understand the Risks” and add an exception to continue to your site. In Chrome, click “Advanced” and then proceed to your site.