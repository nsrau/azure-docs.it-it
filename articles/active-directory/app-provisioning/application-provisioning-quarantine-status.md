---
title: Stato di provisioning dell'applicazione Di Quarantena Documenti Microsoft
description: Dopo aver configurato un'applicazione per il provisioning automatico degli utenti, scopri cosa significa lo stato di provisioning Quarantena e come cancellarla.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563c049bf3d1606e87db54e3b003dac987594610
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154628"
---
# <a name="application-provisioning-in-quarantine-status"></a>Provisioning dell'applicazione in stato di quarantena

Il servizio di provisioning di Azure AD monitora l'integrità della configurazione e inserisce le app non integre in uno stato di "quarantena". Se la maggior parte o tutte le chiamate effettuate sul sistema di destinazione hanno esito negativo in modo coerente a causa di un errore, ad esempio credenziali di amministratore non valide, il processo di provisioning viene contrassegnato come in quarantena.

Durante la quarantena, la frequenza dei cicli incrementali viene gradualmente ridotta a una volta al giorno. Il processo di provisioning viene rimosso dalla quarantena dopo che tutti gli errori sono stati corretti e viene avviato il successivo ciclo di sincronizzazione. Se il processo di provisioning rimane in quarantena per più di quattro settimane, il processo di provisioning viene disabilitato (interrompe l'esecuzione).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Come faccio a sapere se la mia domanda è in quarantena?

Esistono tre modi per verificare se un'applicazione è in quarantena:
  
- Nel portale di Azure passare al > &lt;*nome*&gt; > dell'applicazione delle applicazioni **di Azure Active Directory** > **Enterprise****Provisioning** e scorrere fino alla barra di stato nella parte inferiore.  

  ![Barra di stato del provisioning che mostra lo stato di quarantena](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Nel portale di Azure passare a Log di**controllo** di **Azure Active Directory** > > filtrare in **Activity: Quarantine** ed esaminare la cronologia di quarantena. Mentre la visualizzazione nella barra di stato descritta in precedenza mostra se il provisioning è attualmente in quarantena, i registri di controllo consentono di visualizzare la cronologia di quarantena per un'applicazione. 

- Utilizzare la richiesta Microsoft Graph [Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) per ottenere a livello di codice lo stato del processo di provisioning:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Controlla la tua email. Quando un'applicazione viene messa in quarantena, viene inviata un'e-mail di notifica una tantera. Se il motivo della quarantena cambia, viene inviato un messaggio di posta elettronica aggiornato che mostra il nuovo motivo della quarantena. Se non vedi un'email:

  - Assicurarsi di aver specificato un messaggio di **posta elettronica** di notifica valido nella configurazione di provisioning per l'applicazione.
  - Assicurarsi che non vi sia alcun filtro antispam nella casella di posta elettronica di notifica.
  - Assicurati di non aver annullato l'iscrizione alle e-mail.

## <a name="why-is-my-application-in-quarantine"></a>Perché la mia domanda è in quarantena?

Una richiesta di Microsoft Graph per ottenere lo stato del processo di provisioning mostra il motivo della quarantena seguente:

- `EncounteredQuarantineException`indica che sono state fornite credenziali non valide. Il servizio di provisioning non è in grado di stabilire una connessione tra il sistema di origine e il sistema di destinazione.

- `EncounteredEscrowProportionThreshold`indica che il provisioning ha superato la soglia di deposito a garanzia. Questa condizione si verifica quando più del 60% degli eventi di provisioning non è riuscito.

- `QuarantineOnDemand`significa che abbiamo rilevato un problema con l'applicazione e che l'abbiamo impostato manualmente in quarantena.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Come faccio a far uscire la mia domanda dalla quarantena?

Risolvere innanzitutto il problema che ha causato l'inserimento dell'applicazione in quarantena.

- Controllare le impostazioni di provisioning dell'applicazione per assicurarsi di aver [immesso credenziali di amministratore valide.](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning) Azure AD deve essere in grado di stabilire una relazione di trust con l'applicazione di destinazione. Assicurarsi di aver immesso credenziali valide e che l'account disponga delle autorizzazioni necessarie.

- Esaminare i log di [provisioning](../reports-monitoring/concept-provisioning-logs.md) per analizzare ulteriormente gli errori che causano la quarantena e risolvere l'errore. Accedere ai log di provisioning nel portale di Azure accedendo ai log di provisioning delle app **di Azure Active Directory** &gt; Enterprise (anteprima) nella sezione Attività.Access the provisioning logs in the Azure portal by going to Azure Active Directory **Enterprise Apps** &gt; **Provisioning logs (preview)** in the **Activity** section.

Dopo aver risolto il problema, riavviare il processo di provisioning. Alcune modifiche alle impostazioni di provisioning dell'applicazione, ad esempio i mapping degli attributi o i filtri di ambito, riavvieranno automaticamente il provisioning. La barra di avanzamento nella pagina **Provisioning** dell'applicazione indica l'ultimo avvio del provisioning. Se è necessario riavviare manualmente il processo di provisioning, utilizzare uno dei metodi seguenti:  

- Usare il portale di Azure per riavviare il processo di provisioning. Nella pagina **Provisioning** dell'applicazione, in **Impostazioni,** selezionare **Cancella stato e riavvia la sincronizzazione** e impostare **Stato provisioning** su **Attivato**. Questa azione riavvia completamente il servizio di provisioning, operazione che può richiedere del tempo. Verrà eseguito di nuovo un ciclo iniziale completo, che cancella i deposito a garanzia, rimuove l'app dalla quarantena e cancella eventuali filigrane.

- Utilizzare Microsoft Graph per [riavviare il processo](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)di provisioning. Avrai il pieno controllo su ciò che riavvierai. È possibile scegliere di cancellare i deposito a garanzia (per riavviare il contatore dei deposito a garanzia che si accumula verso lo stato di quarantena), cancellare la quarantena (per rimuovere l'applicazione dalla quarantena) o cancellare le filigrane. Usare la richiesta seguente:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
