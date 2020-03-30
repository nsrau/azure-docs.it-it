---
title: Guida alla pubblicazione dell'offerta di modelli di soluzione per le applicazioni di Azure Azure Marketplace
description: Questo articolo descrive i requisiti per pubblicare un modello di soluzione in Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 9/25/2019
ms.author: dsindona
ms.openlocfilehash: c84436015ad37b57f6603551f1d328ac76181836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288734"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Applicazioni di Azure: Guida alla pubblicazione dell'offerta di modello di soluzione

I modelli di soluzione sono uno dei modi principali per pubblicare una soluzione nel Marketplace. Usare questa guida per comprendere i requisiti per questa offerta. 

Usare app Azure: offerta Modello di soluzione quando una soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre a una singola macchina virtuale. È possibile automatizzare il provisioning di una o più macchine virtuali con le app di Azure: Modelli di soluzione. È anche possibile eseguire il provisioning di risorse di rete e di archiviazione. App di Azure: l'offerta Modello di soluzione offre i vantaggi di automazione per singole macchine virtuali e le soluzioni complete basate su IaaS.

Questi modelli di soluzione non sono offerte di transazione, ma possono essere usati per distribuire le offerte di macchine virtuali a pagamento fatturate tramite il marketplace commerciale di Microsoft.These solution templates are not transaction offers, but can be used to deploy paid VM offers billed through Microsoft's commercial marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".


## <a name="requirements-for-solution-templates"></a>Requisiti per i modelli di soluzioni

| **Requisiti** | **Dettagli**  |
| ---------------  | -----------  |
|Fatturazione e misurazione    |  Verrà eseguito il provisioning delle risorse nella sottoscrizione di Azure del cliente. Le macchine virtuali PAYGO (Pay-as-you-go) verranno effettuate transazioni con il cliente tramite Microsoft, fatturate tramite la sottoscrizione di Azure del cliente (PAYGO).  <br/> Nel caso del modello BYOL (Bring Your Own License), mentre Microsoft addebiterà i costi di infrastruttura sostenuti nella sottoscrizione del cliente, le transazioni relative alle tariffe delle licenze software verranno effettuate direttamente con il cliente.   |
|Disco rigido virtuale (VHD) compatibile con Azure  |   Le macchine virtuali devono essere compilate in Windows o Linux.  Per altre informazioni, [vedere Creare un disco rigido virtuale compatibile con Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Attribuzione dell'utilizzo da parte dei clienti | L'abilitazione dell'attribuzione dell'utilizzo da parte del cliente è necessaria in tutti i modelli di soluzione pubblicati in Azure Marketplace. Per altre informazioni sull'attribuzione dell'utilizzo da parte del cliente e su come abilitarla, vedere [Attribuzione dell'utilizzo da parte del cliente dei partner di Azure](./azure-partner-customer-usage-attribution.md).  |
| Usare Managed Disks | [Dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) è l'opzione predefinita per i dischi persistenti delle macchine virtuali IaaS in Azure.Managed Disks is the default option for persisted disks of IaaS VMs in Azure. È necessario utilizzare dischi gestiti nei modelli di soluzione. <br> <br> 1. Seguire le [indicazioni](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) e [gli esempi](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) per l'uso di dischi gestiti nei modelli di Azure ARM per aggiornare i modelli di soluzione. <br> <br> 2. Seguire le istruzioni riportate di seguito per importare il disco rigido virtuale sottostante dei dischi gestiti in un account di archiviazione per pubblicare il disco rigido virtuale come immagine nel Marketplace: <br> <ul> <li> [Powershell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Cli](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Passaggi successivi
Se non è già stato fatto, [eseguire la registrazione](https://azuremarketplace.microsoft.com/sell) nel marketplace.

Se la registrazione è stata effettuata e si sta creando una nuova offerta o se ne sta usando una esistente, accedere al [portale Cloud Partner](https://cloudpartner.azure.com) per creare o completare l'offerta.
