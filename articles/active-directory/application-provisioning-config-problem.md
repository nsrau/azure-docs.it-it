---
title: Problemi di configurazione del provisioning utenti in un'applicazione della raccolta di Azure AD | Microsoft Docs
description: "Come risolvere i problemi comuni riscontrati durante la configurazione del provisioning utenti per un'applicazione già elencata nella raccolta di Azure AD"
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
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 44e344095352f2bc6b27e389fc8be2cdf3e368d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problemi di configurazione del provisioning utenti in un'applicazione della raccolta di Azure AD

Per configurare il [provisioning utenti automatico](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) per un'app (se supportato), è necessario seguire istruzioni specifiche per preparare l'applicazione a questa operazione. Sarà quindi possibile usare il Portale di Azure per configurare il servizio di provisioning per sincronizzare gli account utente con l'applicazione.

È sempre consigliabile iniziare cercando l'esercitazione di configurazione specifica per il provisioning dell'applicazione desiderata. Seguire quindi i passaggi riportati per configurare l'app e Azure AD e creare la connessione di provisioning. Le esercitazioni per le app sono disponibili nell'[Elenco di esercitazioni sulla procedura di integrazione delle applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Come verificare se viene eseguito il provisioning 

Dopo aver configurato il servizio, sarà possibile ricavare la maggior parte delle informazioni dettagliate sul funzionamento del servizio in due posizioni:

-   **Log di controllo**: i log di controllo di provisioning registrano tutte le operazioni eseguite dal servizio di provisioning, ad esempio l'esecuzione in Azure AD di query sugli utenti assegnati che rientrano nell'ambito del provisioning, l'esecuzione nell'app di destinazione di query per l'esistenza di tali utenti, il confronto degli oggetti utente tra i sistemi e quindi l'aggiunta, l'aggiornamento o la disabilitazione dell'account utente nel sistema di destinazione in base al confronto. I log di controllo di provisioning sono accessibili nella scheda **Azure Active Directory &gt; App aziendali &gt; \[Nome applicazione\] &gt; Log di controllo** del Portale di Azure. Filtrare i log nella categoria **Provisioning account** per visualizzare solo gli eventi di provisioning per l'app specifica.

-   **Stato del provisioning**: un riepilogo dell'ultimo provisioning eseguito per una specifica app è disponibile nella sezione **Azure Active Directory &gt; App aziendali &gt;\[Nome applicazione\]&gt;Provisioning** nella parte inferiore dello schermo in Impostazioni servizio. Questa sezione contiene un riepilogo del numero di utenti (e/o gruppi) attualmente sincronizzati tra i due sistemi e segnala la presenza di eventuali errori. I dettagli degli errori sono disponibili nei log di controllo. Si noti che lo stato di provisioning non verrà popolato fino al completamento di una sincronizzazione iniziale completa tra Azure AD e l'app.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Aree problematiche generali con provisioning da considerare

Di seguito è riportato un elenco delle aree problematiche generali che è possibile analizzare se si ha un'idea del punto da cui iniziare.

* [Avvio non riuscito del servizio di provisioning](#provisioning-service-does-not-appear-to-start)
* [Impossibile salvare la configurazione a causa di un errore nelle credenziali dell'app](#can’t-save-configuration-due-to-app-credentials-not-working)
* [I log di controllo indicano che gli utenti vengono "ignorati" e non sottoposti a provisioning, anche se assegnati](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Avvio non riuscito del servizio di provisioning

Se si imposta **Stato provisioning** su **Attivato** nella sezione **Azure Active Directory &gt; App aziendali &gt; \[Nome applicazione\] &gt;Provisioning** del Portale di Azure, ma in seguito a successivi ricaricamenti non vengono visualizzati altri dettagli nella pagina, il servizio potrebbe essere in esecuzione senza aver ancora completato una sincronizzazione iniziale. Esaminare i **log di controllo** descritti in precedenza per individuare le operazioni in esecuzione nel servizio e verificare la presenza di eventuali errori.

>[!NOTE]
>Una sincronizzazione iniziale può richiedere da 20 minuti a diverse ore, a seconda delle dimensioni della directory di Azure AD e del numero di utenti inclusi nell'ambito del provisioning. Le sincronizzazioni successive a quella iniziale risultano più veloci, poiché il servizio di provisioning archivia i limiti che rappresentano lo stato di entrambi i sistemi dopo la sincronizzazione iniziale, migliorando le prestazioni delle sincronizzazioni successive.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Impossibile salvare la configurazione a causa di un errore nelle credenziali dell'app

Ai fini del provisioning, Azure AD richiede credenziali valide che consentano di connettersi a un'API di gestione utenti fornita da tale app. Se queste credenziali non funzionano o non sono note, vedere l'esercitazione per configurare questa app, descritta in precedenza.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>I log di controllo indicano che gli utenti vengono ignorati e non sottoposti a provisioning, anche se assegnati

Quando un utente viene visualizzato come "ignorato" nei log di controllo, è essenziale leggere i dettagli estesi nel messaggio di log per determinarne il motivo. Di seguito sono elencati i motivi e le risoluzioni comuni:

-   **È stato configurato un filtro di definizione dell'ambito** **che esclude l'utente in base a un valore di attributo**. Per altre informazioni sui filtri di definizione dell'ambito, vedere <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **L'utente "non è autorizzato in modo efficiente".** Se viene visualizzato questo messaggio di errore specifico, si è verificato un problema con il record di assegnazione degli utenti archiviato in Azure AD. Per risolvere il problema, annullare l'assegnazione dell'utente (o del gruppo) dall'app e riassegnarla. Per altre informazioni sull'assegnazione, vedere <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Attributo obbligatorio mancante o non popolato per un utente.** Un aspetto importante da considerare quando si configura il provisioning è quello di esaminare e configurare il mapping degli attributi e i flussi di lavoro che definiscono il tipo di flusso di proprietà utente (o gruppo) da Azure AD all'applicazione. Rientra in questo ambito anche l'impostazione della "proprietà di abbinamento" che può essere usata per identificare e abbinare in modo univoco utenti/gruppi tra i due sistemi. Per altre informazioni su questo importante processo, vedere <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

   * **Mapping degli attributi per gruppi:** provisioning dei dettagli del gruppo e del nome del gruppo, oltre ai membri, se supportato per alcune applicazioni. È possibile abilitare o disabilitare questa funzionalità abilitando o disabilitando il **mapping** per gli oggetti di gruppo visualizzati nella scheda **Provisioning**. Se il provisioning di gruppi è abilitato, verificare di controllare i mapping degli attributi per garantire che venga usato un campo appropriato per l'ID di abbinamento. Può trattarsi del nome visualizzato o dell'alias di posta elettronica, poiché il provisioning del gruppo e dei relativi membri non viene eseguito se la proprietà di abbinamento è vuota o non popolata per un gruppo in Azure AD.

#<a name="next-steps"></a>Passaggi successivi
[Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
