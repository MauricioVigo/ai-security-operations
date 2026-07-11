# Infrastructure

Promox Virtual Machines

| System | RAM | vCPU | Storage | VLAN |
|--------|----:|------:|--------:|-----:|
| OPNsense | 2 GB | 2 | 30 GB | 30 | * not implemented
| Wazuh + Suricata | 8 GB | 4 | 160 GB | 40 |
| Local LLM (Ollama + Gemma 4) | 14 GB | 6 | 160 GB | 10 |
| Windows Active Directory | 4 GB | 2 | 100 GB | 50 | * not implemented
| Bastion + Keycloak + Vault | 3 GB | 2 | 80 GB | 20 |
| Proxmox Host | 5–6 GB | — | 80 GB | Management |

## Total Resources

| Resource | Total |
|----------|------:|
| RAM | 32–33 GB |
| vCPU | 14 |

Planned Improvements

Windows Active Directory is planned for a future deployment.
IAM (Keycloak) and Vault will be deployed when additional resources are available.
Current Bastion host is allocated 80 GB of storage to support future IAM integration.
