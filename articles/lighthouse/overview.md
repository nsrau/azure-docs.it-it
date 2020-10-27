---
title: Informazioni su Azure Lighthouse
description: Azure Lighthouse consente ai provider di servizi di offrire servizi gestiti ai clienti con maggiore automazione ed efficienza su larga scala.
ms.date: 10/19/2020
ms.topic: overview
ms.openlocfilehash: a76606ff48a09c0c31584882e3d2aa164ec97325
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203790"
---
# <a name="what-is-azure-lighthouse"></a>Informazioni su Azure Lighthouse

Azure Lighthouse consente una gestione tra tenant e multi-tenant, offrendo una maggiore automazione, scalabilità e una migliore governance tra risorse e tenant.

Con Azure Lighthouse, i provider di servizi possono offrire servizi gestiti con strumenti di gestione completi e affidabili integrati nella piattaforma Azure. I clienti mantengono il controllo su chi può accedere al tenant, a quali risorse è possibile accedere e quali azioni possono essere eseguite. Questa offerta può anche agevolare le [organizzazioni IT aziendali](concepts/enterprise.md) che gestiscono le risorse tra più tenant.

![Diagramma di panoramica di Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Vantaggi

Azure Lighthouse consente ai provider di servizi di creare e distribuire in modo efficiente i servizi gestiti. Questa caratteristica offre i vantaggi seguenti:

- **Gestione su larga scala** : l'engagement dei clienti e le operazioni del ciclo di vita per gestire le risorse dei clienti sono più semplici e più scalabili. Le API, gli strumenti di gestione e i flussi di lavoro esistenti possono essere usati con risorse delegate, tra cui macchine virtuali ospitate al di fuori di Azure, indipendentemente dalle aree in cui si trovano.
- **Maggiore visibilità e controllo per i clienti** : i clienti hanno un controllo preciso sugli ambiti che delegano per la gestione e sulle autorizzazioni consentite. Possono controllare le azioni del provider di servizi e rimuovere completamente l'accesso, se necessario.
- **Strumenti di piattaforma completi e unificati** : l'esperienza con gli strumenti si applica ai principali scenari dei provider di servizi, inclusi più modelli di licenza, ad esempio EA, CSP e con pagamento in base al consumo. Azure Lighthouse è compatibile con gli strumenti e con le API esistenti, con i modelli di licenze, con le [applicazioni gestite di Azure](concepts/managed-applications.md) e con programmi per i partner come [Cloud Solution Provider (CSP)](/partner-center/csp-overview). È possibile integrare Azure Lighthouse nei flussi di lavoro e nelle applicazioni esistenti, nonché tenere traccia dell'impatto sull'engagement dei clienti [collegando l'ID partner](./how-to/partner-earned-credit.md).

Non sono previsti costi aggiuntivi associati all'uso di Azure Lighthouse per gestire le risorse di Azure. Qualsiasi cliente o partner di Azure può usare Azure Lighthouse.

## <a name="capabilities"></a>Capabilities

Azure Lighthouse include più modi per semplificare l'engagement e la gestione:

- **Gestione risorse delegate di Azure** : [gestire in modo sicuro le risorse di Azure dei clienti all'interno del tenant](concepts/azure-delegated-resource-management.md), senza la necessità di cambiare il contesto e i piani di controllo. Le sottoscrizioni e i gruppi di risorse del cliente possono essere delegati a utenti e ruoli specifici nel tenant di gestione, con la possibilità di rimuovere l'accesso se necessario.
- **Nuove esperienze con il portale di Azure** : visualizzare le informazioni tra tenant nella nuova pagina [**Clienti personali**](how-to/view-manage-customers.md) del portale di Azure. Una pagina [**Provider di servizi**](how-to/view-manage-service-providers.md) corrispondente consente ai clienti di visualizzare e gestire l'accesso per i provider di servizi.
- **Modelli di Azure Resource Manager** : usare i modelli di Resource Manager per l'[onboarding di risorse delegate dei clienti](how-to/onboard-customer.md) e per [eseguire attività di gestione tra tenant](samples/index.md).
- **Offerte di servizi gestiti in Azure Marketplace** : [proporre servizi ai clienti](concepts/managed-services-offers.md) tramite offerte private o pubbliche ed eseguirne automaticamente l'onboarding in Azure Lighthouse.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [gestione risorse delegate di Azure](concepts/azure-delegated-resource-management.md).
- Esplorare le [esperienze di gestione tra tenant](concepts/cross-tenant-management-experience.md).
- Vedere come [usare Azure Lighthouse all'interno di un'azienda](concepts/enterprise.md).
