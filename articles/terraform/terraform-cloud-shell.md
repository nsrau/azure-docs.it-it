---
title: Usare Terraform con Azure Cloud Shell
description: Usare Terraform con Azure Cloud Shell per semplificare l'autenticazione e la configurazione del modello.
services: terraform
ms.service: terraform
keywords: terraform, devops, set di scalabilità, macchina virtuale, rete, archiviazione, moduli
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2017
ms.openlocfilehash: a5f44edf201ce9a2e2c0da6b5da9031958a11fdc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959309"
---
# <a name="terraform-cloud-shell-development"></a>Sviluppo Terraform in Cloud Shell 

Terraform funziona perfettamente da una riga di comando Bash, ad esempio il Terminale macOS o l'esperienza Bash in Windows o Linux. L'esecuzione delle configurazioni Terraform nell'esperienza Bash di [Azure Cloud Shell](/azure/cloud-shell/overview) offre alcuni vantaggi esclusivi che consentono di velocizzare il ciclo di sviluppo.

Questo articolo concettuale illustra le funzionalità di Cloud Shell che consentono di scrivere script Terraform da distribuire in Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configurazione automatica delle credenziali

Terraform è installato e immediatamente disponibile in Cloud Shell. Quando si è connessi a Cloud Shell per gestire l'infrastruttura, gli script Terraform vengono autenticati in Azure senza la necessità di configurazioni aggiuntive. Grazie all'autenticazione automatica, non è necessario creare manualmente un'entità servizio di Active Directory e configurare le variabili del provider Terraform per Azure.


## <a name="using-modules-and-providers"></a>Uso di moduli e provider

I moduli Terraform per Azure richiedono le credenziali per accedere e apportare modifiche alle risorse nella sottoscrizione di Azure. Quando si lavora in Cloud Shell, aggiungere agli script il codice seguente per usare i moduli Terraform per Azure in Cloud Shell:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Quando si usa qualsiasi comando dell'interfaccia della riga di comando `terraform`, Cloud Shell passa i valori necessari per il provider `azurerm` attraverso variabili di ambiente.

## <a name="other-cloud-shell-developer-tools"></a>Altri strumenti di sviluppo Cloud Shell

I file e gli stati della shell vengono mantenuti in Archiviazione di Azure tra le sessioni di Cloud Shell. Usare [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) per copiare e caricare i file in Cloud Shell dal computer locale.

In Cloud Shell è disponibile l'interfaccia della riga di comando di Azure, che è un ottimo strumento per testare le configurazioni e verificare il proprio lavoro al termine di un comando `terraform apply` o `terraform destroy`.


## <a name="next-steps"></a>Passaggi successivi

[Creare un cluster di macchine virtuali con il registro del modulo](terraform-create-vm-cluster-module.md)
[Create a small VM cluster using custom HCL](terraform-create-vm-cluster-with-infrastructure.md) (Creare un piccolo cluster di macchine virtuali con un HCL personalizzato)
