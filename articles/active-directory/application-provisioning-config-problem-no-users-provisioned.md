---
title: "Nessun utente è sottoposto a provisioning per un'applicazione della raccolta di Azure AD | Microsoft Docs"
description: Come risolvere i problemi comuni riscontrati quando in un'applicazione della raccolta di Azure AD non vengono visualizzati utenti configurati per il provisioning utenti con Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: asteen
ms.openlocfilehash: 30db71c0706de1dcc0d48fb72fa5d62475a14ba7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nessun utente è sottoposto a provisioning per un'applicazione della raccolta di Azure AD

Dopo aver configurato il provisioning automatico per un'applicazione (e dopo aver verificato la validità delle credenziali fornite ad Azure AD per connettersi all'app), gli utenti e/o i gruppi vengono sottoposti a provisioning per l'app in base a quanto segue:

-   Utenti e gruppi **assegnati** all'applicazione. Per altre informazioni sull'assegnazione, vedere [Assegnare un utente o un gruppo a un'app aziendale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

-   Abilitazione o meno dei **mapping degli attributi** ed eventuale configurazione per la sincronizzazione degli attributi validi da Azure AD all'app. Per altre informazioni sui mapping degli attributi, vedere [Personalizzazione dei mapping degli attributi del provisioning degli utenti per le applicazioni SaaS in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

-   Presenza o meno di un **filtro per la definizione dell'ambito** che filtri gli utenti in base a specifici valori di attributo. Per altre informazioni sui filtri di ambito, vedere [Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

Se gli utenti non sono sottoposti a provisioning, vedere i log di controllo in Azure AD e cercare le voci per un utente specifico.

I log di controllo di provisioning sono accessibili nella scheda **Azure Active Directory &gt; App aziendali &gt; \[Nome applicazione\] &gt; Log di controllo** del Portale di Azure. Filtrare i log nella categoria **Provisioning account** per visualizzare solo gli eventi di provisioning per l'app specifica. È possibile cercare gli utenti in base all'ID di abbinamento configurato nel mapping degli attributi. Ad esempio, se è stato configurato il nome dell'entità utente o l'indirizzo di posta elettronica come attributo di abbinamento sul lato Azure AD e l'utente non sottoposto a provisioning presenta un valore "audrey@contoso.com", cercare "audrey@contoso.com" nei log di controllo ed esaminare le voci restituite.

I log di controllo di provisioning registrano tutte le operazioni eseguite dal servizio di provisioning, ad esempio l'esecuzione in Azure AD di query sugli utenti assegnati che rientrano nell'ambito del provisioning, l'esecuzione nell'app di destinazione di query sull'esistenza di tali utenti, il confronto degli oggetti utente tra i sistemi e quindi l'aggiunta, l'aggiornamento o la disabilitazione dell'account utente nel sistema di destinazione in base al confronto.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Aree problematiche generali con provisioning da considerare

Di seguito è riportato un elenco delle aree problematiche generali che è possibile analizzare se si ha un'idea del punto da cui iniziare.

* [Avvio non riuscito del servizio di provisioning](#provisioning-service-does-not-appear-to-start)
* [I log di controllo indicano che gli utenti vengono ignorati e non sottoposti a provisioning, anche se assegnati](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Avvio non riuscito del servizio di provisioning

Se si imposta **Stato provisioning** su **Attivato** nella sezione **Azure Active Directory &gt; App aziendali &gt; \[Nome applicazione\] &gt;Provisioning** del Portale di Azure, ma in seguito a successivi ricaricamenti non vengono visualizzati altri dettagli nella pagina, il servizio potrebbe essere in esecuzione senza aver ancora completato una sincronizzazione iniziale. Esaminare i **log di controllo** descritti in precedenza per individuare le operazioni in esecuzione nel servizio e verificare la presenza di eventuali errori.

>[!NOTE]
>Una sincronizzazione iniziale può richiedere da 20 minuti a diverse ore, a seconda delle dimensioni della directory di Azure AD e del numero di utenti inclusi nell'ambito del provisioning. Le sincronizzazioni successive a quella iniziale risultano più veloci, poiché il servizio di provisioning archivia i limiti che rappresentano lo stato di entrambi i sistemi dopo la sincronizzazione iniziale, migliorando le prestazioni delle sincronizzazioni successive.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>I log di controllo indicano che gli utenti vengono ignorati e non sottoposti a provisioning, anche se assegnati

Quando un utente viene visualizzato come "ignorato" nei log di controllo, è essenziale leggere i dettagli estesi nel messaggio di log per determinarne il motivo. Di seguito sono elencati i motivi e le risoluzioni comuni:

-   **È stato configurato un filtro di definizione dell'ambito** **che esclude l'utente in base a un valore di attributo**. Per altre informazioni sui filtri di definizione dell'ambito, vedere <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **L'utente "non è autorizzato in modo efficiente".** Se viene visualizzato questo messaggio di errore specifico, si è verificato un problema con il record di assegnazione degli utenti archiviato in Azure AD. Per risolvere il problema, annullare l'assegnazione dell'utente (o del gruppo) dall'app e riassegnarla. Per altre informazioni sull'assegnazione, vedere <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Attributo obbligatorio mancante o non popolato per un utente.** Un aspetto importante da considerare quando si configura il provisioning è quello di esaminare e configurare il mapping degli attributi e i flussi di lavoro che definiscono il tipo di flusso di proprietà utente (o gruppo) da Azure AD all'applicazione. Rientra in questo ambito anche l'impostazione della "proprietà di abbinamento" che può essere usata per identificare e abbinare in modo univoco utenti/gruppi tra i due sistemi. Per altre informazioni su questo importante processo, vedere [Personalizzazione dei mapping degli attributi del provisioning degli utenti per le applicazioni SaaS in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

  * **Mapping degli attributi per gruppi:** provisioning dei dettagli del gruppo e del nome del gruppo, oltre ai membri, se supportato per alcune applicazioni. È possibile abilitare o disabilitare questa funzionalità abilitando o disabilitando il **mapping** per gli oggetti di gruppo visualizzati nella scheda **Provisioning**. Se il provisioning di gruppi è abilitato, verificare di controllare i mapping degli attributi per garantire che venga usato un campo appropriato per l'ID di abbinamento. Può trattarsi del nome visualizzato o dell'alias di posta elettronica, poiché il provisioning del gruppo e dei relativi membri non viene eseguito se la proprietà di abbinamento è vuota o non popolata per un gruppo in Azure AD.

## <a name="next-steps"></a>Passaggi successivi
[Servizio di sincronizzazione Azure AD Connect: informazioni sul provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md)

