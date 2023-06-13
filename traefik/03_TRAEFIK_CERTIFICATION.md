## What is the difference between a DNS challenge and TLS challenge in Traefik?

Traefik  provides dynamic SSL certificate management through Let's Encrypt.

When it comes to generating and renewing SSL certificates, Traefik can leverage the ACME (
Automatic Certificate Management Environment) protocol and use either the HTTP challenge or the DNS challenge.

1. HTTP Challenge (http-01): In this method, the ACME server validates your control over a domain by ensuring that you 
can host a specific file at a known location. It does this by trying to access a specific path
(http://<YOUR_DOMAIN>/.well-known/acme-challenge/<TOKEN>) on your server. If your server can serve the correct content,
it proves you have control over the domain. This challenge only works over port 80.

2. DNS Challenge (dns-01): Instead of serving a file over HTTP, the DNS challenge involves adding a specific DNS TXT 
record to the DNS configuration for your domain. The ACME server checks this record to validate your control over the 
domain. This challenge has the advantage of working even if your server isn't publicly accessible on the internet 
(like if it's behind a firewall or NAT) and it allows you to issue wildcard certificates.

## DNS Challenge on DigitalOcean

1. Create a DigitalOcean API token with read and write access to DNS records.
2. Add the token to the docker-compose.yml file as an environment variable named DO_AUTH_TOKEN.

## DNS Wildcard Certificates

To create a wildcard certificate, you need to use the DNS challenge. This is because the HTTP challenge only works for
specific subdomains, not for all subdomains of a domain.

1. Create a wildcard DNS record for your domain. For example, if your domain is example.com, you would create a
   wildcard record for *.example.com. Set the value of this record to any valid IP address.