---
title: Stato del provisioning dell'applicazione della quarantena | Microsoft Docs
description: Quando è stata configurata un'applicazione per il provisioning utenti automatico, scoprire che cosa significa lo stato di provisioning della quarantena e come eliminarlo.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: c1e0039133b7f9a7ae827e348640f6379b7f10ac
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593931"
---
# <a name="application-provisioning-in-quarantine-status"></a>Provisioning dell'applicazione in stato di quarantena

Il servizio di provisioning Azure AD monitora l'integrità della configurazione e inserisce le app non integre in uno stato di "quarantena". Se la maggior parte o tutte le chiamate effettuate sul sistema di destinazione hanno esito negativo a causa di un errore, ad esempio credenziali di amministratore non valide, il processo di provisioning è contrassegnato come in quarantena.

Durante la quarantena, la frequenza dei cicli incrementali viene gradualmente ridotta a una volta al giorno. Il processo di provisioning viene rimosso dalla quarantena dopo che tutti gli errori sono stati risolti e viene avviato il ciclo di sincronizzazione successivo. Se il processo di provisioning rimane in quarantena per più di quattro settimane, il processo di provisioning viene disabilitato (arresta l'esecuzione).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Ricerca per categorie verificare se l'applicazione è in quarantena?

Esistono tre modi per verificare se un'applicazione è in quarantena:
  
- Nella portale di Azure passare a **Azure Active Directory** > **applicazioni** > &lt;*aziendali nome*&gt; > applicazione**provisioning** ed esaminare l'indicatore di stato per un messaggio di quarantena.   

  ![Barra di stato del provisioning che mostra lo stato della quarantena](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Nella portale di Azure passare a **Azure Active Directory** > **log di controllo** > filtro per **attività: quarantena** e verificare la cronologia di quarantena. Mentre la visualizzazione nell'indicatore di stato, come descritto in precedenza, Mostra se il provisioning è attualmente in quarantena, i log di controllo consentono di visualizzare la cronologia di quarantena per un'applicazione. 

- Usare la richiesta Microsoft Graph [Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) per ottenere lo stato del processo di provisioning a livello di codice:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Controllare la posta elettronica. Quando un'applicazione viene messa in quarantena, viene inviato un messaggio di posta elettronica di notifica monouso. Se il motivo della quarantena cambia, viene inviato un messaggio di posta elettronica aggiornato che mostra il nuovo motivo della quarantena. Se non viene visualizzato un messaggio di posta elettronica:

  - Assicurarsi di aver specificato un messaggio di **posta elettronica di notifica** valido nella configurazione del provisioning per l'applicazione.
  - Assicurarsi che non sia presente alcun filtro per la posta indesiderata nella cartella posta in arrivo della notifica.
  - Assicurarsi di non aver annullato la sottoscrizione ai messaggi di posta elettronica.

## <a name="why-is-my-application-in-quarantine"></a>Perché l'applicazione è in quarantena?

|Descrizione|Azione consigliata|
|---|---|
|**Problema di conformità di SCIM:** È stata restituita una risposta HTTP/404 non trovata anziché la risposta HTTP/200 OK prevista. In questo caso il servizio di provisioning Azure AD ha effettuato una richiesta all'applicazione di destinazione e ha ricevuto una risposta imprevista.|Controllare la sezione credenziali amministratore per verificare se l'applicazione richiede la specifica dell'URL del tenant e verificare che l'URL sia corretto. Se non viene visualizzato alcun problema, contattare lo sviluppatore dell'applicazione per assicurarsi che il servizio sia conforme a SCIM. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Credenziali non valide:** Quando si tenta di autorizzare l'accesso all'applicazione di destinazione, è stata ricevuta una risposta dall'applicazione di destinazione che indica che le credenziali specificate non sono valide.|Passare alla sezione credenziali amministratore dell'interfaccia utente di configurazione del provisioning e autorizzare di nuovo l'accesso con credenziali valide. Se l'applicazione si trova nella raccolta, vedere l'esercitazione sulla configurazione dell'applicazione per eventuali ulteriori passaggi necessari.|
|**Ruoli duplicati:** I ruoli importati da alcune applicazioni, ad esempio Salesforce e Zendesk, devono essere univoci. |Passare al [manifesto](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) dell'applicazione nella portale di Azure e rimuovere il ruolo duplicato.|

 Una richiesta di Microsoft Graph per ottenere lo stato del processo di provisioning Mostra il motivo seguente per la quarantena:

- `EncounteredQuarantineException`indica che sono state specificate credenziali non valide. Il servizio di provisioning non è in grado di stabilire una connessione tra il sistema di origine e il sistema di destinazione.

- `EncounteredEscrowProportionThreshold`indica che il provisioning ha superato la soglia del deposito. Questa condizione si verifica quando non è possibile eseguire più del 60% degli eventi di provisioning.

- `QuarantineOnDemand`indica che è stato rilevato un problema con l'applicazione e che è stato impostato manualmente per la quarantena.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Ricerca per categorie dismettere in quarantena l'applicazione?

Per prima cosa, risolvere il problema che ha causato la messa in quarantena dell'applicazione.

- Controllare le impostazioni di provisioning dell'applicazione per verificare di avere [immesso credenziali amministrative valide](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD deve essere in grado di stabilire una relazione di trust con l'applicazione di destinazione. Assicurarsi di aver immesso le credenziali valide e che l'account disponga delle autorizzazioni necessarie.

- Esaminare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per esaminare in modo approfondito gli errori che causano la quarantena e risolvere l'errore. Per accedere ai log di provisioning nel portale di Azure, passare **a Azure Active Directory** &gt; **log di provisioning** di **app** &gt; aziendali (anteprima) nella sezione **attività** .

Dopo aver risolto il problema, riavviare il processo di provisioning. Alcune modifiche apportate alle impostazioni di provisioning dell'applicazione, ad esempio i mapping degli attributi o i filtri di ambito, riavvieranno automaticamente il provisioning. L'indicatore di stato nella pagina di **provisioning** dell'applicazione indica il momento dell'ultimo avvio del provisioning. Se è necessario riavviare manualmente il processo di provisioning, usare uno dei metodi seguenti:  

- Usare il portale di Azure per riavviare il processo di provisioning. Nella pagina **provisioning** dell'applicazione in **Impostazioni**Selezionare **Cancella stato e riavvia sincronizzazione** e impostare stato del **provisioning** **su on**. Questa azione Riavvia completamente il servizio di provisioning, operazione che può richiedere del tempo. Un ciclo iniziale completo verrà eseguito di nuovo, che cancella i limiti di deposito, rimuove l'app dalla quarantena e cancella tutte le filigrane.

- Usare Microsoft Graph per [riavviare il processo di provisioning](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Si avrà il controllo completo su ciò che viene riavviato. È possibile scegliere di cancellare i riconoscimenti (per riavviare il contatore del deposito per lo stato di quarantena), cancellare la quarantena (per rimuovere l'applicazione dalla quarantena) o cancellare le filigrane. Usare la richiesta seguente:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
