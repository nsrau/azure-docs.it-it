---
title: Considerazioni sul programma Cloud Solution Provider
description: Per i partner CSP, la gestione risorse delegate di Azure consente di migliorare la sicurezza e il controllo abilitando autorizzazioni granulari.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 27bb1c4f82fe3c9ff8abe74b74bef19f16a8a874
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967155"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse e programma Cloud Solution Provider

I partner [CSP (Cloud Solution Provider)](/partner-center/csp-overview) possono già accedere alle sottoscrizioni di Azure create per i clienti tramite il programma CSP usando la funzionalità [Amministra per conto terzi](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO). Questo accesso consente di supportare, configurare e gestire le sottoscrizioni dei clienti direttamente.

Con [Azure Lighthouse](../overview.md) è possibile usare la gestione risorse delegate di Azure insieme ad Amministra per conto terzi. In questo modo è possibile migliorare la sicurezza e ridurre l'accesso non necessario abilitando autorizzazioni più granulari per gli utenti. È inoltre possibile aumentare l'efficienza e la scalabilità, in quanto gli utenti possono lavorare con più sottoscrizioni dei clienti usando un unico account di accesso nel tenant.

> [!TIP]
> Per proteggere le risorse dei clienti, assicurarsi di esaminare e seguire le [procedure di sicurezza consigliate](recommended-security-practices.md) insieme ai [requisiti di sicurezza dei partner](/partner-center/partner-security-requirements).

## <a name="administer-on-behalf-of-aobo"></a>Funzionalità Amministra per conto terzi

Con la funzionalità Amministra per conto terzi, gli utenti con il ruolo [Agente amministratore](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) nel tenant avranno l'accesso Amministra per conto terzi alle sottoscrizioni di Azure create tramite il programma CSP. Tutti gli utenti che devono accedere alle sottoscrizioni di qualsiasi cliente devono essere membri di questo gruppo. La funzionalità Amministra per conto terzi non offre la possibilità di creare gruppi distinti per clienti diversi o di abilitare ruoli diversi per gruppi o utenti.

![Diagramma che illustra la gestione dei tenant mediante AOBO.](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Gestione risorse delegate di Azure

Con Azure Lighthouse è possibile assegnare gruppi diversi a clienti o ruoli diversi, come illustrato nella figura seguente. Poiché gli utenti avranno il livello di accesso appropriato tramite la gestione delle risorse delegata di Azure, è possibile ridurre il numero di utenti che hanno il ruolo di agente amministratore e quindi dispongono dell'accesso completo come amministratore per conto terzi. Questo consente di migliorare la sicurezza limitando l'accesso non necessario alle risorse dei clienti e inoltre offre maggiore flessibilità per la gestione di più clienti su larga scala.

L'onboarding di una sottoscrizione creata tramite il programma CSP segue i passaggi descritti in caricare [una sottoscrizione al Faro di Azure](../how-to/onboard-customer.md). Tutti gli utenti che hanno il ruolo Agente amministratore nel tenant possono eseguire questa operazione di onboarding.

![Diagramma che illustra la gestione dei tenant mediante AOBO e la gestione delle risorse delegata di Azure.](../media/csp-2.jpg)

> [!TIP]
> Le [offerte del servizio gestito](managed-services-offers.md) con piani privati non sono supportate con le sottoscrizioni stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP). È possibile caricare queste sottoscrizioni in Azure Lighthouse [usando Azure Resource Manager modelli](../how-to/onboard-customer.md).

> [!NOTE]
> La [pagina **clienti** nella portale di Azure](../how-to/view-manage-customers.md) include ora una sezione **Cloud Solution Provider (anteprima)** , che visualizza le informazioni di fatturazione e le risorse per i clienti CSP che hanno [firmato il contratto di servizio Microsoft (MCA)](/partner-center/confirm-customer-agreement) e si trovano nel [piano Azure](/partner-center/azure-plan-get-started). Per altre informazioni, vedere [Introduzione all'account di fatturazione del Contratto Microsoft Partner](../../cost-management-billing/understand/mpa-overview.md).
>
> I clienti CSP possono essere visualizzati in questa sezione anche se sono stati caricati in Azure Lighthouse. In caso affermativo, verranno visualizzati anche nella sezione **Customers** , come descritto in [View and manage customers and Delegated Resources](../how-to/view-manage-customers.md). Analogamente, non è necessario che un cliente CSP venga visualizzato nella sezione del **provider di soluzioni cloud (anteprima)** dei **clienti** per caricarli nel Faro di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- Informazioni su come eseguire [l'onboarding di una sottoscrizione al Faro di Azure](../how-to/onboard-customer.md).
- Informazioni sul [programma Cloud Solution Provider](/partner-center/csp-overview).
