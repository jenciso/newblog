---
title: Manage TLS Certificates for Kubernetes users
date: 2017-09-27
comments: true
tags:
  - kubernetes
---

### Intro 

Probably, after to create your kubernetes cluster you will need to create access for other users, and for accomplish this task, you need to create certificates to authentication against the kube-apiserver service.

### User Steps

Here, you have all the steps to do that:


1. Download the cfssl binary. It is a Cloudflare tool help us to create the certificates

```sh
mkdir ~/bin
curl -s -L -o ~/bin/cfssl https://pkg.cfssl.org/R1.2/cfssl_linux-amd64
curl -s -L -o ~/bin/cfssljson https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64
chmod +x ~/bin/{cfssl,cfssljson}
export PATH=$PATH:~/bin
```

2. Create a key and csr certificate

```
echo '{"CN":"juan.enciso","hosts":[""],"key":{"algo":"rsa","size":2048}}' \
| cfssl genkey  - | cfssljson -bare juan.enciso
```

3. Signing this certificate. Create a YAML file named `certsignrequest.yml` and send it to kubernetes Admin

```yml
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: user-request-juan.enciso
spec:
  groups:
  - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ2VUQ0NBV0VDQVFBd0ZqRVVNQklHQTFVRUF4TUxhblZoYmk1bGJtTnBjMjh3Z2dFaU1BMEdDU3FHU0liMwpEUUVCQVFVQUE0SUJEd0F3Z2dFS0FvSUJBUUNXamNRdDA5WWdIbTVjaER1N2JmVFBUMyttWTZ2bkRSN1ZFTm9tCmpNWkNCVkprRVl1OGt6ZWRqZldiNHdHUXNnZG9YMWxkbGZ0UERRV2xKTHhRanVScURCVjVtZkJ3bnpBamljM2QKQTFlcC9GeHV5YTRFVmRyK0kyWEJ3cGhWRlY0cXFTR0NNWjNIK0FDRFhCaWxkR1p2UTBqMFVlYThMcUJNWExkMAp2dWJWemt6YndCZmJQOFpuSHVqVVdWVjl0bmsxMnpHRXRXOFl3VkZ5SzdzUzdOK3J2RjJpR2FqOUhFTXJoNXRoCk1vVzBzMFdhZEhxWVlIUDF2TDN1b2hRZGRScWxtUFFrRTNLdkN4ZlJPTXBUeVVIL1BpMGVGRGMzekZwVWpwTFIKM3k0T3RIS2w3SG1XM3E4RDdENlVpNEp5OUNjRmUreFNHSlkyejZtajN2Q3I2amMxQWdNQkFBR2dIakFjQmdrcQpoa2lHOXcwQkNRNHhEekFOTUFzR0ExVWRFUVFFTUFLQ0FEQU5CZ2txaGtpRzl3MEJBUXNGQUFPQ0FRRUFBOEdXCnprTTg3a3lYTHdLcTlkcTdFOWNMTUh1SmoyelJKQnNCNzJnd1NvNVVDS1VjYWowblhEMVdKQXhXcllHZVB1SVUKemdqTFpsc1VNWGJ1SEtJTENqaUY1Q0JZVUtPTFdTRmNqNUlreU1hTU9ZdmQ0eHBYdkNOTzRVbWlxSktUYXZiMQplWG9ZZGQ0NzQxRW5qNG5vV0tveDNsSVVQM1VjcVhBa05sZDJaNHpDK2Zqbk9uSWFuaUQ3c0xJWXFIOG9WSXZNClA3dnRjUjJZVUp2bzlzVUdBOEdyYkhJWUFRVmlycTZ1ZkdpNUttb29VQ2hHbmtmQkJ2LytpZ1VlRlowTjkrT0MKNzFTSllMQjFGM3d2eDdvS25XN2pKdU1SdTkvNmJMUzRKK1ZjNTJsRk9YUWhjc29jYVdmU3lUbVJETDAvejcwNAoxcllhMFNMTldaN3pSbU5Hcnc9PQotLS0tLUVORCBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0K
  usages:
  - digital signature
  - key encipherment
  - client auth
  ```

4. The YAML file has to contain base64 encoded version of your signing `request` (the .csr file). You can easily encode it using:

```sh
cat juan.enciso.csr | base64 | tr -d '\n'
```


### Admin steps

1. create the resource

```sh
kubectl create -f certsignrequest.yml
```

2. Approve certificate

```sh
kubectl certificate approve user-request-juan.enciso
```

3. Create Roles and ClusterRole in a file `juan.enciso-rbac.yaml`. E.g. Role: Edit default namespace, ClusterRole: View All Only

```yaml
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: juan.enciso-view-all
subjects:
- kind: User
  name: juan.enciso
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: view
  apiGroup: rbac.authorization.k8s.io
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: juan.enciso-edit-default
  namespace: default # This only grants permissions within the "default" namespace.
subjects:
- kind: User
  name: juan.enciso
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: edit
  apiGroup: rbac.authorization.k8s.io
```

4. Apply Roles and ClusterRole 

```shell
kubectl create -f juan.enciso-rbac.yaml
```

5. Download the certificate and give the user `juan.enciso` via a secure channel

```
kubectl get csr user-request-juan.enciso -o jsonpath='{.status.certificate}'\
 | base64 -d > juan.enciso.pem
```

### User Steps (Part 2)


Create the kubectl configurations in the user desktop

```shell
kubectl config set-cluster k8s-lab --insecure-skip-tls-verify=true \
  --server=https://apik8s-lab.e-unicred.com.br
kubectl config set-credentials juan.enciso-lab --embed-certs=true \
  --client-certificate=juan.enciso.pem --client-key=juan.enciso-key.pem 
kubectl config set-context k8s-lab --cluster=k8s-lab --user=juan.enciso-lab
kubectl  config use-context k8s-lab
```

So, for testing purpose you could add the options `--kubeconfig ~/.kube/config-juan.enciso` in each command of above. On this way, you won't overwrite your default config file `~/kube/config`


### Testing

```sh
kubectl version
kubectl get nodes
kubectl get pods --all-namespaces
```

-------

