---
title: Considerazioni sul programma Cloud Solution Provider
description: Quando si usa la gestione delle risorse delegata di Azure, è importante considerare sicurezza e controllo di accesso.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 76df2942b176fd7d0e785213e44655ffd11da410
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286213"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse e programma Cloud Solution Provider

I partner [CSP (Cloud Solution Provider)](https://docs.microsoft.com/partner-center/csp-overview) possono già accedere alle sottoscrizioni di Azure create per i clienti tramite il programma CSP usando la funzionalità [Amministra per conto terzi](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO). Questo accesso consente di supportare, configurare e gestire le sottoscrizioni dei clienti direttamente.

Con Azure Lighthouse è possibile usare la gestione risorse delegate di Azure insieme ad Amministra per conto terzi. In questo modo è possibile migliorare la sicurezza e ridurre l'accesso non necessario abilitando autorizzazioni più granulari per gli utenti. È inoltre possibile aumentare l'efficienza e la scalabilità, in quanto gli utenti possono lavorare con più sottoscrizioni dei clienti usando un unico account di accesso nel tenant.

## <a name="administer-on-behalf-of-aobo"></a>Funzionalità Amministra per conto terzi

Con la funzionalità Amministra per conto terzi, gli utenti con il ruolo [Agente amministratore](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) nel tenant avranno l'accesso Amministra per conto terzi alle sottoscrizioni di Azure create tramite il programma CSP. Tutti gli utenti che devono accedere alle sottoscrizioni di qualsiasi cliente devono essere membri di questo gruppo. La funzionalità Amministra per conto terzi non offre la possibilità di creare gruppi distinti per clienti diversi o di abilitare ruoli diversi per gruppi o utenti.

![Gestione dei tenant tramite la funzionalità Amministra per conto terzi](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Gestione risorse delegate di Azure

Usando la gestione delle risorse delegate di Azure, è possibile assegnare gruppi diversi a clienti o ruoli diversi, come illustrato nel diagramma seguente. Poiché gli utenti avranno il livello di accesso appropriato tramite la gestione delle risorse delegate di Azure, è possibile ridurre il numero di utenti che hanno il ruolo Agente amministratore e che hanno quindi l'accesso Amministra per conto terzi. Questo garantisce una maggiore sicurezza limitando l'accesso non necessario alle risorse dei clienti. Offre anche maggiori possibilità di gestire più clienti su larga scala.

Per eseguire l'onboarding di una sottoscrizione creata tramite il programma CSP, seguire la procedura descritta in [Eseguire l'onboarding di una sottoscrizione nella gestione risorse delegate di Azure](../how-to/onboard-customer.md). Tutti gli utenti che hanno il ruolo Agente amministratore nel tenant possono eseguire questa operazione di onboarding.

![Gestione dei tenant tramite la funzionalità Amministra per conto terzi e la gestione risorse delegate di Azure](../media/csp-2.jpg)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- Informazioni su come [eseguire l'onboarding di una sottoscrizione di un cliente nella gestione risorse delegate di Azure](../how-to/onboard-customer.md).
- Informazioni sul [programma Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview).
