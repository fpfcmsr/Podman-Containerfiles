# Podman-Containerfiles
## Simple Podman Quadlet Containerfiles for home use.
The expectation is that these services are run behind a firewall/router and are not exposed to the internet.
There are ways of getting access to them remotely with a vpn/tailscale/netbird/reverse proxy, but those are not in the scope of this repository.

I mainly want to document for myself and anyone who may find it useful getting started with podman containerfiles, and hopefully you have to spend less time than I did struggling to convert the ever common dockerfiles into something podman can turn into a systemd service.

## Getting Started - Things to prepare so you and your services are happy:

#### Log in as the user you want running your containers and enable podman
systemctl --user enable --now podman.socket

#### Run this command for container auto-updating (also needs specifying in the containerfile)
systemctl --user enable podman-auto-update  
add "AutoUpdate=registry" under [Container] in your .container file

#### Allow your user to run podman services without needing you to login
loginctl enable-linger $USER

#### Place your .container files under ~/.config/containers/systemd/

##### Run this command when you update or create a .container file
systemctl --user daemon-reload  
systemctl --user restart <name-of-service(s)>

##### To have your containers talk to each other create a podman .network or .pod
See examples in repo for podman pods

##### Set up podman secrets
nano secretfileforpodman "and then type a good password"  
podman secret create mypodmansecret secretfileforpodman  
rm secretfileforpodman  
You can also pass the password directly to podman to create a secret:  
echo "superstrongpassword" | podman secret create mypodmansecret -  
Remember to remove the value of the password from your bash history in ~/.bash_history  
I recommend storing the passwords (as they are deleted) in a password manager or someplace safe  

## Useful Troubleshooting Commands
systemctl --user restart <name-of-service> --no-block; journalctl --user -f  
/usr/lib/systemd/system-generators/podman-system-generator --user --dryrun

check out anatomy_of_a_containerfile for some basics on what's the different parts of the containerfile mean. Note, its not comprehensive, but just my current understanding. PRs improving it gratefully accepted.

## References:
https://blog.datalad.org/posts/forgejo-aneksajo-podman-deployment/  
https://thinkaboutit.tech/posts/2025-07-13-implement-nextcloud-with-podman-quadlet/#create-pod  
https://scardex.nl/portfolio/pieter/9  
https://forgejo.org/docs/latest/admin/installation/docker/  
https://www.redhat.com/en/blog/multi-container-application-podman-quadlet  
https://www.redhat.com/en/blog/podman-kubernetes-secrets  
https://old.reddit.com/r/podman/comments/16onu8p/podman_secrets_how_the_hell_does_it_work/  
