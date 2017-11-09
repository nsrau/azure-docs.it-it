---
title: Fatturazione clienti e Chargeback nello Stack di Azure | Documenti Microsoft
description: Informazioni su come recuperare informazioni sull'utilizzo di risorse dallo Stack di Azure.
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: a9afc7b6-43da-437b-a853-aab73ff14113
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: alfredop
ms.openlocfilehash: ea7510c239ee07a9a27f3e682e61a6b08eb5694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="usage-and-billing-in-azure-stack"></a>Informazioni sull'utilizzo e fatturazione nello Stack di Azure

Utilizzo rappresenta la quantità di risorse utilizzate da un utente. Azure Stack raccoglie informazioni sull'utilizzo per ogni utente che viene utilizzato per la fatturazione li. In questo articolo viene descritto come gli utenti dello Stack di Azure vengono fatturati per l'utilizzo delle risorse e l'accesso le informazioni di fatturazione per analitica chargeback, e così via.

Stack di Azure contiene l'infrastruttura di raccolta e aggregazione dei dati di utilizzo per tutte le risorse e di inoltrare questi dati a Azure commerce. È possibile accedere ai dati ed esportarlo in un sistema di fatturazione utilizzando un adattatore di fatturazione o esportarlo in uno strumento di business intelligence come Microsoft Power BI. Dopo l'esportazione, queste informazioni di fatturazione sono utilizzate per analitica o trasferite in un sistema di chargeback.

![Modello concettuale di un adattatore di fatturazione connessione Azure Stack a fatturazione applicazione](media/azure-stack-billing-and-chargeback/image1.png)

## <a name="usage-pipeline"></a>Pipeline di utilizzo

Ogni provider di risorse nello Stack di Azure genera dati di utilizzo per l'utilizzo delle risorse. Periodicamente il servizio di utilizzo (su base oraria o giornaliera) consente di aggregare i dati di utilizzo e archivia nel database di utilizzo. I dati di utilizzo archiviati accessibili dagli utenti e gli operatori di Azure Stack localmente utilizzando le API di utilizzo. 

Se dispone di [registrata l'istanza dello Stack di Azure con Azure](azure-stack-register.md), il Bridge di utilizzo è configurato per inviare i dati di utilizzo per Azure commerce. Dopo che i dati sono disponibili in Azure, è possibile accedere tramite il portale di fatturazione o di Azure-utilizzo dell'API. Consultare la [report dati di utilizzo](azure-stack-usage-reporting.md) argomento per ulteriori informazioni su quale tipo di utilizzo più vengono segnalati dati in Azure. 

La figura seguente mostra i componenti chiave nella pipeline di utilizzo:

![Pipeline di utilizzo](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Le informazioni sull'utilizzo è possibile trovare e in che modo?

Azure provider di risorse dello Stack, ad esempio di calcolo, archiviazione e rete, generare dati di utilizzo a intervalli di ore per ogni sottoscrizione. I dati di utilizzo contengono informazioni sulle risorse utilizzate, ad esempio il nome di risorsa, sottoscrizione utilizzata, quantità utilizzata e così via. Per ulteriori informazioni sulle risorse ID metri, consultare il [utilizzo domande frequenti su API](azure-stack-usage-related-faq.md) articolo. 

Dopo che sono stati raccolti i dati di utilizzo, è [segnalati in Azure](azure-stack-usage-reporting.md) per generare una distinta, che può essere visualizzata tramite il portale di fatturazione di Azure. 

> [!NOTE]
> Report di dati di utilizzo non è necessaria per Azure Stack Development Kit e per gli utenti del sistema Azure Stack integrato che acquistano una licenza in base al modello di capacità. Per ulteriori informazioni sulla gestione delle licenze nello Stack di Azure, vedere il [sui pacchetti e prezzi](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) foglio dati.

Il portale di fatturazione di Azure Mostra i dati di utilizzo solo per le risorse addebitabile. Oltre alle risorse addebitabile, Azure Stack acquisisce i dati di utilizzo per un set più ampio di risorse, è possibile accedere nell'ambiente dello Stack di Azure tramite le API REST o PowerShell. Gli operatori di Azure Stack possono recuperare i dati di utilizzo per tutte le sottoscrizioni dell'utente, mentre un utente può ottenere solo i dettagli sull'uso.

## <a name="retrieve-usage-information"></a>Recuperare informazioni sull'utilizzo

Per generare i dati di utilizzo, è necessario disporre di risorse che sono in esecuzione e usano attivamente il sistema, ad esempio, una macchina virtuale attiva o un account di archiviazione contenente alcuni dati e così via. Se non si è certi se si hanno risorse in esecuzione in Azure Marketplace di Stack, distribuire una macchina virtuale (VM) e verifica la macchina virtuale pannello per assicurarsi che il monitoraggio è in esecuzione. Utilizzare i cmdlet di PowerShell seguenti per visualizzare i dati di utilizzo:

1. [Installare PowerShell per Azure dello Stack.](azure-stack-powershell-install.md)
2. [Configurare l'utente di Azure Stack](user/azure-stack-powershell-configure-user.md) o [dell'operatore Azure Stack](azure-stack-powershell-configure-admin.md) ambiente di PowerShell 

3. Per recuperare i dati di utilizzo, utilizzare il [Get UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) cmdlet di PowerShell:

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

## <a name="next-steps"></a>Passaggi successivi

[Report di dati di utilizzo dello Stack di Azure in Azure](azure-stack-usage-reporting.md)

[Utilizzo del provider di risorse API](azure-stack-provider-resource-api.md)

[Utilizzo delle risorse API tenant](azure-stack-tenant-resource-usage-api.md)

[Domande frequenti relative all'uso](azure-stack-usage-related-faq.md)

