---
title: 'Esercitazione: Configurare Azure Cloud Shell per Terraform'
description: Questa esercitazione illustra come usare Terraform con Azure Cloud Shell per semplificare l'autenticazione e la configurazione di modelli.
keywords: azure devops terraform cloud shell
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945340"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Esercitazione: Configurare Azure Cloud Shell per Terraform

Terraform funziona bene da una riga di comando Bash in macOS, Windows o Linux. L'esecuzione delle configurazioni di Terraform nell'esperienza Bash di [Azure Cloud Shell](/azure/cloud-shell/overview) offre alcuni vantaggi esclusivi. Questa esercitazione illustra come scrivere script di Terraform distribuiti in Azure con Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configurazione automatica delle credenziali

Terraform è installato e immediatamente disponibile in Cloud Shell. Gli script di Terraform vengono autenticati in Azure quando si accede a Cloud Shell per gestire l'infrastruttura senza alcuna configurazione aggiuntiva. L'autenticazione automatica sostituisce due processi manuali:
- Creazione di un'entità servizio di Active Directory
- Configurazione delle variabili del provider di Azure Terraform


## <a name="use-modules-and-providers"></a>Usare moduli e provider

I moduli Azure Terraform richiedono credenziali per l'accesso e la modifica delle risorse di Azure. Per usare i moduli Terraform in Cloud Shell, aggiungere il codice seguente:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

Cloud Shell passa i valori necessari per il provider `azurerm` tramite variabili di ambiente quando si usa uno dei comandi dell'interfaccia della riga di comando `terraform`.

## <a name="other-cloud-shell-developer-tools"></a>Altri strumenti di sviluppo Cloud Shell

I file e gli stati della shell vengono mantenuti in Archiviazione di Azure tra le sessioni di Cloud Shell. Usare [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) per copiare e caricare i file in Cloud Shell dal computer locale.

In Cloud Shell è disponibile l'interfaccia della riga di comando di Azure, che è un ottimo strumento per testare le configurazioni e verificare il proprio lavoro al termine di un comando `terraform apply` o `terraform destroy`.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un piccolo cluster di VM con il registro modulo](terraform-create-vm-cluster-module.md)