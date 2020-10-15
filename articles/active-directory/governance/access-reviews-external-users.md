---
title: Usare Azure AD Identity Governance per rivedere e rimuovere gli utenti esterni che non hanno più accesso alle risorse
description: Usare le verifiche di accesso per estendere la rimozione dell'accesso dai membri delle organizzazioni partner
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: barclayn
ms.openlocfilehash: 64ff2a2a7ad6f07aac959422eadec7f24b210d88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89505838"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Usare la governance delle identità Azure Active Directory (Azure AD) per rivedere e rimuovere gli utenti esterni che non hanno più l'accesso alle risorse

Questo articolo descrive le funzionalità e i metodi che consentono di individuare e selezionare le identità esterne, in modo che sia possibile esaminarli e rimuoverli da Azure AD se non sono più necessari. Il cloud rende più semplice che mai collaborare con utenti interni o esterni. Adottando Office 365, le organizzazioni iniziano a vedere la proliferazione di identità esterne (inclusi i guest), mentre gli utenti interagiscono su dati, documenti o aree di lavoro digitali, ad esempio i team. Le organizzazioni devono bilanciare, abilitando la collaborazione e rispettando i requisiti di sicurezza e governance. Parte di queste attività deve includere la valutazione e la pulizia degli utenti esterni, che sono stati invitati per la collaborazione nel tenant, che hanno originato dalle organizzazioni partner e la loro rimozione dal Azure AD quando non sono più necessari.

