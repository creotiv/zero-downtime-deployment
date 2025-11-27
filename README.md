# Zero-Downtime Deployment

## Zero-downtime IP switching (VIP hopping with drain)
https://github.com/creotiv/ip-switchover-graceful-drain

This demo shows zero-downtime deployment by floating a Virtual IP (VIP) between containers. You start a new container, let it warm up, attach the VIP to it, and only then retire the old container after it drains existing connections. Clients always talk to the same IP while the backing instance changes underneath.

## Graceful upgrade with Go & Tableflip (FDs passing lib)
https://github.com/creotiv/go-fds-passing-graceful-upgrade

This tiny example shows how to restart a Go HTTP server without dropping any connections by passing the listening socket (file descriptor) from the old process to the new one. It uses cloudflare/tableflip, which hides the low‑level socket‑passing mechanics.

## Rolling update in K8S
https://github.com/creotiv/k8s-rollout-test

During a rolling update, Kubernetes updates pods with a new version in a controlled way, terminating old pods and starting new ones. The key to a smooth update is ensuring that the old pods don't get terminated before they've finished handling their current connections. Kubernetes sends a SIGTERM signal to the containers in the pod to initiate a graceful shutdown, followed by a SIGKILL signal after a grace period. But when the SIGTERM signal sent in parallel there could be ongoing request that will hit container which already shutdown. This is open issue in k8s right now.

## Graceful upgrade with Systemd through DEB package
https://github.com/creotiv/deb-package-zero-downtime-deployment

This example shows a zero-downtime deployment flow built on two simple pieces: a DEB package that ships systemd unit templates, and systemd running multiple instances of the service side by side. Each install/upgrade spins up a new `graceful-upgrade-example@<version>` instance while the previous one keeps handling traffic (via `SO_REUSEPORT`), waits briefly, then stops the old instance. The template-based service naming lets us keep both versions alive during the handoff without special orchestration or load balancers.
