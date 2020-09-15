---
title: Informazioni su Azure Lighthouse
description: Azure Lighthouse consente ai provider di servizi di offrire servizi gestiti ai clienti con maggiore automazione ed efficienza su larga scala.
ms.date: 08/19/2020
ms.topic: overview
ms.openlocfilehash: 22bec7ec8944a11ce0cfdf51776f1f193a1aedaa
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488814"
---
# <a name="what-is-azure-lighthouse"></a>Informazioni su Azure Lighthouse

Azure Lighthouse consente una gestione tra tenant e multi-tenant, offrendo una maggiore automazione, scalabilità e una migliore governance tra risorse e tenant. Con Azure Lighthouse, i provider di servizi possono offrire servizi gestiti con strumenti di gestione completi e affidabili integrati nella piattaforma Azure. Questa offerta può anche agevolare le organizzazioni IT aziendali che gestiscono le risorse tra più tenant.

![Diagramma di panoramica di Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Vantaggi

Azure Lighthouse consente di creare e distribuire in modo efficiente e proficuo i servizi gestiti. Questo approccio offre i vantaggi seguenti:

- **Gestione su larga scala**: l'engagement dei clienti e le operazioni del ciclo di vita per gestire le risorse dei clienti sono più semplici e più scalabili. Le API, gli strumenti di gestione e i flussi di lavoro esistenti possono essere usati con risorse delegate, indipendentemente dalle aree in cui si trovano.
- **Maggiore visibilità e precisione per i clienti**: i clienti avranno una maggiore visibilità sulle azioni e un controllo preciso sull'ambito delegato per la gestione, inclusa la possibilità di rimuovere completamente l'accesso, mentre l'IP viene mantenuto.
- **Strumenti di piattaforma completi e unificati**: l'esperienza con gli strumenti si applica ai principali scenari dei provider di servizi, inclusi più modelli di licenza, ad esempio EA, CSP e con pagamento in base al consumo. Le nuove funzionalità usano gli strumenti e le API esistenti, i modelli di licenza e i programmi per i partner, ad esempio il [programma Cloud Solution Provider (CSP)](/partner-center/csp-overview). È possibile integrare Azure Lighthouse nei flussi di lavoro e nelle applicazioni esistenti, nonché tenere traccia dell'impatto sugli engagement dei clienti e ricevere il credito ottenuto dai partner [collegando l'ID partner](./how-to/partner-earned-credit.md).

Non sono previsti costi aggiuntivi associati all'uso di Azure Lighthouse per gestire le risorse di Azure. Qualsiasi cliente o partner di Azure può usare Azure Lighthouse.

## <a name="capabilities"></a>Capabilities

Azure Lighthouse include più modi per semplificare l'engagement e la gestione:

- **Gestione risorse delegate di Azure**: gestire in modo sicuro le risorse di Azure dei clienti dal tenant, senza dover cambiare il contesto e i piani di controllo. Le sottoscrizioni e i gruppi di risorse possono essere delegati a utenti e ruoli specifici nel tenant di gestione, con la possibilità di rimuovere l'accesso in base alle esigenze. Per altre informazioni, vedere [Gestione risorse delegate di Azure](concepts/azure-delegated-resource-management.md).
- **Nuove esperienze con il portale di Azure**: visualizzare le informazioni tra tenant nella nuova pagina [**Clienti personali** ](./how-to/view-manage-customers.md) del portale di Azure. Una pagina [**Provider di servizi**](how-to/view-manage-service-providers.md) corrispondente consente ai clienti di visualizzare e gestire l'accesso per i provider di servizi.
- **Modelli di Azure Resource Manager**: i modelli consentono di eseguire attività di gestione tra tenant e l'onboarding di risorse dei clienti delegate. Per altre informazioni, vedere il [repository di esempi](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) ed [Eseguire l'onboarding di un cliente in Azure Lighthouse](how-to/onboard-customer.md).
- **Offerte di servizi gestiti in Azure Marketplace**: fornire servizi ai clienti tramite offerte private o pubbliche ed eseguirne automaticamente l'onboarding in Azure Lighthouse. Per altre informazioni, vedere [Offerte di servizi gestiti in Azure Marketplace](concepts/managed-services-offers.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [gestione risorse delegate di Azure](concepts/azure-delegated-resource-management.md).
- Esplorare le [esperienze di gestione tra tenant](concepts/cross-tenant-management-experience.md).
- Vedere come [usare Azure Lighthouse all'interno di un'azienda](concepts/enterprise.md).
