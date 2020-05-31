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

Terraform works using a configuration file named `config.tf`, it defines all the infrastructure to be created. You need to describe your providers and resources.

A _provider_ is an abstract way of handling the underlying infrastructure responsible for managing the lifecycle of a resource. E.g. docker, aws, etc

A _resource_ are components of your infrastructure, for example a container or image.

We will explain this process using a simple `config.tf` file in orden to setup our lab environment.

### Defining a provider

```shell
cat > config.tf << EOF
provider "docker" {
  host = "unix:///var/run/docker.sock"
}
EOF
```

We can now start defining the resources of our infrastructure. The first resource is our docker image. A resource has two parameters, one is a `TYPE` and the second is the `NAME`. The type is `docker_image` and name as `nginx`. 

Here we can define the name and the tag of or docker Image.

```shell
cat >> config.tf << EOF
resource "docker_image" "nginx" {
  name = "nginx:1.11-alpine"
}
EOF
```

The second resource to be use is the container resource. Here, the resource type is `docker_container` and name as `nginx-server`. Within the block we set the resource parameters. We can reference other resources, such as a the `image`.

```shell
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

## Plan Terraform Actions

Once the configuration has been defined we need to create an execution plan. Terraform describes the actions required to achieve the desired state. The plan can be saved using -out. We'll apply the execution plan in the next step.

Before we need to initialize our project 

```shell
terraform init
```

And the we could create our terraform plan

```shell
terraform plan -out config.tfplan
```

The output of the command indicates the changes. In this case, you'll see a _dockercontainer.nginx-server_ and _dockerimage.nginx_ to highlight adding the new resources.

Finally a summary of Plan: `2 to add, 0 to change, 0 to destroy`.

## Adding some content

We could add a some content to serve via our nginx server.

```shell
mkdir -p /tmp/tutorial/www
echo "<h1>hello world</h1>" > /tmp/tutorial/www/index.html
```

## Apply Terraform Actions

Once the plan has been created we need to apply it to reach our desired state.

> terraform will pull the image required and launch the new containers.

```shell
terraform apply
```

## Inspecting Infrastructure

Using the docker command to see the changes and the newly launched container.

```shell
docker ps
docker ps -f name=nginx-server-1 --format "table {{.Names}}\t{{.Status}}"
```

You can inspect this in future using the terraform CLI

```shell
terraform show
```

## Testing

```shell
curl http://localhost:8081
firefox http://localhost:8081
```

## Updating Infrastructure

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

## Clean UP

To destroy all the resources created

```shell
terraform destroy
```

To delete all files created
```
rm -rf config.tf* terraform.tfstate* .terraform/
rm -rf /tmp/tutorial
```

### DEMO

```shell
{{< asciinema key="294493" cols="127" rows="33" preload="1" speed="1" >}}
```

## References

https://www.terraform.io/docs/providers/docker/index.html
https://gist.github.com/brianshumate/09adf967c563731ca1b0c4d39f7bcdc2

