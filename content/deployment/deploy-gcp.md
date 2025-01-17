---
title: "Deploy to Google Cloud"
metaTitle: "Deploy to Google Cloud"
metaDescription: "Step-by-step guide for deploying OpenReplay on Google Cloud."
---

OpenReplay stack can be installed on a single machine and Google Cloud is an ideal candidate. Here's how to do it.

## Launch a VM

1. Go to 'Compute Engine' Dashboard and select 'VM Instances' from the left-side menu bar
2. Click 'Create Instance'
3. Select your preferred 'region' and 'zone'
4. Choose your machine type. The minimum specs are `2 vCPUs, 8 GB of RAM, 50 GB of storage`. So, we recommend the `e2-standard-2` (or an equivalent) for a low/moderate volume. If you're expecting high traffic, you should scale from here.
4. Change your boot disk to *Ubuntu 20.04 LTS*, then pick *SSD persistent disk* for the boot disk type and set the size to 50 GB.
5. Check both 'Allow HTTP traffic' and 'Allow HTTPS traffic' in Firewall
9. Click 'Create'

## Deploy OpenReplay

Once your instance is `Running`, connect to it by hitting the `SSH` button then install OpenReplay:

```bash
git clone https://github.com/openreplay/openreplay.git
cd openreplay/scripts/helm && bash install.sh
```

> **Note:** You'll be prompted to provide the domain on which OpenReplay will be running (e.g. openreplay.mycompany.com). This is required to continue the installation.

## Configure TLS/SSL

OpenReplay deals with sensitive user data and therefore requires HTTPS to run. This is mandatory, otherwise the tracker simply wouldn't start recording. Same thing for the dashboard, without HTTPS you won't be able to replay user sessions.

The easiest way to handle SSL in Google Cloud is to setup a load balancer (Google Load Balancing) and run OpenReplay behind it. Another option is to generate or use your own SSL certificate and point your subdomain (i.e. openreplay.mycompany.com) to the OpenReplay instance. More on both options below.

### Setup Google load balancer (option 1)

First step is to add an instance group which is required later for the load balancer:

1. Go to 'Compute Engine' > 'Instance Groups'
2. 'Create Instance Group' and select *New unmanaged instance group*
3. Pick your preferred 'Location' then select the 'Network' and choose the OpenReplay VM instance
4. Click 'Create'

Now it's time to create the load balancer:

1. Go to 'Network Services' > 'Load Balancing'
2. 'Create Load Balancer' and pick *HTTP(S) Load Balancing*
3. Choose 'From Internet to my VMs' then click 'Continue'
4. Start with 'Backend configuration' and click on 'Backend services' > 'Create a backend service'
5. Select *Instance group* for 'Backend type'. In 'Backends' > 'New backend', choose the instance group you previously created, set the port to `80` then hit 'Done'.
6. Scroll down to 'Health Check' and 'Create a health check'. Choose HTTP for the 'Protocol' and set the 'Port' to `80` while keeping the other default values. Hit 'Save'.
7. Click 'Create'
8. In 'Frontend configuration', choose HTTPS for 'Protocol' then in 'Certificate' create a new certificate (managed by Google) or bring yours. Hit 'Done'.
9. Review then click 'Create'

Once created, go to Cloud DNS (or your DNS service provider) and create an `A Record` that points to the load balancer using its IP (can be found in Load Balancing dashboard).

You're all set now, OpenReplay should be securely accessible on the subdomain you just set up. You can create an account by visiting the `/signup` page (i.e. openreplay.mycompany.com/signup).

### Bring/generate your SSL certificate (option 2)

Alternatively to creating a load balancer, you can bring (or generate) your own SSL certificate.

First, go to Cloud DNS (or your other DNS service provider) and create an `A Record`. Use the domain you previously provided during the installation step and point it to the VM using its public IP (can be found in Compute Engine dashboard).

Open the `vars.yaml` file with the command `vi openreplay/scripts/helm/vars.yaml` then substitute:
- `domain_name`: this is where OpenReplay will be accessible (i.e. openreplay.mycompany.com)
- `nginx_ssl_cert_file_path`: the path to you .cert file (i.e. /home/openreplay/my-cert.crt)
- `nginx_ssl_key_file_path`: the path to your .pem file (i.e. /home/openreplay/my-key.pem)

> **Note:** If you don't have a certificate, generate one for your domain (the one provided during installation) using Let's Encrypt. Connect to OpenReplay VM, run `helm uninstall -n nginx-ingress nginx-ingress` then execute `bash openreplay/scripts/certbot.sh` and follow the steps.

Restart OpenReplay NGINX (and choose whether to enable the default HTTP to HTTPS redirection using the `NGINX_REDIRECT_HTTPS` variable):

```bash
cd openreplay/scripts/helm
NGINX_REDIRECT_HTTPS=1 ./install.sh --app nginx
```

You're all set now, OpenReplay should be accessible on your subdomain. You can create an account by visiting the `/signup` page (i.e. openreplay.mycompany.com/signup).

## Troubleshooting

If you encounter any issues, connect to our [Slack](https://slack.openreplay.com) and get help from our community.
