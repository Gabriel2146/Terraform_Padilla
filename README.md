# Techustart IAC — Taller Terraform + Azure

Infraestructura como código para desplegar una VM Linux en Azure con Apache2, usando Terraform.

## Arquitectura

```
Resource Group: rg-techustart-dev
│
├── Virtual Network (10.0.0.0/16)
│   └── Subnet (10.0.1.0/24)
│       └── NSG → permite TCP:80 inbound
│
├── Public IP (Static, Standard, Zone 1)
│
├── Network Interface
│   └── IP dinámica privada + IP pública
│
└── Linux VM (Ubuntu 24.04 LTS, Zone 1)
    ├── SSH key auth (RSA 4096 auto-generada)
    ├── OS Disk: Standard_LRS
    └── cloud-init: instala y habilita apache2
```

## Recursos desplegados

| Recurso | Nombre | Descripción |
|---|---|---|
| Resource Group | `rg-techustart-dev` | Contenedor de todos los recursos |
| Virtual Network | `vnet-techustart-dev` | Red virtual `10.0.0.0/16` |
| Subnet | `snet-techustart-dev` | Subred `10.0.1.0/24` |
| Public IP | `pip-techustart-dev` | IP estática en zona 1 |
| NSG | `nsg-techustart-dev` | Permite HTTP (80) inbound |
| NIC | `nic-techustart-dev` | Interfaz de red de la VM |
| Linux VM | `vm-techustart-dev` | Ubuntu 24.04, admin: `techustartadmin` |

## Variables

| Variable | Default | Descripción |
|---|---|---|
| `azure_region` | `centralus` | Región de Azure |
| `tamano_vm` | `Standard_B1s` | SKU de la VM |

## Providers

| Provider | Versión |
|---|---|
| `hashicorp/azurerm` | `~> 4.0` |
| `hashicorp/tls` | `~> 4.0` |
| `hashicorp/local` | `~> 2.0` |

## Pre-requisitos

- [Terraform](https://developer.hashicorp.com/terraform/install) >= 1.0
- [Azure CLI](https://learn.microsoft.com/cli/azure/install-azure-cli) instalado y autenticado
- Suscripción de Azure activa

## Autenticación Azure

```bash
az login
az account set --subscription "<ID_DE_SUSCRIPCION>"
```

## Uso

```bash
cd techustart-iac

# Inicializar providers
terraform init

# Ver plan de cambios
terraform plan

# Aplicar infraestructura
terraform apply

# Destruir todo
terraform destroy
```

## Outputs

| Output | Descripción |
|---|---|
| `public_ip_address` | IP pública de la VM |
| `ssh_private_key_path` | Ruta local de la clave privada SSH generada |

## Conectarse a la VM

```bash
ssh -i ./id_rsa_techustart techustartadmin@<public_ip_address>
```

La clave privada SSH (`id_rsa_techustart`) se genera automáticamente en el directorio del módulo con permisos `0600`.

## Verificar Apache

```bash
curl http://<public_ip_address>
```

## Estructura del proyecto

```
techustart-iac/
├── main.tf              # Recursos principales
├── variables.tf         # Definición de variables
├── terraform.tfstate    # Estado de Terraform (no commitear)
└── .terraform/          # Cache de providers (no commitear)
```

> **Nota:** Los archivos `terraform.tfstate`, `id_rsa_techustart` y el directorio `.terraform/` están en `.gitignore` y no deben subirse al repositorio.
