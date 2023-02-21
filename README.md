# ArgoCD Nginx Webserver Demo



## What this repo is used for?

This repo contains a Nginx Webserver with custom html page for GitOps with ArgoCD demo
You can read more about the setuu [in this medium article](https://medium.com/@calvineotieno010/gitops-with-argocd-eks-and-gitlab-ci-using-terraform-2a3c094b4ea3)

## Signing and Verifying Images with Sigstore

We use Sigstore to Sign and verify the image. The signing part has been automated via github action. See the workflow for more info.

Example:

#### Signing

```sh
cosign sign --key cosign.key -a "author=CalvineDevOps" argocd-nginx-webserver
```

#### Verify

```sh
cosign verify --key cosign.pub devopscalvine/nginxwebserver:4184854252 | jq -r . 
```

## Authors and acknowledgment
 * [Calvine Otieno](https://www.calvineotieno.com)

## License
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

See the LICENSE file for more info