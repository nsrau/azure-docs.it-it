---
title: Azure Lighthouse in scenari aziendali
description: Le funzionalità di Azure Lighthouse possono essere usate per semplificare la gestione tra tenant all'interno di un'azienda che usa più tenant di Azure AD.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 09/25/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: fbd87cc801824729025feb7aefa411ac38048949
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266678"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse in scenari aziendali

Lo scenario più comune per Azure Lighthouse è quello di un provider di servizi che gestisce le risorse nei tenant di Azure Active Directory (Azure AD) dei clienti. Le funzionalità di Azure Lighthouse possono tuttavia essere usate anche per semplificare la gestione tra tenant all'interno di un'azienda che usa più tenant di Azure AD.

## <a name="single-vs-multiple-tenants"></a>Tenant singolo o più tenant

Per la maggior parte delle organizzazioni, la gestione è più semplice con un singolo tenant di Azure AD. L'inclusione di tutte le risorse in un tenant consente la centralizzazione delle attività di gestione da parte di entità servizio, gruppi di utenti o utenti designati all'interno di tale tenant. Quando possibile, è consigliabile usare un unico tenant per l'organizzazione.

Al tempo stesso, in determinate situazioni potrebbe essere necessario per un'organizzazione gestire più tenant di Azure AD. In alcuni casi può trattarsi di una situazione temporanea, ad esempio per il tempo necessario a definire una strategia di consolidamento dei tenant a lungo termine in seguito ad acquisizioni. È anche possibile che un'organizzazione debba gestire più tenant in modo continuativo, a causa di affiliate completamente indipendenti, di requisiti geografici o legali e così via. Nei casi in cui è necessaria un'architettura multi-tenant, si può usare la gestione risorse delegate di Azure per centralizzare e semplificare le operazioni di gestione. È possibile eseguire l'onboarding delle sottoscrizioni di più tenant per la [gestione risorse delegate di Azure](azure-delegated-resource-management.md) per consentire agli utenti designati in un tenant di gestione di eseguire [funzioni di gestione tra tenant](cross-tenant-management-experience.md) in modo centralizzato e scalabile.

## <a name="tenant-management-architecture"></a>Architettura di gestione dei tenant

Quando si centralizzano le operazioni di gestione in più tenant, è necessario determinare in quale tenant saranno inclusi gli utenti che eseguono operazioni di gestione per gli altri tenant. In altri termini, sarà necessario determinare quale sarà il tenant di gestione per gli altri tenant.

Si supponga, ad esempio, che l'organizzazione abbia un tenant singolo denominato *Tenant A*. L'organizzazione acquisisce quindi due tenant aggiuntivi, *Tenant B* e *Tenant C*, che per motivi aziendali devono essere mantenuti distinti.

L'organizzazione vuole usare le stesse definizioni di criteri e procedure di backup e gli stessi processi di sicurezza in tutti i tenant. Dato che sono già presenti utenti (inclusi gruppi di utenti ed entità servizio) incaricati di queste attività in Tenant A, è possibile eseguire l'onboarding di tutte le sottoscrizioni di Tenant B e Tenant C in modo che gli stessi utenti di Tenant A possano eseguire tali attività.

![Utenti di Tenant A che gestiscono le risorse in Tenant B e Tenant C](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Considerazioni sulla sicurezza e sull'accesso

Anche se è possibile delegare solo gruppi di risorse specifici all'interno di una sottoscrizione, nella maggior parte degli scenari aziendali è opportuno delegare un'intera sottoscrizione per la gestione risorse delegate di Azure.

In entrambi i casi, quando si definiscono gli utenti che avranno accesso alle risorse assicurarsi di [seguire il principio dei privilegi minimi](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Questo garantisce che gli utenti abbiano solo le autorizzazioni necessarie per eseguire le attività richieste e riduce la probabilità di errori accidentali.

Azure Lighthouse e la gestione risorse delegate di Azure si limitano a fornire collegamenti logici tra un tenant di gestione e i tenant gestiti, anziché spostare fisicamente dati o risorse. L'accesso, inoltre, viene sempre eseguito in una sola direzione, dal tenant di gestione a quelli gestiti.  Gli utenti e i gruppi del tenant di gestione dovranno continuare a usare l'autenticazione a più fattori per eseguire le operazioni di gestione sulle risorse dei tenant gestiti.

Le aziende con tutele interne o esterne per la governance e la conformità possono usare i [log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) per soddisfarne i requisiti di trasparenza. Quando nei tenant aziendali sono state stabilite le relazioni tra tenant di gestione e gestiti, gli utenti di ogni tenant possono ottenere la visibilità delle azioni eseguite dagli utenti dell'altro tenant e monitorarle visualizzando l'attività registrata.

## <a name="onboarding-process-considerations"></a>Considerazioni sul processo di onboarding

È possibile eseguire l'onboarding delle sottoscrizioni (o dei gruppi di risorse all'interno di una sottoscrizione) nella gestione risorse delegate di Azure distribuendo modelli di Azure Resource Manager oppure tramite le offerte di servizi gestiti pubblicate, privatamente o pubblicamente, in Azure Marketplace.

Dato che gli utenti aziendali potranno normalmente ottenere l'accesso diretto ai tenant dell'azienda e non è necessario commercializzare o promuovere un'offerta di gestione, è in genere più veloce e semplice eseguire direttamente la distribuzione con modelli di Azure Resource Manager. Nelle [indicazioni per l'onboarding](../how-to/onboard-customer.md) viene fatto riferimento a provider di servizi e clienti, ma gli stessi processi possono essere usati dalle aziende.

Se si preferisce, è possibile eseguire l'onboarding dei tenant in un'azienda [pubblicando un'offerta di servizi gestiti in Azure Marketplace](../how-to/publish-managed-services-offers.md). Affinché l'offerta sia disponibile solo per i tenant appropriati, assicurarsi che i piani siano contrassegnati come privati. Con un piano privato, è possibile fornire gli ID sottoscrizione per ogni tenant di cui si intende eseguire l'onboarding in modo che nessun altro possa sfruttare l'offerta.

## <a name="terminology-notes"></a>Note sulla terminologia

Per la gestione tra tenant all'interno dell'azienda, i riferimenti ai provider di servizi nella documentazione di Azure Lighthouse possono essere considerati applicabili al tenant di gestione all'interno di un'azienda, ossia al tenant contenente gli utenti che gestiranno le risorse degli altri tenant tramite la gestione risorse delegate di Azure. Analogamente, i riferimenti ai clienti possono essere considerati applicabili ai tenant che delegano le risorse che dovranno essere gestite tramite gli utenti del tenant di gestione.

Nell'esempio descritto in precedenza, Tenant A può essere considerato come il tenant del provider di servizi (tenant di gestione) e Tenant B e Tenant C possono essere considerati come i tenant dei clienti.

In questo esempio, gli utenti di Tenant A con le autorizzazioni appropriate possono [visualizzare e gestire le risorse delegate](../how-to/view-manage-customers.md) nella pagina **Clienti personali** del portale di Azure. Analogamente, gli utenti di Tenant B e Tenant C con le autorizzazioni appropriate possono [visualizzare e gestire le risorse che sono state delegate](../how-to/view-manage-service-providers.md) a Tenant A nella pagina **Provider di servizi** del portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- Informazioni sulla [gestione risorse delegate di Azure](azure-delegated-resource-management.md).