Sure! Below are the Terraform files you should have for your GKE + Flux + PostgreSQL setup, properly modularized and explained.

---

**1. `providers.tf`**
```hcl
terraform {
  required_version = ">= 1.4.4"
  required_providers {
    google     = { source = "hashicorp/google", version = "6.22.0" }
    helm       = { source = "hashicorp/helm", version = "2.17.0" }
    kubernetes = { source = "hashicorp/kubernetes", version = "2.36.0" }
    random     = { source = "hashicorp/random", version = "3.7.1" }
  }
}

data "google_client_config" "current" {}

provider "kubernetes" {
  token         = data.google_client_config.current.access_token
  config_path   = "~/.kube/config"
  config_context = ""
}

provider "helm" {
  kubernetes {
    token         = data.google_client_config.current.access_token
    config_path   = "~/.kube/config"
    config_context = ""
  }
}
```

---

**2. `k8s.tf`**
```hcl
resource "kubernetes_namespace" "example_app" {
  metadata {
    name = var.namespace
  }
}
```

---

**3. `flux.tf`**
```hcl
resource "kubernetes_namespace" "flux" {
  metadata {
    name = var.namespace
  }
}
}

resource "kubernetes_secret" "flux_gitlab_auth" {
  metadata {
    name      = var.namespace
    namespace = var.namespace
  }
  data = {
    username = var.gitlab_user
    password = var.gitlab_token
  }
}

resource "kubernetes_manifest" "gitrepository" {
  manifest = yamldecode(templatefile("${path.module}/manifests/gitrepository.yaml", {
    gitlab_url = var.gitlab_url
  }))
}
```

---

**4. `helm-release.tf`**
```hcl
resource "helm_release" "example_app" {
  name       = var.helm_release_name
  repository = var.helm_repository
  chart      = "example-app"
  version    = var.helm_chart_version
  namespace  = var.namespace

  values = [
    templatefile("${path.module}/values.yaml.tpl", {
      db_user     = var.db_user,
      db_name     = var.db_name,
      db_password = random_password.cloudlab-db-password.result,
    })
  ]
}
```

---

**5. `database.tf`**
```hcl
resource "google_sql_database" "cloudlab_core_db" {
  project  = var.project_id
  name     = var.db_name
  instance = var.postgres_instance
}

resource "random_password" "cloudlab-db-password" {
  length           = 12
  special          = true
  override_special = "_@"
}

resource "google_sql_user" "cloudlab_db_user" {
  project  = var.project_id
  name     = var.db_user
  instance = var.postgres_instance
  password = random_password.cloudlab-db-password.result
}

resource "kubernetes_secret" "db_credentials" {
  metadata {
    name      = "db-credentials"
    namespace = var.namespace
  }

  data = {
    username = var.db_user
    password = random_password.cloudlab-db-password.result
  }
}
```

---

**6. `variables.tf`**
```hcl
variable "project_id" {}
variable "gke_cluster_name" {}
variable "postgres_instance" {}
variable "db_user" {}
variable "db_name" {}
variable "region" {}
variable "namespace" {}

variable "helm_repository" {}
variable "helm_release_name" {}
variable "helm_chart_version" {}

variable "gitlab_url" {}
variable "gitlab_user" {}
variable "gitlab_token" {}
```

---

**7. `terraform.tfvars`**
```hcl
project_id           = "my-project"
gke_cluster_name     = "gke-staging"
postgres_instance    = "example-postgres"
db_user              = "example-user"
db_name              = "exampledb"
region               = "us-central1"
namespace            = "example"

helm_repository      = "oci://registry.gitlab.com/myorg"
helm_release_name    = "example-app"
helm_chart_version   = "1.2.3"

gitlab_url           = "https://gitlab.com/myorg/k8s-flux"
gitlab_user          = "gitlab-user"
gitlab_token         = "my-token"
```

### manifests.tf — Refactored with Improvements

```hcl
locals {
  manifests = {
    backend_ui    = "./files/backend-config-ui.yaml.tpl"
    backend_api   = "./files/backend-config-api.yaml.tpl"
    mps_api       = "./files/backend-config-mps-api.yaml.tpl"
    ssl_cert      = "./files/airworthiness-ssl-cert.yaml.tpl"
    ingress       = "./files/http-ingress.yaml.tpl"
    frontend_conf = "./files/frontend-config.yaml.tpl"
  }
}

resource "kubernetes_manifest" "generated" {
  for_each = local.manifests

  manifest = yamldecode(templatefile(each.value, {
    NAMESPACE  = var.namespace,
    DOMAIN     = var.domain,
    ADDR_NAME  = var.address_name
  }))
}
```
