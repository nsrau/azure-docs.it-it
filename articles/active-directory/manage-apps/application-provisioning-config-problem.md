---
title: Problemi di configurazione del provisioning utenti in un'app della raccolta Azure AD
description: Come risolvere i problemi comuni riscontrati durante la configurazione del provisioning utenti per un'applicazione già elencata nella raccolta di Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55b9b8dae6ff47099935f42f75286b1b4ddd3708
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275752"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problemi di configurazione del provisioning utenti in un'applicazione della raccolta di Azure AD

Per configurare il [provisioning utenti automatico](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) per un'app (se supportato), è necessario seguire istruzioni specifiche per preparare l'applicazione a questa operazione. Sarà quindi possibile usare il Portale di Azure per configurare il servizio di provisioning per sincronizzare gli account utente con l'applicazione.

È sempre consigliabile iniziare cercando l'esercitazione di configurazione specifica per il provisioning dell'applicazione desiderata. Seguire quindi i passaggi riportati per configurare l'app e Azure AD e creare la connessione di provisioning. Le esercitazioni per le app sono disponibili nell'[Elenco di esercitazioni sulla procedura di integrazione delle applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Come verificare se viene eseguito il provisioning 

Dopo aver configurato il servizio, sarà possibile ricavare la maggior parte delle informazioni dettagliate sul funzionamento del servizio in due posizioni:

-   **Log di provisioning (anteprima)** : i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) registrano tutte le operazioni eseguite dal servizio di provisioning, inclusa l'esecuzione di query Azure ad per gli utenti assegnati che rientrano nell'ambito del provisioning. l'esecuzione nell'app di destinazione di query per l'esistenza di tali utenti, il confronto degli oggetti utente tra i sistemi e quindi l'aggiunta, l'aggiornamento o la disabilitazione dell'account utente nel sistema di destinazione in base al confronto. È possibile accedere ai log di provisioning nel portale di Azure selezionando **Azure Active Directory** &gt; **app aziendali** &gt; i **log di provisioning (anteprima)** nella sezione **attività** .

-   **Stato corrente:** Un riepilogo dell'ultimo esecuzione del provisioning per una determinata app può essere visualizzato nella **Azure Active Directory &gt; app aziendali &gt; \[nome dell'applicazione\] sezione provisioning** , nella parte inferiore della schermata sotto le impostazioni del servizio.&gt; La sezione stato corrente indica se un ciclo di provisioning ha avviato il provisioning degli account utente. È possibile controllare lo stato di avanzamento del ciclo, vedere il numero di utenti e gruppi di cui è stato effettuato il provisioning e verificare il numero di ruoli creati. Se sono presenti errori, è possibile trovare i dettagli nei [log di provisioning (anteprima)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Aree problematiche generali con provisioning da considerare

Di seguito è riportato un elenco delle aree problematiche generali che è possibile analizzare se si ha un'idea del punto da cui iniziare.

* [Avvio non riuscito del servizio di provisioning](#provisioning-service-does-not-appear-to-start)
* Impossibile salvare la configurazione a causa di un errore nelle credenziali dell'app
* [Log di provisioning: gli utenti vengono "ignorati" e non sottoposti a provisioning, anche se sono assegnati](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Avvio non riuscito del servizio di provisioning

Se si imposta **Stato provisioning** su **Attivato** nella sezione **Azure Active Directory &gt; App aziendali &gt; \[Nome applicazione\] &gt;Provisioning** del Portale di Azure, ma in seguito a successivi ricaricamenti non vengono visualizzati altri dettagli nella pagina, È probabile che il servizio sia in esecuzione, ma non ha ancora completato un ciclo iniziale. Controllare i **log di provisioning** descritti in precedenza per determinare le operazioni eseguite dal servizio e in caso di errori.

>[!NOTE]
>Un ciclo iniziale può richiedere da 20 minuti a diverse ore, a seconda delle dimensioni della directory Azure AD e del numero di utenti nell'ambito del provisioning. Le sincronizzazioni successive dopo il ciclo iniziale risultano più veloci, poiché il servizio di provisioning archivia le filigrane che rappresentano lo stato di entrambi i sistemi dopo il ciclo iniziale, migliorando le prestazioni delle sincronizzazioni successive.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Impossibile salvare la configurazione a causa di un errore nelle credenziali dell'app

Ai fini del provisioning, Azure AD richiede credenziali valide che consentano di connettersi a un'API di gestione utenti fornita da tale app. Se queste credenziali non funzionano o non sono note, vedere l'esercitazione per configurare questa app, descritta in precedenza.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Il provisioning dei log dice che gli utenti vengono ignorati e non sottoposti a provisioning anche se sono assegnati

Quando un utente viene visualizzato come "ignorato" nei log di provisioning, è molto importante leggere i dettagli estesi nel messaggio del log per determinare il motivo. Di seguito sono elencati i motivi e le risoluzioni comuni:

- **È stato configurato un filtro di definizione dell'ambito** **che esclude l'utente in base a un valore di attributo**. Per altre informazioni sui filtri di definizione dell'ambito, vedere <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

- **L'utente "non è autorizzato in modo efficiente".** Se viene visualizzato questo messaggio di errore specifico, si è verificato un problema con il record di assegnazione degli utenti archiviato in Azure AD. Per risolvere il problema, annullare l'assegnazione dell'utente (o del gruppo) dall'app e riassegnarla. Per altre informazioni sull'assegnazione, vedere <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

- **Attributo obbligatorio mancante o non popolato per un utente.** Un aspetto importante da considerare quando si configura il provisioning è quello di esaminare e configurare il mapping degli attributi e i flussi di lavoro che definiscono il tipo di flusso di proprietà utente (o gruppo) da Azure AD all'applicazione. Rientra in questo ambito anche l'impostazione della "proprietà di abbinamento" che può essere usata per identificare e abbinare in modo univoco utenti/gruppi tra i due sistemi. Per altre informazioni su questo processo importante, vedere <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

  * **Mapping degli attributi per gruppi:** provisioning dei dettagli del gruppo e del nome del gruppo, oltre ai membri, se supportato per alcune applicazioni. È possibile abilitare o disabilitare questa funzionalità abilitando o disabilitando il **mapping** per gli oggetti gruppo visualizzati nella scheda **provisioning** . Se il provisioning dei gruppi è abilitato, assicurarsi di esaminare i mapping degli attributi per assicurarsi che venga usato un campo appropriato per "ID corrispondente". Può trattarsi del nome visualizzato o dell'alias di posta elettronica, poiché il provisioning del gruppo e dei relativi membri non viene eseguito se la proprietà di abbinamento è vuota o non popolata per un gruppo in Azure AD.

## <a name="next-steps"></a>Passaggi successivi
[Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](user-provisioning.md)
