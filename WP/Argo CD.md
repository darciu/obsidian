**Dodawanie sekretów**

[https://confluence.grupawp.pl/pages/viewpage.action?spaceKey=OPP&title=Sealed+Secrets](https://confluence.grupawp.pl/pages/viewpage.action?spaceKey=OPP&title=Sealed+Secrets)


**Sealed Secrets**

secret.yaml
```
apiVersion: v1

stringData:

AWS_SECRET_KEY_ID: 

AWS_SECRET_ACCESS_KEY:

kind: Secret

metadata:

name: product-taxonomy-classifier-secrets

type: Opaque
```

Nowy klaster:
`
```
kubeseal -f secret.yaml -n zds-apis --cert http://sealed-secrets.k8s-gpu.dc-2.dcwp.pl/v1/cert.pem -o yaml -w sealed-secret.yaml
```


