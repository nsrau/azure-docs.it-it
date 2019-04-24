---
title: Installare e configurare Terraform per l'uso con Azure | Microsoft Docs
description: Informazioni su come installare e configurare Terraform per la creazione di risorse di Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/19/2018
ms.author: echuvyrov
ms.openlocfilehash: 71cf07b227a75e53119f2f35e79ccd7926b551e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418864"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Installare e configurare Terraform per eseguire il provisioning di macchine virtuali e altra infrastruttura in Azure
 
Terraform offre un modo semplice per definire, visualizzare in anteprima e distribuire l'infrastruttura cloud usando un [linguaggio di creazione modelli semplice](https://www.terraform.io/docs/configuration/syntax.html). Questo articolo descrive la procedura da seguire per usare Terraform per effettuare il provisioning di risorse in Azure.

Per altre informazioni su come usare Terraform con Azure, visitare l'[hub di Terraform](/azure/terraform).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Terraform viene installato per impostazione predefinita in [Cloud Shell](/azure/terraform/terraform-cloud-shell). Se si sceglie di installare Terraform in locale, completare il passaggio successivo; in caso contrario, passare a [Configurare l'accesso di Terraform in Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Installazione di Terraform

Per installare Terraform, [scaricare](https://www.terraform.io/downloads.html) il pacchetto appropriato per il sistema operativo in uso in una directory di installazione distinta. Il download contiene un singolo file eseguibile, per cui è anche necessario definire un percorso globale. Per istruzioni su come impostare il percorso in Mac e Linux, vedere [questa pagina Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Per istruzioni su come impostare il percorso in Windows, vedere [questa pagina Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Per verificare la configurazione del percorso, usare il comando `terraform`. Viene visualizzato un elenco delle opzioni Terraform disponibili, come nell'output di esempio di seguito:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Impostazione dell'accesso di Terraform in Azure

Per consentire a Terraform di eseguire il provisioning di risorse in Azure, creare un'[entità servizio di Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli). L'entità servizio concede agli script Terraform la possibilità di effettuare il provisioning di risorse nella sottoscrizione di Azure.

Se hai più sottoscrizioni di Azure, eseguire una query con l'account tramite [az account show](/cli/azure/account#az-account-show) per ottenere un elenco di valori di ID sottoscrizione e ID tenant:

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Per usare una sottoscrizione selezionata, impostare la sottoscrizione per questa sessione con [az account set](/cli/azure/account#az-account-set). Impostare la`SUBSCRIPTION_ID` variabile di ambiente che conterrà il valore del campo restituito`id` dalla sottoscrizione che si intende usare:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

A questo punto è possibile creare un'entità servizio per l'uso con Terraform. Usare [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) e impostare l'*ambito* alla sottoscrizione come indicato di seguito:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Vengono restituiti i valori `appId`, `password`, `sp_name` e `tenant`. Prendere nota di `appId` e `password`.

## <a name="configure-terraform-environment-variables"></a>Configurare le variabili di ambiente di Terraform

Per configurare Terraform per l'uso dell'entità servizio di Azure AD, impostare le variabili di ambiente seguenti che saranno quindi usate dai [moduli Terraform per Azure](https://registry.terraform.io/modules/Azure). È anche possibile impostare l'ambiente se si usa un cloud di Azure diverso da Azure pubblico.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

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

Salvare il file e quindi inizializzare la distribuzione di Terraform. Questo passaggio scarica i moduli di Azure necessari per creare un gruppo di risorse di Azure.

```bash
terraform init
```

L'output è simile all'esempio seguente:

```bash
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

È possibile visualizzare in anteprima le azioni da completare dallo script di Terraform con `terraform plan`. Quando si è pronti per creare il gruppo di risorse, applicare il piano Terraform come indicato di seguito:

```bash
terraform apply
```

L'output è simile all'esempio seguente:

```bash
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

In questo articolo, viene installato Terraform o usato Cloud Shell per configurare le credenziali di Azure e iniziare la creazione di risorse nella sottoscrizione di Azure. Per creare una distribuzione più completa di Terraform in Azure, vedere l'articolo seguente:

> [!div class="nextstepaction"]
> [Creare una macchina virtuale di Azure con Terraform](terraform-create-complete-vm.md)
