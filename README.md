##### Azure Virtual Network Configuration & Networking Project

###### Project Overview
This project focuses on the foundational principles of cloud networking within Microsoft Azure. The goal was to design and implement a secure, scalable Virtual Network (VNet), utilizing CIDR notation for IP addressing, creating isolated subnets for different architectural tiers, and validating internal connectivity between resources.

###### Architecture Design
- **VNet Address Space:** `10.0.0.0/16`
- **Subnets:**
  - Web Tier (`snet-web`): `10.0.1.0/24`
  - App Tier (`snet-app`): `10.0.2.0/24`
  - Database Tier (`snet-db`): `10.0.3.0/24`
- **Network Security Groups (NSGs):**
  - `nsg-web-tier`: Configured to allow inbound traffic on ports 80 (HTTP) and 443 (HTTPS).

###### Deployment Steps & CLI Commands
The entire infrastructure was provisioned using the Azure CLI. Below are the exact commands used:

###### 1. Create the Resource Group

**BASH**

> az group create --name rg-networking-project --location westus2


###### 2. Create the Virtual Network VNet

**BASH**

```

az network vnet create \
  --resource-group rg-networking-project \
  --name vnet-core-architecture \
  --address-prefix 10.0.0.0/16 \
  --location westus2

```

###### 3. Create the Subnets

**BASH**

###### Web Subnet

```

az network vnet subnet create \
  --resource-group rg-networking-project \
  --vnet-name vnet-core-architecture \
  --name snet-web \
  --address-prefix 10.0.1.0/24

```

###### App Subnet

```

az network vnet subnet create \
  --resource-group rg-networking-project \
  --vnet-name vnet-core-architecture \
  --name snet-app \
  --address-prefix 10.0.2.0/24

```

###### Database Subnet

```
az network vnet subnet create \
  --resource-group rg-networking-project \
  --vnet-name vnet-core-architecture \
  --name snet-db \
  --address-prefix 10.0.3.0/24

  ```

######  5. Deploy Test Virtual Machines

Two VMs were deployed to test network isolation and internal connectivity.

- vm-web-test: Deployed in the Web Subnet with no public IP.

- vm-jumper: Deployed in the App Subnet with a public IP to act as a bastion host.

**BASH**

###### Deploy the Web Test VM (No public IP)

```

az vm create \
  --resource-group rg-networking-project \
  --name vm-web-test \
  --image Canonical:0001-com-ubuntu-server-focal:20_04-lts:latest \
  --admin-username azureuser \
  --generate-ssh-keys \
  --vnet-name vnet-core-architecture \
  --subnet snet-web \
  --public-ip-address ""

```

###### Deploy the Jumper VM (With public IP for SSH access)

```

az vm create \
  --resource-group rg-networking-project \
  --name vm-jumper \
  --image Canonical:0001-com-ubuntu-server-focal:20_04-lts:latest \
  --admin-username azureuser \
  --ssh-key-values "$(cat ~/.ssh/id_ed25519.pub)" \
  --vnet-name vnet-core-architecture \
  --subnet snet-app \
  --public-ip-sku Standard

```