# derper_docker

derper_docker

# Helm Charts

Notice: It works well with K3s. SSL cert is managed by cert-manager. You should edit the templates, if K8s is used to replace K3s.

![Helm with ArgoCD](static/helm_argocd.png)

```
# values.yaml
hostname: derp.example.com  # Your derper hostname
replicaCount: 1 # Replica count, Suggest 1

mesh_with: # Mesh with other derper, if empty mesh is disabled
pskKey: # Mesh PSK key, must be same with other derper, 64 hex string at least

ingressAnnotations: # Ingress annotations
  cert-manager.io/cluster-issuer: letsencrypt
  kubernetes.io/ingress.class: traefik

```
# DERP HELP

```
Usage of derper:
  -a string
        server HTTPS listen address, in form ":port", "ip:port", or for IPv6 "[ip]:port". If the IP is omitted, it defaults to all interfaces. (default ":443")
  -accept-connection-burst int
        burst limit for accepting new connection (default 9223372036854775807)
  -accept-connection-limit float
        rate limit for accepting new connection (default +Inf)
  -bootstrap-dns-names string
        optional comma-separated list of hostnames to make available at /bootstrap-dns
  -c string
        config file path
  -certdir string
        directory to store LetsEncrypt certs, if addr's port is :443 (default "/home/user/.cache/tailscale/derper-certs")
  -certmode string
        mode for getting a cert. possible options: manual, letsencrypt (default "letsencrypt")
  -derp
        whether to run a DERP server. The only reason to set this false is if you're decommissioning a server but want to keep its bootstrap DNS functionality still running. (default true)
  -dev
        run in localhost development mode
  -hostname string
        LetsEncrypt host name, if addr's port is :443 (default "derp.tailscale.com")
  -http-port int
        The port on which to serve HTTP. Set to -1 to disable. The listener is bound to the same IP (if any) as specified in the -a flag. (default 80)
  -mesh-psk-file string
        if non-empty, path to file containing the mesh pre-shared key file. It should contain some hex string; whitespace is trimmed.
  -mesh-with string
        optional comma-separated list of hostnames to mesh with; the server's own hostname can be in the list
  -stun
        whether to run a STUN server. It will bind to the same IP (if any) as the --addr flag value. (default true)
  -stun-port int
        The UDP port on which to serve STUN. The listener is bound to the same IP (if any) as specified in the -a flag. (default 3478)
  -unpublished-bootstrap-dns-names string
        optional comma-separated list of hostnames to make available at /bootstrap-dns and not publish in the list
  -verify-clients
        verify clients to this DERP server through a local tailscaled instance.

```

# Example Dockerfile

```
version: "3.7"
services:
  derper:
    image: dextercai/derper:latest
    volumes:
    - ./cert:/app/cert
    ports:
    - 443:443
    - 3478:3478/udp
    command: ["-certdir=/app/cert", "-hostname=derp.****.com"]
```
