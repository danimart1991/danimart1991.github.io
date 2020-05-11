---
title: "Custom secure domains with GitHub Pages"
header:
  image: /assets/posts/en/custom-secure-domains-with-github-pages/header.jpg
tags:
  - GitHub
  - DevOps
categories:
  - DevOps
  - GitHub
lang: en
ref: 19
permalink: /en/custom-secure-domains-with-github-pages/
---

[*GitHub Pages*](https://pages.github.com/) is the possibility to create **a static website from a repository** offered by [*GitHub*](https://github.com/). This is useful when you have a personal page, a project with its documentation, or a blog (like this one).

In addition, thanks to a partnership with [*Let's Encrypt*](https://letsencrypt.org/) by [*Internet Security Research Group (ISRG)*](https://www.abetterinternet.org/), *HTTPS* encryption is offered free of charge and also includes a content distribution network (*CDN*) backed by [*Cloudfare*](https://www.cloudflare.com/). You will be able to have a static website that is scalable, redundant, fast and securely encrypted.

> Disponible en español [aquí](https://www.nocountryforgeeks.com/dominios-seguros-a-medida-con-github-pages/).

From the configuration of the *GitHub* repository itself, the options to configure the site in a simple way are offered. Simply indicate the branch from which the website will be deployed, activate the option "*Enforce HTTPS*", and if we want a custom domain (which we must previously buy from our domain provider and configure to redirect to our website *GitHub Pages*).

![GitHub Pages settings](/assets/posts/en/custom-secure-domains-with-github-pages/image01.jpg)

> In this article we are not going to see how the compilation of static websites works on *GitHub Pages*, as well as *Jekyll* or its *themes*. At the moment with a simple `index.html` page with any text is enough to try.

## *CNAME* and its restrictions

If you want to have more than one repository with its static website and domains for each, you know that it is not an easy task, hence this article, since the way *GitHub Pages* works assumes that the main page of your user or organization (by default a repository with `username.github.io`) will be of type: *https://usuario.github.io/* and the rest of repositories (considered projects) will follow a subdomain scheme of type: *https://usuario.github.io/nombrerepositorio/*. The following scheme is used:

| Repository | URL |
|---|---|
| https://github.com/danimart1991/danimart1991.github.io | https://www.danimart1991.github.io/ |
| https://github.com/danimart1991/project1 | https://www.danimart1991.github.io/project1/ |
| https://github.com/danimart1991/project2 | https://www.danimart1991.github.io/project2/ |
| ... | ... |

A priori this is not a problem. If we buy two domains (one for each repository) and according to the theory, it would be enough to put the custom domain in the configuration options of the repository for *GitHub Pages*.

![GitHub Pages domain settings](/assets/posts/en/custom-secure-domains-with-github-pages/image02.jpg)

However, what this change creates is a *CNAME* file at the root of our repository, which basically tells our website the domain it points to. In the *DNS* configuration of our domain provider, we must do the same indicating the web to which we want to redirect, and this is when the subdomains come into play, since a *CNAME* redirection does not accept subdomains (*https://domain.com/subdomain*), and therefore, we can not create the redirection with the secondary repository. Giving errors both in the redirection itself and in the activation of *HTTPS* encryption.

![CNAME Github Pages](/assets/posts/en/custom-secure-domains-with-github-pages/image03.jpg)

## Using *DNS* records of type *A*

The solution is to use other types of *DNS* records apart from *CNAME*. With this, we indicate that you must create a hierarchy that will be hidden behind the custom domains and both repositories will have their own websites and domains.

This is the configuration of *DNS* records for both domains. Notice how in both cases *A-type* records are used pointing to *GitHub IPs* and create a *CNAME* record pointing always to the main repository domain (without *www*).

![DNS Records](/assets/posts/en/custom-secure-domains-with-github-pages/image04.jpg)

Then configure both domains in the *GitHub* repository settings for each. Finally, creating the redirection in this way will also allow you to use the option to create a secure *HTTPS* domain.

| Repository | URL |
|---|---|
| https://github.com/danimart1991/danimart1991.github.io | https://www.danielmartingonzalez.com/ |
| https://github.com/danimart1991/project1 | https://www.project1.com/ |
| https://github.com/danimart1991/project2 | https://www.project2.com/ |
| ... | ... |

## Conclusion

At this point we'll have as many custom domains as we want pointing to our repositories with *GitHub Pages* websites. The only problem with this method is that when you create an association by subdomain hierarchy, if you access *https://www.danielmartingonzalez.com/project1*, it will redirect you to the domain *https://www.project1.com/*, in my opinion something assumable.

I hope this article will save you the headaches I've had with custom domains and *GitHub Pages*.