>[!NOTE]
>Per usare le verifiche di accesso di Azure AD è necessaria una licenza di valutazione o una licenza a pagamento Azure Premium P2 o Enterprise Mobility + Security E5 valida. Per altre informazioni, vedere [Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Perché esaminare gli utenti di organizzazioni esterne nel tenant?

Nella maggior parte delle organizzazioni, gli utenti finali avviano il processo di invitare partner aziendali e fornitori per la collaborazione. La necessità di collaborare alle organizzazioni delle aziende per fornire ai proprietari delle risorse e agli utenti finali la possibilità di valutare e attestare gli utenti esterni regolarmente. Spesso il processo di onboarding dei nuovi partner di collaborazione è pianificato e considerato, ma con molte collaborazioni che non hanno una data di fine chiara, non è sempre ovvio quando un utente non necessita più dell'accesso. Inoltre, la gestione del ciclo di vita delle identità consente alle aziende di proteggere Azure AD pulire e rimuovere gli utenti che non necessitano più dell'accesso alle risorse dell'organizzazione. Mantenere solo i riferimenti alle identità pertinenti per partner e fornitori nella directory consente di ridurre il rischio dei dipendenti, selezionando inavvertitamente e concedendo l'accesso agli utenti esterni che dovrebbero essere stati rimossi. Questo documento illustra diverse opzioni che variano da suggerimenti proattivi consigliati a attività reattive e di pulizia per gestire le identità esterne.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Usare la gestione dei diritti per concedere e revocare l'accesso

Le funzionalità di gestione dei diritti consentono il [ciclo di vita automatico delle identità esterne](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) con accesso alle risorse. Stabilendo processi e procedure per gestire l'accesso tramite la gestione dei diritti e la pubblicazione di risorse attraverso pacchetti di accesso, tenere traccia dell'accesso degli utenti esterni alle risorse diventa un problema molto meno complesso da risolvere. Quando si gestisce l'accesso tramite i [pacchetti di accesso alla gestione dei diritti](entitlement-management-overview.md) in Azure ad, l'organizzazione può definire e gestire in modo centralizzato l'accesso per gli utenti, nonché gli utenti delle organizzazioni partner. La gestione dei diritti USA le approvazioni e le assegnazioni dei pacchetti di accesso per tenere traccia del punto in cui gli utenti esterni hanno richiesto e assegnato l'accesso Se un utente esterno perde tutte le relative assegnazioni, la gestione dei diritti può rimuovere automaticamente questi utenti esterni dal tenant. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Trova Guest non invitati tramite la gestione dei diritti

Quando i dipendenti sono autorizzati a collaborare con utenti esterni, possono invitare un numero qualsiasi di utenti all'esterno dell'organizzazione. La ricerca e il raggruppamento dei partner esterni in gruppi dinamici allineati dall'azienda e la relativa verifica potrebbero non essere fattibili, in quanto potrebbero essere presenti troppe società diverse per la revisione oppure non è presente alcun proprietario o sponsor per l'organizzazione. Microsoft fornisce uno script di PowerShell di esempio che consente di analizzare l'uso di identità esterne in un tenant. Lo script enumera le identità esterne e le categorizza. Lo script può essere utile per identificare e pulire le identità esterne che potrebbero non essere più necessarie. Come parte dell'output dello script, l'esempio di script supporta la creazione automatica di gruppi di sicurezza che contengono i partner esterni senza gruppi identificati, per un'ulteriore analisi e l'uso con le verifiche di accesso Azure AD.
Lo script è disponibile su [GitHub](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Al termine dell'esecuzione dello script, viene generato un file di output HTML che descrive le identità esterne che:

- Non sono più presenti appartenenze a gruppi nel tenant
- Disporre di un'assegnazione per un ruolo con privilegi nel tenant
- Eseguire un'assegnazione a un'applicazione nel tenant

L'output include anche i singoli domini per ognuna di queste identità esterne. 

>[!NOTE]
>Lo script a cui si fa riferimento è uno script di esempio che verifica l'appartenenza a un gruppo, le assegnazioni di ruolo e le assegnazioni di applicazioni in Azure AD. Potrebbero essere presenti altre assegnazioni nelle applicazioni che gli utenti esterni ricevevano al di fuori di Azure AD, ad esempio SharePoint (assegnazione di appartenenza diretta) o controllo degli accessi in base al ruolo di Azure o Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Esaminare le risorse usate da identità esterne

Se si hanno identità esterne che usano risorse quali team o altre applicazioni non ancora regolate dalla gestione dei diritti, potrebbe essere opportuno verificare l'accesso a queste risorse regolarmente. Azure AD le verifiche di [accesso](create-access-review.md) offrono la possibilità di esaminare l'accesso alle identità esterne, consentendo al proprietario della risorsa, alle identità esterne o a un'altra persona delegata che si considera attendibile di confermare se è necessario l'accesso continuo. Le verifiche di accesso sono destinate a una risorsa e creano un'attività di revisione con ambito a tutti gli utenti che hanno accesso alla risorsa o solo agli utenti guest. Il revisore visualizzerà quindi l'elenco di utenti risultante che è necessario esaminare, ovvero tutti gli utenti, inclusi i dipendenti dell'organizzazione o solo le identità esterne.

![uso di un gruppo per verificare l'accesso](media/access-reviews-external-users/group-members.png)

La definizione di una cultura di revisione basata sul proprietario delle risorse consente di gestire l'accesso per le identità esterne. I proprietari delle risorse, responsabili dell'accesso, della disponibilità e della sicurezza delle informazioni di loro proprietà, sono, nella maggior parte dei casi, i destinatari più idonei a prendere decisioni in merito all'accesso alle risorse e sono più vicini agli utenti che accedono al sistema IT centrale o a uno sponsor che gestisce molti External.

## <a name="create-access-reviews-for-external-identities"></a>Creare verifiche di accesso per le identità esterne

Gli utenti che non hanno più accesso alle risorse nel tenant possono essere rimossi se non lavorano più con l'organizzazione. Prima di bloccare ed eliminare queste identità esterne, è possibile rivolgersi a tali utenti esterni per assicurarsi che non sia stato trascurato un progetto o che l'accesso sia stato effettuato e che sia ancora necessario. Quando si crea un gruppo che contiene tutte le identità esterne come membri che sono stati trovati non hanno accesso ad alcuna risorsa nel tenant, è possibile usare le verifiche di accesso per fare in modo che tutti gli esterni autocertificano se hanno ancora bisogno o hanno accesso oppure dovranno comunque accedere in futuro. Nell'ambito della revisione, il creatore della revisione delle verifiche di accesso può usare la funzione **Richiedi il motivo** per l'approvazione per richiedere agli utenti esterni di fornire una giustificazione per l'accesso continuo, tramite cui è possibile apprendere dove e come devono ancora accedere al tenant. Inoltre, è possibile abilitare l'impostazione del **contenuto aggiuntivo per la funzionalità di posta elettronica del revisore** , in modo che gli utenti sappiano che perderanno l'accesso se non rispondono e, se necessitano ancora dell'accesso, è necessaria una giustificazione. Se si desidera procedere e consentire alle verifiche di accesso di **disabilitare ed eliminare** identità esterne, qualora non rispondano o forniscano un motivo valido per l'accesso continuo, è possibile utilizzare l'opzione Disabilita ed Elimina, come descritto nella sezione successiva.

![limitazione dell'ambito della verifica solo agli utenti Guest](media/access-reviews-external-users/guest-users-only.png)

Al termine della verifica, nella pagina **risultati** viene visualizzata una panoramica della risposta fornita da ogni identità esterna. È possibile scegliere di applicare i risultati automaticamente e consentire alle verifiche di accesso di disabilitarli ed eliminarli. In alternativa, è possibile esaminare le risposte fornite e decidere se si desidera rimuovere l'accesso o il completamento di un utente e ottenere ulteriori informazioni prima di prendere una decisione. Se alcuni utenti hanno ancora accesso alle risorse che non sono state ancora esaminate, è possibile usare la revisione come parte dell'individuazione e arricchire il ciclo di verifica e di attestazione successivo.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews-preview"></a>Disabilitare ed eliminare identità esterne con Azure AD le verifiche di accesso (anteprima)

Oltre all'opzione che consente di rimuovere identità esterne indesiderate da risorse quali gruppi o applicazioni, le verifiche di accesso Azure AD possono impedire l'accesso delle identità esterne al tenant ed eliminare le identità esterne dal tenant dopo 30 giorni.

![al termine delle impostazioni](media/access-reviews-external-users/upon-completion-settings.png)

Quando si crea una nuova verifica di accesso, nella sezione "impostazioni di completamento", per l' **azione da applicare agli utenti** non autorizzati, è possibile definire impedisce agli utenti di eseguire l'accesso **per 30 giorni, quindi rimuovere l'utente dal tenant**.
Questa impostazione, attualmente in anteprima, consente di identificare, bloccare ed eliminare identità esterne dal tenant di Azure AD. Le identità esterne che vengono rivedute e negate l'accesso continuo da parte del revisore verranno bloccate ed eliminate, indipendentemente dall'accesso alle risorse o dall'appartenenza al gruppo. Questa impostazione è ideale per l'ultimo passaggio dopo aver verificato che gli utenti esterni in-Review non contengano più l'accesso alle risorse e possano essere rimossi in modo sicuro dal tenant o se si desidera assicurarsi che vengano rimossi, indipendentemente dall'accesso permanente. La funzionalità "Disabilita ed Elimina" blocca prima l'utente esterno, togliendo la possibilità di accedere al tenant e accedere alle risorse. L'accesso alle risorse non viene revocato in questa fase e, nel caso in cui si desideri ricreare un'istanza dell'utente esterno, la possibilità di accedere può essere riconfigurata. In assenza di ulteriori azioni, un'identità esterna bloccata verrà eliminata dalla directory dopo 30 giorni, rimuovendo l'account e il relativo accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Verifiche di accesso - API Graph](/graph/api/resources/accessreviews-root?view=graph-rest-beta)
- [Gestione entitlement - API Graph](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)