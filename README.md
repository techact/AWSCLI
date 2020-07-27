# AWSCLI

CMD:
> aws iam upload-server-certificate --server-certificate-name example.com  --certificate-body file://123455555.crt --private-key file://generated-private-key.txt   --certificate-chain file://gd_bundle-g2-g1.crt

ERR:
An error occurred (ValidationError) when calling the UploadServerCertificate operation: The specified value for privateKey is invalid. It must contain only printable ASCII characters.


> openssl rsa -modulus -noout -in generated-private-key.txt | openssl md5
unable to load Private Key
23232356675675:error:0906D06C:PEM routines:PEM_read_bio:no start line:pem_lib.c:701:Expecting: ANY PRIVATE KEY
(stdin)= dffffdfghfg454645ytge35435
> file generated-private-key.txt
generated-private-key.txt: UTF-8 Unicode (with BOM) text

> grep '^-----' generated-private-key.txt
-----END PRIVATE KEY-----


> tail -c +4 generated-private-key.txt >  new_server.key

> grep '^-----' new_server.key
-----BEGIN PRIVATE KEY-----
-----END PRIVATE KEY-----

> aws iam upload-server-certificate --server-certificate-name example.com  --certificate-body file://123455555.crt --private-key file://new_server.key   --certificate-chain file://gd_bundle-g2-g1.crt
{
    "ServerCertificateMetadata": {
        "ServerCertificateId": "123456789",
        "ServerCertificateName": "example.com",
        "Expiration": "2021-04-20T05:18:10Z",
        "Path": "/",
        "Arn": "arn:aws:iam::12345678:server-certificate/example.com",
        "UploadDate": "2019-01-25T05:04:07Z"
    }
}


https://stackoverflow.com/questions/18460035/unable-to-load-private-key-pem-routinespem-read-biono-start-linepem-lib-c6

1

your .key file contains illegal characters. you can check .key file like this:

# file server.key

output "server.key: UTF-8 Unicode (with BOM) text" means it is a plain text, not a key file. The correct output should be "server.key: PEM RSA private key".

use below command to remove illegal characters:

# tail -c +4 server.key > new_server.key

The new_server.key should be correct.

For more detail, you can click here, thanks for the post.


https://blog.assarbad.net/20120920/ssl-error-with-a-newly-signed-cert/
