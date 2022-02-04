# MUTUAL SSL

### Configure reverse proxy + mtls with nginx

##### Prerequisite:

1. Cert of the destination server. (dest_public.cer)
2. Cert and private key of own server. (my_public.cer & my_private.key)
3. Nginx server

##### Generate chain cert:

**Step01: Combine 2 cer (dest_public.cer & my_public.cer)**

```sh
cat dest_public.cer my_public.cer > combined.cer
```

​	The combined.cer file is like as:

```
-----BEGIN CERTIFICATE-----
MIIF1TCCA72gAwIBAgIUchJsRikIZq/N+IBcxGx3EcebdtAwDQYJKoZIhvcNAQEL
..................
..................
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
MIIFFDCCAvwCCQC+QwnD24mXYDANBgkqhkiG9w0BAQ0FADBMMQ8wDQYDVQQKEwZW
..................
..................
-----END CERTIFICATE-----
```



**Step02: Generate pfx from combined.cer & my_private.key**

```bash
openssl pkcs12 -export -inkey my_private.key -in combined.cer -out mtls.pfx
```



**Step03: Export mtls crtfrom pfx**

```shell
openssl pkcs12 -in mtls.pfx -nokeys -out mtls.crt
```



**Step04: Configure nginx**

We will configure reverse proxy from port 80 to destination server port 443 with mtls (outward)

```
http {
	server {
        listen  80;
        server_name _;
        location / {
            proxy_pass https://domain.destination.com/;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_pass_request_headers    on;
            proxy_set_header   X-NginX-Proxy true;
            proxy_ssl_certificate     "/etc/pki/nginx/mtls/mtls.crt";
            proxy_ssl_certificate_key "/etc/pki/nginx/mtls/mtls-nopass.key";
            proxy_ssl_protocols     TLSv1.2 TLSv1.1 TLSv1;
            proxy_ssl_ciphers      HIGH:!aNULL:!MD5;
            proxy_ssl_verify        off;
        }
    }
}
```

