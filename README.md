# ArgoCD Nginx Webserver Demo



## What this repo is used for?

This repo contains a Nginx Webserver with custom html page for GitOps with ArgoCD demo
You can read more about the setuu [in this medium article](https://medium.com/@calvineotieno010/gitops-with-argocd-eks-and-gitlab-ci-using-terraform-2a3c094b4ea3)

## Signing and Verifying Images with Sigstore

We use Sigstore to Sign and verify the image. The signing part has been automated via github action. See the workflow for more info.

Example:

#### Generate the Keys

```sh
cosign generate-key-pair 
```

#### Signing

```sh
cosign sign --key cosign.key -a "author=CalvineDevOps" argocd-nginx-webserver
```

#### Verify

```sh
cosign verify --key cosign.pub devopscalvine/nginxwebserver:4184854252 | jq -r . 
```

## Using Kyverno as a verification engine

Kyverno is a policy engine designed for Kubernetes. With Kyverno policies are managed as Kubernetes resource and no new language is required for writing policies.

Kyverno policies can validate, mutate, and generate kubernetes resources. You can use Kyverno CLI to test policies and validate resources as part of your CI/CD pipeline.

Kyverno offers an image verification that uses the Cosign component from Sigstore project.

Using Kyverno immensely improve `Software Supply Chain` by making sure you only run verified images and containers in your infrastructure.

### Installation

#### Add Kyverno Helm repository

```bash
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update
```

### Then install Kyverno using Helm

```helm install kyverno --namespace kyverno kyverno/kyverno --create-namespace
```

Example:

#### Sample Kyverno Policy for image check

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: check-image
spec:
  validationFailureAction: Enforce
  background: false
  webhookTimeoutSeconds: 30
  failurePolicy: Fail
  rules:
    - name: check-image
      match:
        any:
        - resources:
            kinds:
              - Pod
      verifyImages:
      - image: "ghcr.io/nyaras/nginxwebserver:*"
      # This is just a sample cosign public key. Replace with your own public key
        key: |-
          -----BEGIN PUBLIC KEY-----
          MFssdkdkdkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEGosO5RNeDtIXEm2Y7tECBDT0aJVyb
          BkZuykTpu6rgZxcQhN1lJ9b76yjxZnNnWB+4Zl/tgO7k+t2brA0bBfecIFeQ==
          -----END PUBLIC KEY-----
```

#### Testing Kyverno Cluster Policy to ensure that ONLY our signed images can be deploy in K8s

```sh
kubectl run unsignedimgpod --image ghcr.io/nyaras/nginxwebserver:unsignednotsecure
Error from server: admission webhook "mutate.kyverno.svc-fail" denied the request: 

policy Pod/default/unsignedimgpod for resource violation: 

check-image:
  check-image: |
    failed to verify image ghcr.io/nyaras/nginxwebserver:unsignednotsecure: .attestors[0].entries[0].keys: no matching signatures:
```

🔥 Kyverno helps secure our `Software Supply Chain` by preventing deployment of pods that uses containers images have not been signed by our signatures.

## Authors and acknowledgment
 * [Calvine Otieno](https://www.calvineotieno.com)

## License
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

See the LICENSE file for more info