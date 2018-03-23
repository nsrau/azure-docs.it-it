---
title: Usare moduli Terraform per creare un cluster di macchine virtuali in Azure
description: Informazioni su come usare moduli Terraform per creare un cluster di macchine virtuali Windows in Azure
keywords: terraform, devops, macchina virtuale, rete, moduli
author: rloutlaw
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 10/19/2017
ms.custom: devops
ms.author: routlaw
ms.openlocfilehash: e33aef252413eeb243b03543f171d5f1e2385b48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>Creare un cluster di macchine virtuali con il registro del modulo

Questo articolo descrive in modo dettagliato come creare un piccolo cluster di macchine virtuali con il [modulo di calcolo di Azure](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2) di Terraform. In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Configurare l'autenticazione con Azure
> * Creare il modello Terraform
> * Visualizzare le modifiche con il piano
> * Applicare la configurazione per creare il cluster di macchine virtuali

Per altre informazioni su Terraform, vedere la [documentazione di Terraform](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Configurare l'autenticazione con Azure

> [!TIP]
> Se si [usano le variabili di ambiente di Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) o si esegue questa esercitazione in [Azure Cloud Shell](/azure/cloud-shell/overview), ignorare questo passaggio.

 Vedere [Installare Terraform e configurare l'accesso ad Azure](/azure/virtual-machines/linux/terraform-install-configure) per creare un'entità servizio di Azure. Usare questa entità servizio per compilare un nuovo file `azureProviderAndCreds.tf` in una directory vuota con il codice seguente:

```tf
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Creare il modello

Creare un nuovo modello Terraform denominato `main.tf` con il codice seguente:

```tf
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = "${module.network.vnet_subnets[0]}"
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = "${module.mycompute.public_ip_dns_name}"
}

output "vm_public_ip" {
    value = "${module.mycompute.public_ip_address}"
}

output "vm_private_ips" {
    value = "${module.mycompute.network_interface_private_ip}"
}
```

Eseguire `terraform init` nella directory di configurazione. Usando almeno la versione 0.10.6 di Terraform, viene restituito l'output seguente:

![Inizializzazione di Terraform](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Visualizzare le modifiche con il piano

Eseguire `terraform plan` per visualizzare in anteprima l'infrastruttura di macchine virtuali creata dal modello.

![Piano Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Creare le macchine virtuali con il comando apply

Eseguire `terraform apply` per effettuare il provisioning delle macchine virtuali in Azure.

![Comando apply di Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Passaggi successivi

- Visualizzare l'elenco dei [moduli Terraform per Azure](https://registry.terraform.io/modules/Azure)
- Creare un [set di scalabilità di macchine virtuali con Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
