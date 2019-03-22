---
title: Fatturazione per i clienti e il chargeback in Azure Stack | Microsoft Docs
description: Informazioni su come recuperare le informazioni sull'utilizzo di risorse di Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: c1b0a1523e65014b153ff3edb0c22b53ce7fb8a3
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337851"
---
# <a name="usage-and-billing-in-azure-stack"></a>Informazioni sull'utilizzo e fatturazione in Azure Stack

Questo articolo descrive come gli utenti di Azure Stack vengono fatturati per l'utilizzo delle risorse e modalità di accesso per l'analitica e carica torna le informazioni di fatturazione.

Azure Stack raccoglie e raggruppa i dati di utilizzo per le risorse usate. Azure Stack inoltra quindi i dati di Azure Commerce. Commerce di Azure prevede la fatturazione per l'utilizzo di Azure Stack nello stesso modo che prevede la fatturazione per l'utilizzo di Azure.

È anche possibile ottenere i dati di utilizzo e l'esportazione per il proprio fatturazione o chargeback eseguire il sistema utilizzando un adattatore di fatturazione, o esportarli in un strumento di business intelligence come Microsoft Power BI.

## <a name="usage-pipeline"></a>Pipeline di utilizzo

Ogni provider di risorse in Azure Stack invia i dati di utilizzo per ogni utilizzo delle risorse. Periodicamente il servizio di utilizzo (su base oraria e giornaliera) aggrega i dati di utilizzo e lo archivia nel database di utilizzo. Gli operatori di Stack e gli utenti di Azure possono accedere ai dati sull'utilizzo archiviati tramite l'API di utilizzo delle risorse di Azure Stack.

Se hai [registrata l'istanza di Azure Stack con Azure](azure-stack-register.md), Azure Stack è configurato per inviare i dati di utilizzo per e-Commerce di Azure. Dopo aver caricati i dati in Azure, è possibile accedervi tramite il portale di fatturazione o dell'utilizzo delle risorse Azure API. Per altre informazioni su quali dati di utilizzo viene segnalati ad Azure, vedere [segnalazione errori dati di utilizzo](azure-stack-usage-reporting.md).  

L'immagine seguente mostra i componenti chiave della pipeline di utilizzo:

![Pipeline di utilizzo](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Le informazioni sull'utilizzo è possibile reperire e in che modo?

I provider di risorse di Azure Stack (ad esempio calcolo, archiviazione e rete) generano dati sull'utilizzo a intervalli di ore per ogni sottoscrizione. I dati di utilizzo contengono informazioni relative alla risorsa usata, ad esempio nome della risorsa, sottoscrizione usata e quantità utilizzata. Per altre informazioni sulle risorse di ID dei misuratori, vedere la [domande frequenti sull'API di utilizzo](azure-stack-usage-related-faq.md).

Dopo aver raccolto i dati di utilizzo, si tratta [segnalato ad Azure](azure-stack-usage-reporting.md) per generare una distinta, che può essere visualizzata tramite il portale di fatturazione di Azure.

> [!NOTE]  
> Segnalazione errori dati di utilizzo non è necessaria per Azure Stack Development Kit (ASDK) e per gli utenti del sistema integrato Azure Stack che acquistano una licenza in base al modello di capacità. Per altre informazioni sulla gestione delle licenze in Azure Stack, vedere la [creazione di pacchetti e prezzi foglio dati](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

Il portale di fatturazione di Azure Mostra i dati di utilizzo per le risorse addebitabili. Oltre alle risorse addebitabili, Azure Stack consente di acquisire dati di utilizzo per un set più ampio di risorse, che è possibile accedere nel proprio ambiente di Azure Stack tramite le API REST o i cmdlet di PowerShell. Operatori di Azure Stack possono ottenere i dati di utilizzo per tutte le sottoscrizioni utente. Singoli utenti possono ottenere solo i propri dettagli di utilizzo.

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Utilizzo di creazione di report per multi-tenant i provider di servizi Cloud

Multi-tenant Cloud Service Provider (CSP) che dispone di molti clienti che usano Azure Stack potrebbe voler segnalare ogni utilizzo del cliente separatamente, in modo che il provider, è possibile ricaricare l'utilizzo di diverse sottoscrizioni di Azure.

Ogni cliente ha la propria identità rappresentata da un altro tenant di Azure Active Directory (Azure AD). Azure Stack supporta assegnando una sottoscrizione di CSP per ogni tenant di Azure AD. È possibile aggiungere i tenant e le sottoscrizioni per la registrazione di Azure Stack base. Viene eseguita la registrazione di base per tutte le istanze di Azure Stack. Se una sottoscrizione non è registrata per un tenant, l'utente può comunque usare Azure Stack e il loro utilizzo verrà inviato per la sottoscrizione usata per la registrazione di base.

## <a name="next-steps"></a>Passaggi successivi

- [Registrare con Azure Stack](azure-stack-registration.md)
- [Segnalare i dati d'uso di Azure Stack in Azure](azure-stack-usage-reporting.md)
- [Utilizzo del provider di risorse API](azure-stack-provider-resource-api.md)
- [Utilizzo delle risorse API tenant](azure-stack-tenant-resource-usage-api.md)
- [Domande frequenti relative all'uso](azure-stack-usage-related-faq.md)