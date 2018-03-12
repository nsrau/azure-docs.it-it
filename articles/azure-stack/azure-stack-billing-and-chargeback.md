---
title: Fatturazione clienti e Chargeback nello Stack di Azure | Documenti Microsoft
description: Informazioni su come recuperare informazioni sull'utilizzo di risorse dallo Stack di Azure.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: eca335797f48b7c44351655f17c8b6499f3d5999
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="usage-and-billing-in-azure-stack"></a>Informazioni sull'utilizzo e fatturazione nello Stack di Azure

In questo articolo viene descritto come gli utenti dello Stack di Azure vengono fatturati per l'utilizzo delle risorse. Viene illustrato come accedere le informazioni di fatturazione per analitica e addebito.

Stack Azure raccoglie e gruppi di dati di utilizzo per tutte le risorse usate e inoltra i dati a Azure Commerce. Commerce Azure addebita è per l'utilizzo dello Stack di Azure nello stesso modo come verrebbe addebito per l'utilizzo di Azure.

È anche possibile ottenere dati di utilizzo e di esportazione, alla fatturazione o addebito eseguire il sistema utilizzando un adattatore di fatturazione o esportarlo in uno strumento di business intelligence, ad esempio Microsoft Power BI e usarlo per analitica.


## <a name="usage-pipeline"></a>Pipeline di utilizzo

Ogni provider di risorse nello Stack di Azure genera dati di utilizzo per ogni utilizzo delle risorse. Periodicamente il servizio di utilizzo (oraria e giornaliera) consente di aggregare i dati di utilizzo e archivia nel database di utilizzo. Azure operatori dello Stack e gli utenti possono accedere ai dati di utilizzo archiviati tramite le API di utilizzo della risorsa di Azure Stack. 

Se dispone di [registrata l'istanza dello Stack di Azure con Azure](azure-stack-register.md), Stack di Azure è configurato per inviare i dati di utilizzo per Azure Commerce. Dopo aver caricati i dati in Azure, è possibile accedere tramite il portale di fatturazione o API di utilizzo della risorsa di Azure. Consultare la [report dati di utilizzo](azure-stack-usage-reporting.md) argomento per ulteriori informazioni su quale tipo di utilizzo più vengono segnalati dati in Azure.  

La figura seguente mostra i componenti chiave nella pipeline di utilizzo: 

![Pipeline di utilizzo](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Le informazioni sull'utilizzo è possibile trovare e in che modo?

Azure provider di risorse dello Stack, ad esempio di calcolo, archiviazione e rete, generare dati di utilizzo a intervalli di ore per ogni sottoscrizione. I dati di utilizzo contengono informazioni sulla risorsa utilizzata come nome di risorsa, sottoscrizione utilizzata e quantità utilizzata. Per ulteriori informazioni sulle risorse ID metri, consultare il [domande frequenti sull'API di utilizzo](azure-stack-usage-related-faq.md) articolo.

Dopo che sono stati raccolti i dati di utilizzo, è [segnalati in Azure](azure-stack-usage-reporting.md) per generare una distinta, che può essere visualizzata tramite il portale di fatturazione di Azure. 


> [!NOTE]
> Report di dati di utilizzo non è necessaria per Azure Stack Development Kit e per gli utenti del sistema Azure Stack integrato che acquistano una licenza in base al modello di capacità. Per ulteriori informazioni sulla gestione delle licenze nello Stack di Azure, vedere il [sui pacchetti e prezzi](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) foglio dati.

Il portale di fatturazione di Azure Mostra i dati di utilizzo per le risorse addebitabile. Oltre alle risorse addebitabile, Azure Stack acquisisce i dati di utilizzo per un set più ampio di risorse, è possibile accedere nell'ambiente dello Stack di Azure tramite le API REST o PowerShell. Gli operatori di Azure Stack possono ottenere i dati di utilizzo per tutte le sottoscrizioni dell'utente. Singoli utenti possono ottenere solo i dettagli sull'uso. 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Utilizzo della creazione di report per multi-tenant provider di servizi Cloud

Multitenant Provider del servizio Cloud (CSP) che dispone di molti clienti che utilizzano Azure Stack potrebbe voler report sull'utilizzo di ogni cliente separatamente, in modo che il provider, è possibile ricaricare l'utilizzo di diverse sottoscrizioni di Azure. 

Ogni cliente da utilizzare per la propria identità rappresentata da un tenant di Azure Active Directory (Azure AD) diverso. Stack di Azure supporta l'assegnazione una sottoscrizione di CSP per ogni tenant di Azure AD. È possibile aggiungere i tenant e le relative sottoscrizioni per la registrazione della base dello Stack di Azure. La registrazione di base viene eseguita per tutti gli stack di Azure. Se una sottoscrizione non è registrata per un tenant, l'utente può comunque utilizzare Azure Stack e verrà inviato il loro utilizzo per la sottoscrizione utilizzata per la registrazione di base. 


## <a name="next-steps"></a>Passaggi successivi

[Registrare con Azure Stack](azure-stack-registration.md)

[Report di dati di utilizzo dello Stack di Azure in Azure](azure-stack-usage-reporting.md)

[Utilizzo del provider di risorse API](azure-stack-provider-resource-api.md)

[Utilizzo delle risorse API tenant](azure-stack-tenant-resource-usage-api.md)

[Domande frequenti relative all'uso](azure-stack-usage-related-faq.md)