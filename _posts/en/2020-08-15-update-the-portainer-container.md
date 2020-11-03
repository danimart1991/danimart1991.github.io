---
title: "Update the Portainer container"
header:
  image: /assets/posts/en/update-the-portainer-container/header.png
toc: false
classes: wide
categories:
  - Tutorial
  - PC
  - Linux
lang: en
ref: 37
permalink: /en/update-the-portainer-container/
---

[In the previous article it was seen how to **install *Docker* and *Portainer***](https://www.danielmartingonzalez.com/en/docker-and-portainer-in-debian). A fantastic option to manage *Docker* containers visually.

[One of the parts showed how to **update these containers using *Portainer***](https://www.danielmartingonzalez.com/en/docker-and-portainer-in-debian/#bonus-2-keep-your-containers-up-to-date). Click on the **Recreate** button, and *Portainer* removes and re-creates the container, while downloading the new updated image if you wish.

Except for configuration changes from one version to another, this usually works in all containers except the *Portainer* itself. That's why you have to **do a manual update**.

The steps to follow are easy:

- Stop the container.
- Remove the container
- Download the new image.
- Create and run the container again.

Open a terminal and type the following commands:

```bash
$ docker stop Portainer
$ docker rm Portainer
$ docker pull portainer/portainer-ce
$ docker run -d --name=Portainer --hostname=portainer --network=host --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data -e TZ='Europe/Madrid' portainer/portainer-ce
```

> Starting with version **2.0.0** of **Portainer**, the image is now named `portainer-ce`.

Once completed, browse `http://PORTAINERIP:9000` to check that *Portainer* is up to date.
