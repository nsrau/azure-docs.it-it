---
title: Azure Lighthouse in scenari aziendali
description: Le funzionalità di Azure Lighthouse possono essere usate per semplificare la gestione tra tenant all'interno di un'azienda che usa più tenant di Azure AD.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 3f452e6810fa6809b5ba1b83b664f8b38d82a895
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88167350"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse in scenari aziendali

Uno scenario comune per [Azure Lighthouse](../overview.md) è un provider di servizi che gestisce le risorse nei tenant Azure Active Directory (Azure ad) dei clienti. Tuttavia, le funzionalità di Azure Lighthouse possono anche essere usate per semplificare la gestione di più tenant all'interno di un'azienda che usa più tenant Azure AD.

## <a name="single-vs-multiple-tenants"></a>Tenant singolo o più tenant

Per la maggior parte delle organizzazioni, la gestione è più semplice con un singolo tenant di Azure AD. L'inclusione di tutte le risorse in un tenant consente la centralizzazione delle attività di gestione da parte di entità servizio, gruppi di utenti o utenti designati all'interno di tale tenant. Quando possibile, è consigliabile usare un unico tenant per l'organizzazione. Tuttavia, alcune organizzazioni potrebbero avere più tenant Azure AD. In alcuni casi può trattarsi di una situazione temporanea, ad esempio quando si verificano acquisizioni e una strategia di consolidamento dei tenant a lungo termine non è ancora stata definita. In altri casi, è possibile che le organizzazioni debbano mantenere più tenant su base continuativa, a causa di consociate completamente indipendenti, requisiti geografici o legali o altre considerazioni.

Nei casi in cui è necessaria un'architettura multi-tenant, Azure Lighthouse può aiutare a centralizzare e semplificare le operazioni di gestione. Usando la [gestione delle risorse delegata di Azure](azure-delegated-resource-management.md), gli utenti in un tenant di gestione possono eseguire [funzioni di gestione tra tenant](cross-tenant-management-experience.md) in modo centralizzato e scalabile.

## <a name="tenant-management-architecture"></a>Architettura di gestione dei tenant

Per usare Azure Lighthouse in un'azienda, è necessario determinare quale tenant includerà gli utenti che eseguono operazioni di gestione sugli altri tenant. In altre parole, sarà necessario determinare quale tenant sarà il tenant di gestione per gli altri tenant.

Si immagini, ad esempio, che l'organizzazione disponga di un singolo tenant che chiameremo *tenant a*. L'organizzazione acquisisce quindi il *tenant B* e il *tenant C*e si hanno motivi aziendali per cui è necessario mantenerli come tenant distinti.

L'organizzazione vuole usare le stesse definizioni di criteri e procedure di backup e gli stessi processi di sicurezza in tutti i tenant. Poiché il tenant A include già gli utenti responsabili di queste attività, è possibile caricare le sottoscrizioni nel tenant B e nel tenant C, consentendo agli stessi utenti del tenant a di eseguire tali attività.

![Diagramma che Mostra gli utenti del tenant A che gestiscono le risorse nel tenant B e nel tenant C.](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Considerazioni sulla sicurezza e sull'accesso

Nella maggior parte degli scenari aziendali è opportuno delegare una sottoscrizione completa al Faro di Azure. È anche possibile scegliere di delegare solo gruppi di risorse specifici all'interno di una sottoscrizione.

In entrambi i casi, assicurarsi di [seguire il principio dei privilegi minimi quando si definiscono gli utenti che avranno accesso alle risorse delegate](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Questo garantisce che gli utenti abbiano solo le autorizzazioni necessarie per eseguire le attività richieste e riduce la probabilità di errori accidentali.

Azure Lighthouse fornisce solo collegamenti logici tra un tenant di gestione e i tenant gestiti, invece di trasferire fisicamente dati o risorse. L'accesso, inoltre, viene sempre eseguito in una sola direzione, dal tenant di gestione a quelli gestiti.  Gli utenti e i gruppi del tenant di gestione dovranno continuare a usare l'autenticazione a più fattori per eseguire le operazioni di gestione sulle risorse dei tenant gestiti.

Le aziende con tutele interne o esterne per la governance e la conformità possono usare i [log attività di Azure](../../azure-monitor/platform/platform-logs-overview.md) per soddisfarne i requisiti di trasparenza. Quando i tenant aziendali hanno stabilito relazioni di gestione e tenant gestite, gli utenti in ogni tenant possono visualizzare l'attività registrata per visualizzare le azioni eseguite dagli utenti nel tenant di gestione.

## <a name="onboarding-considerations"></a>Considerazioni sull'onboarding

Le sottoscrizioni o i gruppi di risorse all'interno di una sottoscrizione possono essere caricate in Azure Lighthouse distribuendo Azure Resource Manager modelli o tramite le offerte di servizi gestiti pubblicate in Azure Marketplace.

Poiché gli utenti aziendali in genere avranno accesso diretto ai tenant dell'organizzazione e non è necessario commercializzare o promuovere un'offerta di gestione, in genere è più veloce e più semplice distribuire Azure Resource Manager modelli. Sebbene le [linee guida per l'onboarding](../how-to/onboard-customer.md) si riferiscono a provider di servizi e clienti, le aziende possono usare gli stessi processi per caricare i tenant.

Se si preferisce, è possibile eseguire l'onboarding dei tenant in un'azienda [pubblicando un'offerta di servizi gestiti in Azure Marketplace](../how-to/publish-managed-services-offers.md). Affinché l'offerta sia disponibile solo per i tenant appropriati, assicurarsi che i piani siano contrassegnati come privati. Con un piano privato, si forniscono gli ID sottoscrizione per ogni tenant che si prevede di caricare e nessun altro sarà in grado di ottenere l'offerta.

## <a name="terminology-notes"></a>Note sulla terminologia

Per la gestione di più tenant all'interno dell'organizzazione, è possibile comprendere i riferimenti ai provider di servizi nella documentazione di Azure Lighthouse da applicare al tenant di gestione all'interno di un'azienda, ovvero il tenant che include gli utenti che gestiranno le risorse in altri tenant tramite Azure Lighthouse. Analogamente, tutti i riferimenti ai clienti possono essere riconosciuti come applicabili ai tenant che delegano le risorse da gestire tramite gli utenti nel tenant di gestione.

Nell'esempio descritto in precedenza, Tenant A può essere considerato come il tenant del provider di servizi (tenant di gestione) e Tenant B e Tenant C possono essere considerati come i tenant dei clienti.

In questo esempio, gli utenti di Tenant A con le autorizzazioni appropriate possono [visualizzare e gestire le risorse delegate](../how-to/view-manage-customers.md) nella pagina **Clienti personali** del portale di Azure. Analogamente, gli utenti di Tenant B e Tenant C con le autorizzazioni appropriate possono [visualizzare e gestire le risorse che sono state delegate](../how-to/view-manage-service-providers.md) a Tenant A nella pagina **Provider di servizi** del portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- Informazioni sulla [gestione risorse delegate di Azure](azure-delegated-resource-management.md).