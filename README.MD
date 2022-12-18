

# Summary
The [AWS DDNS for Docker](https://hub.docker.com/repository/docker/diceninjagaming/aws-ddns) project is a container that performs a simple task: Whenever your external (WAN) IP address changes, update one or more DNS records in Amazon Route 53.

# Description
This project is aimed at anybody who self-hosts services without a static IP and uses Route 53 for their domain DNS. Whether you use it to connect to your home VPN, host game servers, or have a small home business, this is a quick, free, and easy alternative to services such as DynDNS.

By using this container you'll have your own self-hosted dynamic DNS updater up and running in a matter of minutes!

If you are hosting multiple domains (or sub-domains), you can enter them all, leaving a space between each \
**Example**: `shop.example.com mail.mycustomdomain.com vpn.familynamehere.com`

The main code borrows heavily from [famzah's aws-dyndns](https://github.com/famzah/aws-dyndns) script, as it is a simple, no-frills script that just works.

The base image is `phusion/baseimage` to make it as lightweight as possible while still giving it self-healing capability.

# Pre-Requisites
- Get the [Zone ID]((http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/UsingWithIAM.html)) for the hosted domain from the Route 53 Hosted Zones console
- Create a new user. **Make sure you record the Access Key ID and Access Key Secret**
- Edit the new user and click ```Inline Policies``` 
- Use the Visual Editor:
  - **Service**: Amazon Route 53
  - **Actions**: Add `ChangeResourceRecordSets`
  - **Effect**: Allow
  - **Resources**: Under `HostedZone`, click `Add ARN`. In the `ID` field, enter the Zone ID you got earlier 
    - Example: ```arn:aws:route53:::hostedzone/Z148QEXAMPLE8V```
- Click Review Policy
- Give the policy a descriptive name
- Click `Create Policy`

# Environment Variables
**All variable names and values are case-sensitive!**

Any variable with a blank default is **REQUIRED**, the container will not start properly without them being defined.

| Name             | Default | Description                                                                |
| ---------------- | ------- | -------------------------------------------------------------------------- |
| `AWS_ACCESS_KEY` |         | Access key from AWS                                                        |
| `AWS_SECRET`     |         | Secret key from AWS                                                        |
| `AWS_ZONE_ID`    |         | Unique zone ID from Route 53 for the hosted domain                         |
| `DOMAIN`         |         | Space-separated list of domains to keep updated                            |
| `DNS_TTL`        | `300`   | TTL value for the DNS record, in seconds                                   |
| `RECHECK_SECS`   | `900`   | Number of seconds between checking for IP address updates                  |
| `MAX_RETRIES`    | `10`    | Maximum number of times to retry the DNS update if an error is encountered |
| `RETRY_DELAY`    | `30`    | Number of seconds before retrying a failed DNS update                      |

# Docker Configuration
The container doesn't require any volumes or special setup besides the proper values in the environment variables. It **MUST** be able to connect to the internet.

## Using Docker Run
```
$ docker run -d \
    --name aws-ddns \
    -e AWS_ACCESS_KEY="AK6fakekey4EE" \
    -e AWS_SECRET="SuperDuperSecret" \
    -e AWS_ZONE_ID="Z0000fakezone000" \
    -e DOMAIN="example.com" \
    diceninjagaming/aws-ddns
```

If you're updating multiple domains, change the DOMAIN line to something like:
```
-e DOMAIN="shop.example.com mail.mycustomdomain.com vpn.familynamehere.com" \
```

## Using Docker Compose
*The latest version of the sample docker-compose.yml will always be found in the root of the repo.*

```
version: "3"

services: 
  aws-ddns: 
    image: diceninjagaming/aws-ddns:latest
    restart: unless-stopped
    environment:
      AWS_ACCESS_KEY: AK6fakekey4EE
      AWS_SECRET: SuperDuperSecret
      AWS_ZONE_ID: Z0000fakezone000
      DOMAIN: example.com
      DNS_TTL: 300
      RECHECK_SECS: 900
```
If you're updating multiple domains, change the DOMAIN line to something like:
```
DOMAIN: shop.example.com mail.mycustomdomain.com vpn.familynamehere.com
```