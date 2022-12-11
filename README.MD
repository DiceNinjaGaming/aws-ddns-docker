

# Summary
The [AWS DDNS for Docker](https://hub.docker.com/repository/docker/diceninjagaming/aws-ddns) project is a container that performs a simple task: Whenever your external (WAN) IP address changes, update one or more DNS records in Amazon Route 53.

# Description
This project is aimed at anybody who self-hosts services without a static IP and uses Route 53 for their domain DNS. Whether you use it to connect to your home VPN, host game servers, or have a small home business, this is a quick, free, and easy alternative to services such as DynDNS.

By using this container you'll have your own self-hosted dynamic DNS updater up and running in a matter of minutes!

The main code borrows heavily from [famzah's aws-dyndns](https://github.com/famzah/aws-dyndns) script, as it is a simple, no-frills script that just works.

The base image is `phusion/baseimage` to make it as lightweight as possible while still giving it self-healing capability.

Variables:

$AWS_ACCESS_KEY
$AWS_SECRET
$DOMAIN
$DNS_TTL
$AWS_ZONE_ID
$RECHECK_SECS
MAX_RETRIES
RETRY_DELAY