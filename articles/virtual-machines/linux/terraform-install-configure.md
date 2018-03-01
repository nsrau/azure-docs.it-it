---
title: Installare e configurare Terraform per eseguire il provisioning di macchine virtuali e altra infrastruttura in Azure | Microsoft Docs
description: Informazioni su come installare e configurare Terraform per la creazione di risorse di Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: dada9c70eef2adb2704e276a5401509581e37538
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Installare e configurare Terraform per eseguire il provisioning di macchine virtuali e altra infrastruttura in Azure
 
Terraform offre un modo semplice per definire, visualizzare in anteprima e distribuire l'infrastruttura cloud usando un [linguaggio di creazione modelli semplice](https://www.terraform.io/docs/configuration/syntax.html). Questo articolo descrive la procedura da seguire per usare Terraform per effettuare il provisioning di risorse in Azure. 

> [!TIP]
Per altre informazioni su come usare Terraform con Azure, visitare l'[hub di Terraform](/azure/terraform). Terraform viene installato per impostazione predefinita in [Cloud Shell](/azure/terraform/terraform-cloud-shell). Usando Cloud Shell è possibile ignorare le parti relative all'installazione e alla configurazione di questo documento.

## <a name="install-terraform"></a>Installazione di Terraform

Per installare Terraform, [scaricare](https://www.terraform.io/downloads.html) il pacchetto appropriato per il sistema operativo in uso in una directory di installazione distinta. Il download contiene un singolo file eseguibile, per cui è anche necessario definire un percorso globale. Per istruzioni su come impostare il percorso in Mac e Linux, vedere [questa pagina Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Per istruzioni su come impostare il percorso in Windows, vedere [questa pagina Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). 

Per verificare la configurazione del percorso, usare il comando `terraform`. Viene visualizzato un elenco delle opzioni Terraform disponibili come output:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Impostazione dell'accesso di Terraform in Azure

Configurare [un'entità servizio Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli) per consentire a Terraform di effettuare il provisioning di risorse in Azure. L'entità servizio concede agli script Terraform la possibilità di usare le credenziali per effettuare il provisioning di risorse nella sottoscrizione di Azure.

Esistono diversi modi per creare un'applicazione Azure AD e un'entità servizio Azure AD. Il modo più semplice e rapido oggi consiste nell'usare l'interfaccia della riga di comando di Azure 2.0, che [è possibile scaricare e installare in Windows, Linux o Mac](/cli/azure/install-azure-cli).

Eseguire l'accesso per amministrare la sottoscrizione di Azure eseguendo il comando seguente:

   `az login`

Se si dispone di più sottoscrizioni di Azure, i relativi dettagli vengono restituiti dal comando `az login`. Impostare la variabile di ambiente `SUBSCRIPTION_ID` che conterrà il valore del campo `id` restituito dalla sottoscrizione che si intende usare. 

Selezionare la sottoscrizione da usare per la sessione.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Eseguire una query dell'account per ottenere i valori di ID sottoscrizione e tenant.

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Successivamente, creare credenziali separate per Terraform.

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Vengono restituiti appId, password, sp_name e tenant. Prendere nota di appId e password.

Per verificare le credenziali, aprire una nuova shell ed eseguire il comando seguente, usando i valori restituiti per sp_name, password e tenant:

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>Configurare le variabili di ambiente di Terraform

Configurare Terraform in modo da usare l'ID tenant, l'ID sottoscrizione, l'ID client e il segreto client dell'entità servizio usati durante la creazione di risorse di Azure. È anche possibile impostare l'ambiente se si usa un cloud di Azure diverso da Azure pubblico. Impostare le variabili di ambiente seguenti, che vengono usate automaticamente dai [moduli Terraform per Azure](https://registry.terraform.io/modules/Azure).

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID
- ARM_ENVIRONMENT

Di seguito è riportato uno script della shell di esempio che è possibile usare per impostare tali variabili:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Eseguire uno script di esempio

Creare un file `test.tf` in una directory vuota e incollare al suo interno lo script seguente. 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Salvare il file ed eseguire `terraform init`. Questo comando scarica i moduli di Azure necessari per creare un gruppo di risorse di Azure. Viene visualizzato l'output seguente:

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Visualizzare in anteprima lo script con `terraform plan` e quindi creare il gruppo di risorse `testResouceGroup` con `terraform apply`:

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Passaggi successivi

È stato installato Terraform e sono state configurate le credenziali di Azure per avviare la distribuzione dell'infrastruttura nella sottoscrizione di Azure. È stata quindi testata l'installazione tramite la creazione di un gruppo di risorse di Azure vuoto.

> [!div class="nextstepaction"]
> [Creare una macchina virtuale di Azure con Terraform](terraform-create-complete-vm.md)

