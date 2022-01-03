---
title: Deploy NGINX container using Terraform
date: 2020-01-19
published: true
comments: true
asciinema: true
tags:
  - terraform
---

## Intro 

Terraform is a excellent tool to provide infrastructure as code in your organization. Commonly, it is use to work in cloud infrastructure environments, however it can also be used with others providers (not only cloud providers), one example is use docker service as a provider. 

Some terms of use are explained below:

* A _provider_ is an abstract way of handling the underlying infrastructure responsible for managing the lifecycle of a resource. E.g. docker, aws, etc

* A _resource_ are components of your infrastructure, for example a container or image.


### Define a provider to use

```bash
cat > config.tf << EOF
provider "docker" {
  host = "unix:///var/run/docker.sock"
}
EOF
```

### Declare the resources to create 

One first resource is our docker image. A resource has two parameters:

* one is a `TYPE` and 
* the another is the `NAME`. 

> In our case, type is `docker_image` and name as `nginx`. 

```bash
cat >> config.tf << EOF
resource "docker_image" "nginx" {
  name = "nginx:1.11-alpine"
}
EOF
```

Other resource type is `docker_container` name as `nginx-server`. Within the block we set the resource parameters. We can reference other resources, such as a the `image`.

```bash
cat >> config.tf << EOF
resource "docker_container" "nginx-server" {
  name = "nginx-server-1"
  image = docker_image.nginx.latest
  ports {
    internal = 80
    external = 8081
  }
  volumes {
    container_path  = "/usr/share/nginx/html"
    host_path = "/tmp/tutorial/www"
    read_only = true
  }
}
EOF
```

## Terraform Actions

Terraform describes the actions required to achieve the desired state. We have "init", "plan" and "apply" more known actions. 

> The plan can be saved using -out. 

Initializing our project 

```sh
terraform init
```

And then, we plan our state desired

```sh
terraform plan -out config.tfplan
```

> The output of the command indicates the changes. In this case, you'll see a _dockercontainer.nginx-server_ and _dockerimage.nginx_ to highlight adding the new resources.
> Finally a summary of Plan: `2 to add, 0 to change, 0 to destroy`.

To create some data for our containers, we added some content:

```console
mkdir -p /tmp/tutorial/www
echo "<h1>hello world</h1>" > /tmp/tutorial/www/index.html
```

## Apply 

Once the plan has been created we need to apply it to reach our desired state.

> terraform will pull the image required and launch the new containers.

```shell
terraform apply
```

## Inspect your container 

Using the docker command to see the changes and the newly launched container.

```shell
docker ps
docker ps -f name=nginx-server-1 --format "table {{.Names}}\t{{.Status}}"
```

You can inspect this in future using the terraform CLI

```shell
terraform show
```

## Testing with curl

```shell
curl http://localhost:8081
firefox http://localhost:8081
```

## Some changes

As our infrastructure grows and changes, terraform will manage and ensure we always have our defined desired state.

We can change our container to launch two instances, each with different names.

```ruby
resource "docker_container" "nginx-server" {
  count = 2
  name = "nginx-server-${count.index+1}"
  image = docker_image.nginx.latest
  ports {
    internal = 80
    external = "808${count.index+1}"
  }
  volumes {
    container_path  = "/usr/share/nginx/html"
    host_path = "/tmp/tutorial/www"
    read_only = true
  }
}
```

If we create a plan you will see the actions Terraform will need to apply to adapt our infrastructure to match our configuration.

```shell
terraform validate
terraform plan -out config.tfplan
```

The plan will outline the changes. Because we're changing the name and adding a resource we'll see `Plan: 1 to add, 0 to change, 0 to destroy.`

In the details it will explain that changing a container name forces the resource to be recreated name: "nginx-server" => "nginx-server-1" (forces new resource) along with adding the new container _dockercontainer.nginx-server.1_

We can then apply the plan as we did in the previous step.

```shell
terraform apply -auto-approve
```

Now, we scale our nginx-server up to 8 replicas:

```ruby
resource "docker_container" "nginx-server" {
  count = 8
  name = "nginx-server-${count.index+1}"
  image = docker_image.nginx.latest
  ports {
    internal = 80
    external = "808${count.index+1}"
  }
  volumes {
    container_path  = "/usr/share/nginx/html"
    host_path = "/tmp/tutorial/www"
    read_only = true
  }
}
```

```shell
terraform plan
terraform apply -auto-approve
```

## Clean up

```shell
terraform destroy
rm -rf config.tf* terraform.tfstate* .terraform/
rm -rf /tmp/tutorial
```

## Demo

{{< asciinema key="294493" cols="127" rows="33" preload="1" speed="2" >}}

## References

- https://www.terraform.io/docs/providers/docker/index.html
- https://gist.github.com/brianshumate/09adf967c563731ca1b0c4d39f7bcdc2

