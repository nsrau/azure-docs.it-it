---
title: Guida alla pubblicazione dell'offerta di modelli di soluzione per le applicazioni di Azure Azure Marketplace
description: Questo articolo descrive i requisiti per pubblicare un modello di soluzione in Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: faff12adbf6c0f88f3161e1e4f2760da3b7966ca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687525"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Applicazioni di Azure: Guida alla pubblicazione dell'offerta di modello di soluzione

I modelli di soluzione sono uno dei modi principali per pubblicare una soluzione nel Marketplace. Usare questa guida per comprendere i requisiti per questa offerta. 

Usare app Azure: offerta Modello di soluzione quando una soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre a una singola macchina virtuale. È possibile automatizzare il provisioning di una o più macchine virtuali con le app di Azure: Modelli di soluzione. È anche possibile eseguire il provisioning di risorse di rete e di archiviazione. App di Azure: l'offerta Modello di soluzione offre i vantaggi di automazione per singole macchine virtuali e le soluzioni complete basate su IaaS.

Questi modelli di soluzione non sono offerte di transazione, ma possono essere usati per distribuire le offerte di macchine virtuali a pagamento fatturate tramite il marketplace commerciale di Microsoft.These solution templates are not transaction offers, but can be used to deploy paid VM offers billed through Microsoft's commercial marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".


## <a name="requirements-for-solution-templates"></a>Requisiti per i modelli di soluzioni

| **Requisiti** | **Dettagli**  |
| ---------------  | -----------  |
|Fatturazione e misurazione    |  Verrà eseguito il provisioning delle risorse nella sottoscrizione di Azure del cliente. Le macchine virtuali PAYGO (Pay-as-you-go) verranno effettuate transazioni con il cliente tramite Microsoft, fatturate tramite la sottoscrizione di Azure del cliente (PAYGO).  <br/> Nel caso del modello BYOL (Bring Your Own License), mentre Microsoft addebiterà i costi di infrastruttura sostenuti nella sottoscrizione del cliente, le transazioni relative alle tariffe delle licenze software verranno effettuate direttamente con il cliente.   |
|Disco rigido virtuale (VHD) compatibile con Azure  |   Le macchine virtuali devono essere compilate in Windows o Linux.  Per altre informazioni, vedere [Creare un'offerta](./partner-center-portal/create-new-azure-apps-offer.md)di applicazione Azure.For more information, see create an Azure application offer . |
| Attribuzione dell'utilizzo da parte dei clienti | L'abilitazione dell'attribuzione dell'utilizzo da parte del cliente è necessaria in tutti i modelli di soluzione pubblicati in Azure Marketplace. Per altre informazioni sull'attribuzione dell'utilizzo da parte del cliente e su come abilitarla, vedere [Attribuzione dell'utilizzo da parte del cliente dei partner di Azure](./azure-partner-customer-usage-attribution.md).  |
| Usare Managed Disks | [Dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) è l'opzione predefinita per i dischi persistenti delle macchine virtuali IaaS in Azure.Managed Disks is the default option for persisted disks of IaaS VMs in Azure. È necessario utilizzare dischi gestiti nei modelli di soluzione. <br> <br> 1. Seguire le [indicazioni](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) e [gli esempi](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) per l'uso di dischi gestiti nei modelli di Azure ARM per aggiornare i modelli di soluzione. <br> <br> 2. Seguire le istruzioni riportate di seguito per importare il disco rigido virtuale sottostante dei dischi gestiti in un account di archiviazione per pubblicare il disco rigido virtuale come immagine nel Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, 

- [Scopri](https://azuremarketplace.microsoft.com/sell) di più sul mercato.

Per registrarti al Centro per i partner, inizia a creare una nuova offerta o a lavorarla su una esistente:

- [Accedi al Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare o completare la tua offerta.
- Per altre informazioni, vedere [Creare un'offerta di applicazione Azure.See create an Azure application offer](./partner-center-portal/create-new-azure-apps-offer.md) for more information.

