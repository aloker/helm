Helm chart for Roundcube
========================

This chart provides a simple installation of the Roundcube webmail client.

## Installation

If you have not done already, add my Helm repository and get the latest charts.

```
helm repo add aloker https://aloker.github.io/helm
helm repo update
```

Then install Roundcube. Note that *at least* the `host` value (the default IMAP/SMTP host) must be set, either using the `--set` flag or by providing a custom values.yaml.

```
helm install --set host=tls://mail.example.com roundcube aloker/roundcube
```

## Customization

As usual, provide your own values.yaml to configure the installation. 

A more complex installation call:
```
helm install 
    --values=./values.yaml \
    --create-namespace \
    --namespace=roundcube \
    --atomic \
    roundcube \
    aloker/roundcube
```

## Example custom values

This example shows values for an installation with

- persistent storage with the default storage class
- an ingress that routes traffic to Roundcube
- Traefik as ingress controller (not using the built in Let's Encrypt support)
- cert-manager for using Let's Encrypt certificates

```yaml
# Create a PVC for the default storage class
persistence:
  enabled: true
  
# If you're using 
nodeSelector:
  kubernetes.io/hostname: my-node

ingress:
  enabled: true
  annotations: 
    # For use with Traefik: terminate TLS
    traefik.ingress.kubernetes.io/router.tls: "true"
    # For use with cert-manager: provide a suitable (cluster) issuer
    cert-manager.io/cluster-issuer: "letsencrypt"
  tls:
    - secretName: webmail.example.com
      hosts:
        - webmail.example.com
  hosts: 
    - host: webmail.example.com
      paths:
        - path: /

# The default IMAP/SMTP host
host: tls://mail.example.com
```
