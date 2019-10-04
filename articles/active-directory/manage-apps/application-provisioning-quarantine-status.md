---
title: Stato del provisioning dell'applicazione della quarantena | Microsoft Docs
description: Quando è stata configurata un'applicazione per il provisioning utenti automatico, scoprire che cosa significa lo stato di provisioning della quarantena e come eliminarlo.
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
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 704e217cd7ddea988b6a9812627aba8c8468fb73
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955497"
---
# <a name="application-provisioning-in-quarantine-status"></a>Provisioning dell'applicazione in stato di quarantena

Il servizio di provisioning Azure AD monitora l'integrità della configurazione e inserisce le app non integre in uno stato di "quarantena". Se la maggior parte o tutte le chiamate effettuate sul sistema di destinazione hanno esito negativo a causa di un errore, ad esempio credenziali di amministratore non valide, il processo di provisioning è contrassegnato come in quarantena.

Durante la quarantena, la frequenza dei cicli incrementali viene gradualmente ridotta a una volta al giorno. Il processo di provisioning viene rimosso dalla quarantena dopo che tutti gli errori sono stati risolti e viene avviato il ciclo di sincronizzazione successivo. Se il processo di provisioning rimane in quarantena per più di quattro settimane, il processo di provisioning viene disabilitato (arresta l'esecuzione).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Ricerca per categorie verificare se l'applicazione è in quarantena?

Esistono tre modi per verificare se un'applicazione è in quarantena:
  
- Nella portale di Azure passare a **Azure Active Directory** > **applicazioni aziendali** >  @ no__t-4*nome applicazione*&gt; @ no__t-7**provisioning** e scorrere fino all'indicatore di stato nella parte inferiore.  

  ![Barra di stato del provisioning che mostra lo stato della quarantena](media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Usare la richiesta Microsoft Graph [Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) per ottenere lo stato del processo di provisioning a livello di codice:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Controllare la posta elettronica. Quando un'applicazione viene messa in quarantena, viene inviato un messaggio di posta elettronica di notifica monouso. Se il motivo della quarantena cambia, viene inviato un messaggio di posta elettronica aggiornato che mostra il nuovo motivo della quarantena. Se non viene visualizzato un messaggio di posta elettronica:

  - Assicurarsi di aver specificato un messaggio di **posta elettronica di notifica** valido nella configurazione del provisioning per l'applicazione.
  - Assicurarsi che non sia presente alcun filtro per la posta indesiderata nella cartella posta in arrivo della notifica.
  - Assicurarsi di non aver annullato la sottoscrizione ai messaggi di posta elettronica.

## <a name="why-is-my-application-in-quarantine"></a>Perché l'applicazione è in quarantena?

Una richiesta di Microsoft Graph per ottenere lo stato del processo di provisioning Mostra il motivo seguente per la quarantena:

- `EncounteredQuarantineException` indica che sono state specificate credenziali non valide. Il servizio di provisioning non è in grado di stabilire una connessione tra il sistema di origine e il sistema di destinazione.

- `EncounteredEscrowProportionThreshold` indica che il provisioning ha superato la soglia di deposito. Questa condizione si verifica quando non è possibile eseguire più del 60% degli eventi di provisioning.

- `QuarantineOnDemand` indica che è stato rilevato un problema con l'applicazione e che è stato impostato manualmente per la quarantena.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Ricerca per categorie dismettere in quarantena l'applicazione?

Per prima cosa, risolvere il problema che ha causato la messa in quarantena dell'applicazione.

- Controllare le impostazioni di provisioning dell'applicazione per verificare di avere [immesso credenziali amministrative valide](configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD deve essere in grado di stabilire una relazione di trust con l'applicazione di destinazione. Assicurarsi di aver immesso le credenziali valide e che l'account disponga delle autorizzazioni necessarie.

- Esaminare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per esaminare in modo approfondito gli errori che causano la quarantena e risolvere l'errore. Per accedere ai log di provisioning nel portale di Azure, passare a **Azure Active Directory** &gt; **app aziendali** &gt; **log di provisioning (anteprima)** nella sezione **attività** .

Dopo aver risolto il problema, riavviare il processo di provisioning. Alcune modifiche apportate alle impostazioni di provisioning dell'applicazione, ad esempio i mapping degli attributi o i filtri di ambito, riavvieranno automaticamente il provisioning. L'indicatore di stato nella pagina di **provisioning** dell'applicazione indica il momento dell'ultimo avvio del provisioning. Se è necessario riavviare manualmente il processo di provisioning, usare uno dei metodi seguenti:  

- Usare il portale di Azure per riavviare il processo di provisioning. Nella pagina **provisioning** dell'applicazione in **Impostazioni**Selezionare **Cancella stato e riavvia sincronizzazione** e impostare stato del **provisioning** **su on**. Questa azione Riavvia completamente il servizio di provisioning, operazione che può richiedere del tempo. Un ciclo iniziale completo verrà eseguito di nuovo, che cancella i limiti di deposito, rimuove l'app dalla quarantena e cancella tutte le filigrane.

- Usare Microsoft Graph per [riavviare il processo di provisioning](https://docs.microsoft.com/en-us/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Si avrà il controllo completo su ciò che viene riavviato. È possibile scegliere di cancellare i riconoscimenti (per riavviare il contatore del deposito per lo stato di quarantena), cancellare la quarantena (per rimuovere l'applicazione dalla quarantena) o cancellare le filigrane. Usare la richiesta seguente:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`