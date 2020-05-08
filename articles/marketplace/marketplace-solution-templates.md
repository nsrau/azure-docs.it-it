---
title: Guida alla pubblicazione per il modello di soluzione per applicazioni Azure offerte-Azure Marketplace
description: Questo articolo descrive i requisiti per la pubblicazione di modelli di soluzioni in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: f62b3478c5c711423913b5918886b43b79ac691d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858317"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Guida alla pubblicazione per le offerte del modello di soluzione per applicazioni Azure

Questo articolo illustra i requisiti per la pubblicazione delle offerte di modelli di soluzione, un modo per pubblicare le offerte di applicazioni Azure in Azure Marketplace. Il tipo di offerta del modello di soluzione richiede un [modello di Azure Resource Manager (modello ARM)](../azure-resource-manager/templates/overview.md) per distribuire automaticamente l'infrastruttura della soluzione.

Usare il tipo di offerta del *modello di soluzione* applicazione Azure nelle condizioni seguenti:

- La soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre a una singola macchina virtuale (VM), ad esempio una combinazione di VM, rete e risorse di archiviazione.
- I clienti gestiranno la soluzione autonomamente.

La chiamata all'azione che un cliente vede per questo tipo di offerta è *ora*disponibile.

## <a name="requirements-for-solution-template-offers"></a>Requisiti per le offerte del modello di soluzione

| **Requisiti** | **Dettagli**  |
| ---------------  | -----------  |
|Fatturazione e misurazione    |  Le offerte del modello di soluzione non sono offerte di transazione, ma possono essere usate per distribuire offerte di macchine virtuali a pagamento fatturate tramite il Marketplace commerciale Microsoft. Le risorse distribuite dal modello ARM della soluzione vengono configurate nella sottoscrizione di Azure del cliente. Le macchine virtuali con pagamento in base al consumo vengono sottoposte a transazione con il cliente tramite Microsoft e fatturate tramite la sottoscrizione di Azure del cliente.<br/> Per la fatturazione BYOL (Bring your own License), sebbene Microsoft fattura i costi di infrastruttura sostenuti per la sottoscrizione del cliente, è necessario effettuare direttamente le spese di licenza software con il cliente.   |
|Disco rigido virtuale (VHD) compatibile con Azure  |   Le macchine virtuali devono essere compilate in Windows o Linux. Per altre informazioni, vedere: <ul> <li>[Creare un'offerta di applicazione Azure](./partner-center-portal/create-new-azure-apps-offer.md) per i dischi rigidi virtuali di Windows.</li><li>[Distribuzioni Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (per dischi rigidi virtuali Linux).</li></ul> |
| Attribuzione dell'uso da parte dei clienti | L'abilitazione dell'attribuzione dell'utilizzo dei clienti è obbligatoria in tutti i modelli di soluzione pubblicati in Azure Marketplace. Per ulteriori informazioni sull'attribuzione dell'utilizzo dei clienti e su come abilitarla, vedere l' [attribuzione dell'utilizzo dei clienti partner di Azure](./azure-partner-customer-usage-attribution.md).  |
| Usare i dischi gestiti | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) è l'opzione predefinita per i dischi salvati in modo permanente di macchine virtuali IaaS (Infrastructure as a Service) in Azure. È necessario usare Managed disks nei modelli di soluzione. <ul><li>Per aggiornare i modelli di soluzione, seguire le istruzioni in [usare Managed disks in Azure Resource Manager Templates](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)e usare gli [esempi](https://github.com/Azure/azure-quickstart-templates)forniti.<br><br> </li><li>Per pubblicare il disco rigido virtuale come immagine in Azure Marketplace, importare il disco rigido virtuale sottostante dei dischi gestiti in un account di archiviazione usando uno dei metodi seguenti:<ul><li>[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul></ul> |

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, Scopri come [espandere il tuo business cloud con Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Per eseguire la registrazione e iniziare a usare il centro per i partner:

- [Accedere al centro](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per i partner per creare o completare l'offerta.
- Per altre informazioni, vedere [creare un'offerta di applicazione Azure](./partner-center-portal/create-new-azure-apps-offer.md) .
