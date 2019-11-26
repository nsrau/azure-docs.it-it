---
title: Considerazioni sul programma Cloud Solution Provider
description: Per i partner CSP, la gestione risorse delegate di Azure consente di migliorare la sicurezza e il controllo abilitando autorizzazioni granulari.
ms.date: 10/23/2019
ms.topic: conceptual
ms.openlocfilehash: 7e1e371d8c31c45828ee0565545cb40145b40e92
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463969"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse e programma Cloud Solution Provider

I partner [CSP (Cloud Solution Provider)](https://docs.microsoft.com/partner-center/csp-overview) possono già accedere alle sottoscrizioni di Azure create per i clienti tramite il programma CSP usando la funzionalità [Amministra per conto terzi](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO). Questo accesso consente di supportare, configurare e gestire le sottoscrizioni dei clienti direttamente.

Con [Azure Lighthouse](../overview.md) è possibile usare la gestione risorse delegate di Azure insieme ad Amministra per conto terzi. In questo modo è possibile migliorare la sicurezza e ridurre l'accesso non necessario abilitando autorizzazioni più granulari per gli utenti. È inoltre possibile aumentare l'efficienza e la scalabilità, in quanto gli utenti possono lavorare con più sottoscrizioni dei clienti usando un unico account di accesso nel tenant.

> [!TIP]
> Per proteggere le risorse dei clienti, assicurarsi di esaminare e seguire le [procedure di sicurezza consigliate](recommended-security-practices.md) insieme ai [requisiti di sicurezza dei partner](https://docs.microsoft.com/partner-center/partner-security-requirements).

## <a name="administer-on-behalf-of-aobo"></a>Funzionalità Amministra per conto terzi

Con la funzionalità Amministra per conto terzi, gli utenti con il ruolo [Agente amministratore](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) nel tenant avranno l'accesso Amministra per conto terzi alle sottoscrizioni di Azure create tramite il programma CSP. Tutti gli utenti che devono accedere alle sottoscrizioni di qualsiasi cliente devono essere membri di questo gruppo. La funzionalità Amministra per conto terzi non offre la possibilità di creare gruppi distinti per clienti diversi o di abilitare ruoli diversi per gruppi o utenti.

![Gestione dei tenant tramite la funzionalità Amministra per conto terzi](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Gestione risorse delegate di Azure

Usando la gestione delle risorse delegate di Azure, è possibile assegnare gruppi diversi a clienti o ruoli diversi, come illustrato nel diagramma seguente. Poiché gli utenti avranno il livello di accesso appropriato tramite la gestione delle risorse delegate di Azure, è possibile ridurre il numero di utenti che hanno il ruolo Agente amministratore e che hanno quindi l'accesso Amministra per conto terzi. Questo garantisce una maggiore sicurezza limitando l'accesso non necessario alle risorse dei clienti. Offre anche maggiori possibilità di gestire più clienti su larga scala.

Per eseguire l'onboarding di una sottoscrizione creata tramite il programma CSP, seguire la procedura descritta in [Eseguire l'onboarding di una sottoscrizione nella gestione risorse delegate di Azure](../how-to/onboard-customer.md). Tutti gli utenti che hanno il ruolo Agente amministratore nel tenant possono eseguire questa operazione di onboarding.

![Gestione dei tenant tramite la funzionalità Amministra per conto terzi e la gestione risorse delegate di Azure](../media/csp-2.jpg)

> [!NOTE]
> La pagina [**Clienti personali** nel portale di Azure](../how-to/view-manage-customers.md) include ora una sezione **Cloud Solution Provider (anteprima)** , che visualizza le informazioni e le risorse di fatturazione per i clienti CSP che hanno [firmato il Contratto del cliente Microsoft](https://docs.microsoft.com/partner-center/confirm-customer-agreement) e sottoscritto il piano di Azure. Per altre informazioni, vedere [Introduzione all'account di fatturazione del Contratto Microsoft Partner](https://docs.microsoft.com/azure/billing/mpa-overview).
>
> I clienti CSP possono essere visualizzati in questa sezione indipendentemente dal fatto che siano stati sottoposti a onboarding per la gestione risorse delegate di Azure. In questo caso, verranno visualizzati anche nella sezione **Clienti**, come descritto in [Visualizzare e gestire clienti e risorse delegate](../how-to/view-manage-customers.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- Informazioni su come [eseguire l'onboarding di una sottoscrizione di un cliente nella gestione risorse delegate di Azure](../how-to/onboard-customer.md).
- Informazioni sul [programma Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview).
