---
title: 'Azure Active Directory B2C: note per gli sviluppatori sull&quot;uso dei criteri personalizzati | Microsoft Docs'
description: Note per gli sviluppatori sulla configurazione e la gestione di Azure AD B2C con criteri personalizzati
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/05/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: a5f222e5b11e05286152a9f1cc55d2c3fc27a9dc
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Note sulla versione di anteprima pubblica dei criteri personalizzati di Azure Active Directory B2C
Il set di funzionalità dei criteri personalizzati è ora disponibile per la valutazione in anteprima pubblica per tutti i clienti di Azure Active Directory B2C (Azure AD B2C). Questo set di funzionalità è destinato agli sviluppatori esperti che creano le soluzioni di gestione delle identità più complesse.  

Attualmente, questo set di funzionalità richiede agli sviluppatori di configurare direttamente il framework dell'esperienza di gestione delle identità tramite la modifica di file XML. Si tratta di un metodo di configurazione avanzato e complesso. Gli sviluppatori esperti di soluzioni di gestione delle identità che usano questo framework dovranno includere nei piani il tempo necessario per completare le procedure dettagliate e leggere i documenti di riferimento. 

## <a name="features-included-in-this-public-preview"></a>Funzionalità incluse nell'anteprima pubblica
Con le nuove funzionalità introdotte nell'anteprima pubblica, gli sviluppatori possono eseguire queste attività:<br>

* Creare e caricare percorsi utente di autenticazione personalizzati usando criteri personalizzati. 
   * Descrivere in modo dettagliato i percorsi utente come scambi tra provider di attestazioni. 
   * Definire la diramazione condizionale nei percorsi utente. 
* Integrare servizi abilitati per API REST nei percorsi utente di autenticazione personalizzati.  
* Aggiungere la federazione con provider di identità conformi allo standard OpenID Connect. <br>
* Aggiungere la federazione con provider di identità che rispettano il protocollo SAML 2.0. 

## <a name="terms-of-the-public-preview"></a>Condizioni per l'anteprima pubblica

* È consigliabile usare le nuove funzionalità solo a scopo di valutazione.<br>
* Le nuove funzionalità non sono destinate all'uso in un ambiente di produzione.<br>
* I contratti di servizio non si applicano alle nuove funzionalità. <br>
* È possibile inviare richieste di supporto tramite i normali canali del supporto. <br>
* Non è stata fissata una data per la disponibilità generale.<br>
* A propria discrezione e per qualsiasi motivo, Microsoft può contrassegnare e rifiutare o limitare gli scenari e i percorsi utente che esulano dagli obiettivi del prodotto Azure AD B2C di offrire una piattaforma di gestione delle identità e degli accessi per i clienti.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilità degli sviluppatori che usano il set di funzionalità dei criteri personalizzati
La configurazione manuale dei criteri garantisce un accesso di livello inferiore alla piattaforma sottostante di Azure AD B2C e determina la creazione di un framework attendibilità interamente personalizzabile e univoco. Le possibili permutazioni di provider di identità personalizzati, relazioni di trust, integrazioni con servizi esterni e flussi di lavoro dettagliati risultano particolarmente impegnative per gli sviluppatori esperti da cui vengono utilizzate.

Per sfruttare appieno l'anteprima pubblica, è consigliabile che gli sviluppatori che utilizzano il set di funzionalità dei criteri personalizzati si attengano alle linee guida seguenti:
* Acquisire familiarità con il linguaggio di configurazione del motore dell'esperienza di gestione delle identità e con la gestione di chiavi e segreti.
* Assumere la proprietà degli scenari e delle integrazioni personalizzate.
* Eseguire test metodici degli scenari.
* Seguire le procedure consigliate di staging e sviluppo software con almeno un ambiente di sviluppo e testing e un ambiente di produzione.
* Mantenersi aggiornati sui nuovi sviluppi dei servizi e dei provider di identità con cui viene eseguita l'integrazione. Ad esempio, tenere traccia delle modifiche dei segreti e delle modifiche pianificate e non pianificate del servizio.
* Configurare il monitoraggio attivo e monitorare il tempo di risposta degli ambienti di produzione.
* Mantenere aggiornati gli indirizzi di posta elettronica di contatto e prestare attenzione ai messaggi di posta elettronica del team del sito live Microsoft.
* Intervenire tempestivamente quando consigliato del team del sito live Microsoft. 


>[!NOTE]
>Queste funzionalità potrebbero essere infine incluse nei criteri predefiniti di Azure AD, diventando così accessibili a tutti gli sviluppatori.

## <a name="next-steps"></a>Passaggi successivi
[Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md)

