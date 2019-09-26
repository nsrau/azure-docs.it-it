---
title: Guida alla pubblicazione dell'offerta del modello di soluzione applicazioni Azure | Azure Marketplace
description: Questo articolo descrive i requisiti per pubblicare un modello di soluzione in Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 9/25/2019
ms.author: ellacroi
ms.openlocfilehash: 725be2ee239a879be8200d33acaf566b1d42d446
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300347"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Applicazioni Azure: Guida alla pubblicazione dell'offerta di modello di soluzione

I modelli di soluzione sono uno dei modi principali per pubblicare una soluzione nel Marketplace. Usare questa guida per comprendere i requisiti per questa offerta. 

Usare app Azure: offerta Modello di soluzione quando una soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre a una singola macchina virtuale. È possibile automatizzare il provisioning di una o più macchine virtuali con le app di Azure: Modelli di soluzione. È anche possibile eseguire il provisioning di risorse di rete e di archiviazione. App di Azure: l'offerta Modello di soluzione offre i vantaggi di automazione per singole macchine virtuali e le soluzioni complete basate su IaaS.

Questi modelli di soluzione non sono offerte di transazione, ma possono essere usati per distribuire le offerte di macchine virtuali a pagamento fatturate tramite il Marketplace commerciale di Microsoft. L'utente viene invitato all'azione tramite "Scarica adesso".


## <a name="requirements-for-solution-templates"></a>Requisiti per i modelli di soluzioni

| **Requisiti** | **Dettagli**  |
| ---------------  | -----------  |
|Fatturazione e misurazione    |  Verrà eseguito il provisioning di risorse nella sottoscrizione di Azure del cliente. Le transazioni delle macchine virtuali con pagamento in base al consumo (PAYGO) verranno effettuate con il cliente tramite Microsoft e fatturate tramite la sottoscrizione di Azure del cliente (PAYGO).  <br/> Nel caso del modello BYOL (Bring Your Own License), mentre Microsoft addebiterà i costi di infrastruttura sostenuti nella sottoscrizione del cliente, le transazioni relative alle tariffe delle licenze software verranno effettuate direttamente con il cliente.   |
|Disco rigido virtuale (VHD) compatibile con Azure  |   Le macchine virtuali devono essere compilate in Windows o Linux.  Per altre informazioni, [vedere Creare un disco rigido virtuale compatibile con Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Attribuzione dell'utilizzo da parte dei clienti | L'abilitazione dell'attribuzione dell'utilizzo da parte del cliente è necessaria in tutti i modelli di soluzione pubblicati in Azure Marketplace. Per altre informazioni sull'attribuzione dell'utilizzo da parte del cliente e su come abilitarla, vedere [Attribuzione dell'utilizzo da parte del cliente dei partner di Azure](./azure-partner-customer-usage-attribution.md).  |
| Usare Managed Disks | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) è l'opzione predefinita per i dischi salvati in permanenza di macchine virtuali IaaS in Azure. È necessario usare Managed Disks nei modelli di soluzione. <br> <br> 1. Per aggiornare i modelli di soluzione, seguire le [istruzioni](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) e gli [esempi](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) per l'uso di Managed disks nei modelli ARM di Azure. <br> <br> 2. Seguire le istruzioni seguenti per importare il VHD sottostante del Managed Disks in un account di archiviazione per pubblicare il disco rigido virtuale come immagine nel Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Passaggi successivi
Se non è già stato fatto, [eseguire la registrazione](https://azuremarketplace.microsoft.com/sell) nel marketplace.

Se la registrazione è stata effettuata e si sta creando una nuova offerta o se ne sta usando una esistente, accedere al [portale Cloud Partner](https://cloudpartner.azure.com) per creare o completare l'offerta.
