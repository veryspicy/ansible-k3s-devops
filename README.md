# Ansible K3s DevOps

K3s ARM 集群的 Ansible 自动化部署与运维仓库。

## 关联仓库

| 项目 | 仓库 |
|------|------|
| OCI Terraform IaC | [oci-cluster-tf](https://github.com/veryspicy/oci-cluster-tf) |
| k3s-ansible (上游官方) | [k3s-io/k3s-ansible](https://github.com/k3s-io/k3s-ansible) |

## 仓库结构

`
ansible/
├── k3s-ansible/            ← 官方 k3s-ansible (上游同步)
│   └── playbooks/
│       └── site.yml
│
├── post-cluster.yml        ← K3s 安装后置部署 (cert-manager / TLS)
├── ci-cd.yml               ← CI/CD 栈部署 (Jenkins + ArgoCD + Harbor)
│
├── group_vars/             ← 集群变量 (敏感，不进 Git)
│   └── all.yml
│
├── inventory/              ← Ansible 主机清单 (运行时生成)
│   └── oci.ini
│
└── roles/
    ├── post_cluster/       ← cert-manager 部署 role
    └── ci_cd/              ← CI/CD 栈部署 role
`

## 组件

| 层 | 技术栈 |
|----|--------|
| 集群 | K3s v1.33.4 (3x ARM Ampere A1) |
| Ingress | Traefik |
| TLS | cert-manager + Let's Encrypt DNS-01 (Cloudflare) |
| CI | Jenkins (Helm, ARM64 JDK21) |
| CD | ArgoCD (Helm, GitOps) |
| 镜像仓库 | Harbor (Helm, ARM64) |

## 快速开始

`ash
# 1. 部署 K3s 集群
ansible-playbook -i inventory/oci.ini k3s-ansible/playbooks/site.yml

# 2. 后置部署 (cert-manager / TLS)
ansible-playbook -i inventory/oci.ini post-cluster.yml

# 3. CI/CD 栈 (可选)
ansible-playbook -i inventory/oci.ini ci-cd.yml
`

## 变更记录

| 日期 | 变更 |
|------|------|
| 2026-06-13 | Jenkins + ArgoCD + Harbor 部署计划 (ci-cd.yml + role) |
| 2026-06-08 | Ansible + Helm 统一部署 cert-manager (post-cluster.yml) |
| 2026-06-07 | cert-manager DNS-01 + Cloudflare CDN 全链路 HTTPS |
| 2026-06-06 | K3s ARM 集群初始化 (k3s-ansible) |
