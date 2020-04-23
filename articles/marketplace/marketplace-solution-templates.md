---
title: Guida alla pubblicazione del modello di soluzione delle applicazioni di Azure Azure Marketplace
description: Questo articolo descrive i requisiti per pubblicare un modello di soluzione in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084856"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Applicazioni di Azure: requisiti di pubblicazione dell'offerta di modelli di soluzioneAzure Applications: Solution template offer publishing requirements

Questo articolo illustra i requisiti per il tipo di offerta del modello di soluzione, ovvero un modo per pubblicare un'offerta di applicazione azure in Azure Marketplace.This article explains the requirements for the solution template offer type, which is one way to publish an Azure application offer in the Azure Marketplace. Il tipo di offerta del modello di soluzione richiede un modello di [Azure Resource Manager (modello ARM)](../azure-resource-manager/templates/overview.md) per distribuire automaticamente l'infrastruttura della soluzione.

Usare il tipo di offerta del modello di soluzione dell'applicazione Azure quando sono necessarie le condizioni seguenti:Use the Azure application solution template offer type when the following conditions are required:

- La soluzione richiede ulteriore automazione della distribuzione e della configurazione oltre a una singola macchina virtuale, ad esempio una combinazione di macchine virtuali, rete e risorse di archiviazione.
- Il vostro cliente sta per gestire la soluzione se stessi.

L'invito all'azione visualizzato da un utente per questo tipo di offerta è "Get It Now".

## <a name="requirements-for-solution-template-offers"></a>Requisiti per le offerte di modelli di soluzioneRequirements for solution template offers

| **Requisiti** | **Dettagli**  |
| ---------------  | -----------  |
|Fatturazione e misurazione    |  Le offerte dei modelli di soluzione non sono offerte di transazioni, ma possono essere usate per distribuire le offerte di macchine virtuali a pagamento fatturate tramite il marketplace commerciale Microsoft.Solution template offers are not transact offers, but can be used to deploy paid VM offers billed through the Microsoft commercial marketplace. Le risorse distribuite dal modello ARM della soluzione verranno sottoposte a provisioning nella sottoscrizione di Azure del cliente. Le macchine virtuali PAYGO (Pay-as-you-go) verranno effettuate transazioni con il cliente tramite Microsoft, fatturate tramite la sottoscrizione di Azure del cliente.<br/> Nel caso del modello BYOL (Bring Your Own License), mentre Microsoft addebiterà i costi di infrastruttura sostenuti nella sottoscrizione del cliente, le transazioni relative alle tariffe delle licenze software verranno effettuate direttamente con il cliente.   |
|Disco rigido virtuale (VHD) compatibile con Azure  |   Le macchine virtuali devono essere compilate in Windows o Linux. Per altre informazioni, vedere: <ul> <li>[Creare un'offerta di applicazione Azure](./partner-center-portal/create-new-azure-apps-offer.md)(per i dischi rigidi virtuali di Windows)Create an Azure application offer (for Windows VHDs)</li><li>[Distribuzioni Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (per i dischi rigidi virtuali Linux).</li></ul> |
| Attribuzione dell'utilizzo da parte dei clienti | L'abilitazione dell'attribuzione dell'utilizzo da parte del cliente è necessaria in tutti i modelli di soluzione pubblicati in Azure Marketplace. Per altre informazioni sull'attribuzione dell'utilizzo da parte del cliente e su come abilitarla, vedere [Attribuzione dell'utilizzo da parte del cliente dei partner di Azure](./azure-partner-customer-usage-attribution.md).  |
| Usare Managed Disks | [Dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) è l'opzione predefinita per i dischi persistenti delle macchine virtuali IaaS in Azure.Managed Disks is the default option for persisted disks of IaaS VMs in Azure. È necessario utilizzare dischi gestiti nei modelli di soluzione. <br> <br> 1. Seguire le [indicazioni](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) e [gli esempi](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) per l'uso di dischi gestiti nei modelli di Azure ARM per aggiornare i modelli di soluzione. <br> <br> 2. Seguire le istruzioni riportate di seguito per importare il disco rigido virtuale sottostante dei dischi gestiti in un account di archiviazione per pubblicare il disco rigido virtuale come immagine nel Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Passaggi successivi

- Se non è già stato fatto, vedere Azure Marketplace.If you haven't already done so, [learn](https://azuremarketplace.microsoft.com/sell) about the Azure Marketplace.
- [Accedi al Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare o completare la tua offerta.
- [Creare un'offerta di applicazione Azure](./partner-center-portal/create-new-azure-apps-offer.md) per altre informazioni.
