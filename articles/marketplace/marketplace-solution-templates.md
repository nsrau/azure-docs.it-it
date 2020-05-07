---
title: Guida alla pubblicazione dell'offerta del modello di soluzione applicazioni Azure | Azure Marketplace
description: Questo articolo descrive i requisiti per pubblicare un modello di soluzione in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 4cce509b8f4db7549a05eccc3127d2c437f9c037
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594577"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Applicazioni Azure: modello di soluzione che offre requisiti per la pubblicazione

Questo articolo illustra i requisiti per il tipo di offerta del modello di soluzione, un modo per pubblicare un'offerta di applicazione Azure in Azure Marketplace. Il tipo di offerta del modello di soluzione richiede un [modello di Azure Resource Manager (modello ARM)](../azure-resource-manager/templates/overview.md) per distribuire automaticamente l'infrastruttura della soluzione.

Usare il tipo di offerta del modello di soluzione applicazione Azure quando sono necessarie le condizioni seguenti:

- La soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre a una singola macchina virtuale, ad esempio una combinazione di VM, rete e risorse di archiviazione.
- Il cliente sta per gestire la soluzione.

La chiamata all'azione visualizzata da un utente per questo tipo di offerta è "Get it Now".

## <a name="requirements-for-solution-template-offers"></a>Requisiti per le offerte del modello di soluzione

| **Requisiti** | **Dettagli**  |
| ---------------  | -----------  |
|Fatturazione e misurazione    |  Le offerte per i modelli di soluzione non sono offerte da transazioni, ma possono essere usate per distribuire le offerte di macchine virtuali a pagamento fatturate tramite Microsoft Commercial Marketplace. Le risorse distribuite dal modello ARM della soluzione verranno sottoposte a provisioning nella sottoscrizione di Azure del cliente. Le macchine virtuali con pagamento in base al consumo (consumo) verranno sottoposte a transazione con il cliente tramite Microsoft, fatturate tramite la sottoscrizione di Azure del cliente.<br/> Nel caso del modello BYOL (Bring Your Own License), mentre Microsoft addebiterà i costi di infrastruttura sostenuti nella sottoscrizione del cliente, le transazioni relative alle tariffe delle licenze software verranno effettuate direttamente con il cliente.   |
|Disco rigido virtuale (VHD) compatibile con Azure  |   Le macchine virtuali devono essere compilate in Windows o Linux. Per altre informazioni, vedere: <ul> <li>[Creare un'offerta di applicazione Azure](./partner-center-portal/create-new-azure-apps-offer.md)(per i dischi rigidi virtuali di Windows)</li><li>[Distribuzioni Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (per dischi rigidi virtuali Linux).</li></ul> |
| Attribuzione dell'utilizzo da parte dei clienti | L'abilitazione dell'attribuzione dell'utilizzo da parte del cliente è necessaria in tutti i modelli di soluzione pubblicati in Azure Marketplace. Per altre informazioni sull'attribuzione dell'utilizzo da parte del cliente e su come abilitarla, vedere [Attribuzione dell'utilizzo da parte del cliente dei partner di Azure](./azure-partner-customer-usage-attribution.md).  |
| Usare Managed Disks | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) è l'opzione predefinita per i dischi salvati in permanenza di macchine virtuali IaaS in Azure. È necessario usare Managed Disks nei modelli di soluzione. <br> <br> 1. seguire le [istruzioni](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) e gli [esempi](https://github.com/Azure/azure-quickstart-templates) per usare Managed disks nei modelli ARM di Azure per aggiornare i modelli di soluzione. <br> <br> 2. seguire le istruzioni riportate di seguito per importare il VHD sottostante del Managed Disks in un account di archiviazione per pubblicare il disco rigido virtuale come immagine nel Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Passaggi successivi

- Se non è già stato [fatto, vedere informazioni su](https://azuremarketplace.microsoft.com/sell) Azure Marketplace.
- [Accedere al centro](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per i partner per creare o completare l'offerta.
- Per ulteriori informazioni, [creare un'offerta di applicazione Azure](./partner-center-portal/create-new-azure-apps-offer.md) .
