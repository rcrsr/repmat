# Infrastructure

Preset for infrastructure-as-code artifacts. Auto-detects IaC tool and cloud provider.

## Detection Signals

Identify IaC tool from:
- CDK: `aws-cdk-lib` or `constructs` in dependencies, `cdk.json`
- Terraform: `.tf` files, `terraform` blocks, provider configurations
- Pulumi: `Pulumi.yaml`, `pulumi` imports
- CloudFormation: `.yaml`/`.json` with `AWSTemplateFormatVersion`
- Ansible: `playbook.yml`, `ansible.cfg`, roles directory
- Kubernetes: `k8s/`, manifests with `apiVersion`, `kind`
- Helm: `Chart.yaml`, `templates/`, `values.yaml`
- Kustomize: `kustomization.yaml`

Identify cloud provider from:
- AWS: AWS-specific resources, `aws` provider
- GCP: Google Cloud resources, `google` provider
- Azure: Azure resources, `azurerm` provider
- Multi-cloud: Multiple providers configured

## Search Keywords

Based on detected tool:
- "[tool] best practices [year]"
- "[tool] patterns and anti-patterns"
- "[tool] project structure"
- "[tool] testing patterns"

Based on cloud provider:
- "[provider] [tool] patterns"
- "[provider] well-architected framework"

General infrastructure:
- "infrastructure as code best practices"
- "IaC security patterns"
- "cloud resource naming conventions"

## Investigation Focus

When analyzing local infrastructure code, examine:
- **Resource organization** — How are resources grouped? By environment? Service?
- **Naming conventions** — Resource naming patterns, tagging strategy
- **Environment handling** — Dev/staging/prod separation, configuration
- **State management** — Remote state, locking, state file organization
- **Secrets handling** — How are secrets managed? Vaults? SSM?
- **Modularity** — Reusable modules/constructs, composition patterns
- **Security patterns** — IAM, network security, encryption
- **Cost management** — Cost tagging, budget alerts, resource sizing
- **Drift detection** — How is configuration drift handled?
- **Disaster recovery** — Backup strategies, multi-region patterns

## Policy Considerations

Common sections for infrastructure policies:
- Resource naming conventions
- Tagging requirements
- Environment configuration
- State management
- Secrets handling
- Security requirements (IAM, network)
- Module/construct patterns
- Testing requirements

## CI/CD Detection

Look for infrastructure pipeline configurations:
- GitHub Actions: `.github/workflows/*.yml` with terraform/cdk steps
- GitLab CI: `.gitlab-ci.yml` with infrastructure stages
- Atlantis: `atlantis.yaml`
- Terraform Cloud: `.terraform/` workspace configs
- ArgoCD: `argocd/`, Application manifests

## Security Investigation

Examine security-related patterns:
- **Policy-as-code** — OPA, Sentinel, Checkov, tfsec configs
- **IAM patterns** — Least privilege, role boundaries, assume role
- **Network security** — Security groups, NACLs, firewall rules
- **Encryption** — KMS, secrets rotation, TLS termination
- **Compliance** — Tagging for compliance, audit logging

## Defaults

- **meta_type**: stateless
- **author_role**: engineer
- **typical_patterns**: `**/infra/**/*`, `**/*.tf`, `**/cdk/**/*`, `k8s/**/*`, `helm/**/*`
