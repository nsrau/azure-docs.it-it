---
title: Configurare il flusso di lavoro di consenso dell'amministratore-Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare un modo per consentire agli utenti finali di richiedere l'accesso alle applicazioni che richiedono il consenso dell'amministratore.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5d1654556df455accb7f615442d3a97952c5e50
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180420"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Configurare il flusso di lavoro di consenso dell'amministratore (anteprima)

Questo articolo descrive come abilitare la funzionalità di flusso di lavoro di consenso dell'amministratore (anteprima), che consente agli utenti finali di richiedere l'accesso alle applicazioni che richiedono il consenso dell'amministratore.

Senza un flusso di lavoro di consenso dell'amministratore, un utente in un tenant in cui il consenso dell'utente è disabilitato verrà bloccato quando tenterà di accedere a qualsiasi app che richiede le autorizzazioni per accedere ai dati aziendali. L'utente visualizza un messaggio di errore generico che indica che non è autorizzato ad accedere all'app ed è necessario richiedere assistenza all'amministratore. Spesso, tuttavia, l'utente non è in grado di contattare il cliente, in modo che sia possibile rinunciare o creare un nuovo account locale nell'applicazione. Anche quando un amministratore riceve una notifica, non è sempre un processo semplificato per consentire all'amministratore di concedere l'accesso e inviare una notifica agli utenti.
 
Il flusso di lavoro di consenso dell'amministratore offre agli amministratori un modo sicuro per concedere l'accesso alle applicazioni che richiedono l'approvazione dell'amministratore. Quando un utente tenta di accedere a un'applicazione, ma non è in grado di fornire il consenso, può inviare una richiesta per l'approvazione dell'amministratore. La richiesta viene inviata tramite posta elettronica agli amministratori che sono stati designati come revisori. Un revisore esegue un'azione sulla richiesta e l'utente riceve una notifica dell'azione.

Per approvare le richieste, un revisore deve essere un amministratore globale, un amministratore di applicazioni cloud o un amministratore dell'applicazione. Il revisore deve avere già uno di questi ruoli amministrativi assegnati. semplicemente designarli come revisore non eleva i loro privilegi.

## <a name="enable-the-admin-consent-workflow"></a>Abilita il flusso di lavoro di consenso dell'amministratore

Per abilitare il flusso di lavoro di consenso dell'amministratore e scegliere i revisori:

1. Accedere al di [portale di Azure](https://portal.azure.com) come amministratore globale.
2. Fare clic su **tutti i servizi** nella parte superiore del menu di spostamento a sinistra. Si apre l' **estensione Azure Active Directory** .
3. Nella casella di ricerca filtro digitare "**Azure Active Directory**" e selezionare **il Azure Active Directory** elemento.
4. Nel menu di navigazione fare clic su **applicazioni aziendali**. 
5. In **Gestisci**selezionare **impostazioni utente**.
6. In **richieste di consenso dell'amministratore (anteprima)** , impostare **gli utenti possono richiedere il consenso dell'amministratore alle app a cui non sono in grado** di fornire il consenso **.**

   ![Configurare le impostazioni del flusso di lavoro di consenso dell'amministratore](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Configurare le seguenti impostazioni:

   * **Selezionare gli utenti per esaminare le richieste di consenso dell'amministratore**. Selezionare revisori per questo flusso di lavoro da un set di utenti che dispongono dei ruoli amministratore globale, amministratore applicazione cloud e amministratore applicazione.
   * **Gli utenti selezionati riceveranno le notifiche tramite posta elettronica per le richieste**. Consente di abilitare o disabilitare le notifiche tramite posta elettronica ai revisori quando viene effettuata una richiesta.  
   * **Gli utenti selezionati riceveranno promemoria**per la scadenza delle richieste. Abilitare o disabilitare le notifiche di posta elettronica di promemoria ai revisori quando una richiesta sta per scadere.  
   * La **richiesta di consenso scade dopo (giorni)** . Specificare per quanto tempo le richieste resteranno valide.

7. Selezionare **Salva**. Potrebbe essere necessaria fino a un'ora prima che la funzionalità venga abilitata.

> [!NOTE]
> È possibile aggiungere o rimuovere revisori per questo flusso di lavoro modificando l'elenco **selezionare i revisori delle richieste di consenso dell'amministratore** . Si noti che una limitazione attuale di questa funzionalità è che i revisori possono mantenere la possibilità di rivedere le richieste effettuate durante la designazione come revisore.

## <a name="how-users-request-admin-consent"></a>Come gli utenti richiedono il consenso dell'amministratore

Dopo l'abilitazione del flusso di lavoro di consenso dell'amministratore, gli utenti possono richiedere l'approvazione dell'amministratore per un'applicazione a cui non sono autorizzati. Nei passaggi seguenti viene descritta l'esperienza dell'utente quando si richiede l'approvazione. 

1. L'utente tenta di accedere all'applicazione.

2. Viene visualizzato il messaggio **approvazione richiesta** . L'utente digita una giustificazione per la necessità di accedere all'app e quindi seleziona **Richiedi approvazione**.

   ![Richiesta e giustificazione dell'utente di consenso dell'amministratore](media/configure-admin-consent-workflow/end-user-justification.png)

3. Un messaggio di **richiesta inviata** conferma che la richiesta è stata inviata all'amministratore. Se l'utente invia diverse richieste, solo la prima richiesta viene inviata all'amministratore.

   ![Richiesta e giustificazione dell'utente di consenso dell'amministratore](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. L'utente riceve una notifica di posta elettronica quando la richiesta viene approvata, negata o bloccata. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Esaminare e intervenire sulle richieste di consenso dell'amministratore

Per esaminare le richieste di consenso dell'amministratore e intraprendere l'azione:

1. Accedere al di [portale di Azure](https://portal.azure.com) come uno dei revisori registrati del flusso di lavoro di consenso dell'amministratore.
2. Selezionare **tutti i servizi** nella parte superiore del menu di spostamento a sinistra. Si apre l' **estensione Azure Active Directory** .
3. Nella casella di ricerca filtro digitare "**Azure Active Directory**" e selezionare il **Azure Active Directory** elemento.
4. Nel menu di navigazione fare clic su **applicazioni aziendali**.
5. In **attività**selezionare **richieste di consenso dell'amministratore (anteprima)** .

   > [!NOTE]
   > I revisori vedranno solo le richieste di amministratore create dopo essere state designate come revisore.

1. Selezionare l'applicazione richiesta.
2. Esaminare i dettagli relativi alla richiesta:  

   * Per visualizzare chi richiede l'accesso e perché selezionare la scheda **richiesto da** .
   * Per visualizzare le autorizzazioni richieste dall'applicazione, selezionare **Verifica autorizzazioni e consenso**.

8. Valutare la richiesta e intraprendere l'azione appropriata:

   * **Approva la richiesta**. Per approvare una richiesta, concedere il consenso dell'amministratore all'applicazione. Una volta approvata una richiesta, a tutti i richiedenti viene notificato che è stato concesso l'accesso.  
   * **Negare la richiesta**. Per negare una richiesta, è necessario fornire una giustificazione che verrà fornita a tutti i richiedenti. Una volta negata una richiesta, a tutti i richiedenti viene notificato che è stato negato l'accesso all'applicazione. La negazione di una richiesta non impedisce agli utenti di richiedere il consenso dell'amministratore per l'app in futuro.  
   * **Blocca la richiesta**. Per bloccare una richiesta, è necessario fornire una giustificazione che verrà fornita a tutti i richiedenti. Una volta bloccata una richiesta, a tutti i richiedenti viene notificato che è stato negato l'accesso all'applicazione. Bloccando una richiesta viene creato un oggetto entità servizio per l'applicazione nel tenant con stato disabilitato. Gli utenti non saranno in grado di richiedere il consenso dell'amministratore per l'applicazione in futuro.
 
## <a name="email-notifications"></a>Notifiche tramite posta elettronica
 
Se configurato, tutti i revisori riceveranno le notifiche tramite posta elettronica quando:

* È stata creata una nuova richiesta
* Una richiesta è scaduta
* Una richiesta è prossima alla data di scadenza  
 
I richiedenti riceveranno le notifiche tramite posta elettronica quando:

* Inviano una nuova richiesta di accesso
* La richiesta è scaduta
* La richiesta è stata negata o bloccata
* La richiesta è stata approvata
 
## <a name="audit-logs"></a>Log di controllo 
 
La tabella seguente descrive gli scenari e i valori di controllo disponibili per il flusso di lavoro di consenso dell'amministratore. 

> [!NOTE]
> Il contesto utente dell'attore di controllo non è attualmente presente in tutti gli scenari. Si tratta di un limite noto nella versione di anteprima.


|Scenario  |Servizio di controllo  |Categoria di controllo  |Attività di controllo  |Attore di controllo  |Limitazioni dei log di controllo  |
|---------|---------|---------|---------|---------|---------|
|Amministratore che Abilita il flusso di lavoro della richiesta di consenso        |Verifiche di accesso           |UserManagement           |Crea modello di criteri di governance          |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente            |
|Amministratore che disabilita il flusso di lavoro della richiesta di consenso       |Verifiche di accesso           |UserManagement           |Elimina modello criteri di governance          |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente           |
|Amministratore che aggiorna le configurazioni del flusso di lavoro di consenso        |Verifiche di accesso           |UserManagement           |Aggiornare il modello di criteri di governance          |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente           |
|Creazione di una richiesta di consenso dell'amministratore per un'app da parte dell'utente finale       |Verifiche di accesso           |Policy         |Crea richiesta           |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente           |
|Revisori che approvano una richiesta di consenso dell'amministratore       |Verifiche di accesso           |UserManagement           |Approva tutte le richieste nel flusso aziendale          |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente o l'ID app a cui è stato concesso il consenso dell'amministratore.           |
|Revisori che negano una richiesta di consenso dell'amministratore       |Verifiche di accesso           |UserManagement           |Approva tutte le richieste nel flusso aziendale          |Contesto dell'app            | Attualmente non è possibile trovare il contesto utente dell'attore che ha negato una richiesta di consenso dell'amministratore          |

## <a name="faq"></a>FAQ 

**Ho attivato il flusso di lavoro, ma durante il test della funzionalità, perché non è possibile visualizzare il nuovo prompt "approvazione richiesta", che mi consente di richiedere l'accesso?**

Dopo aver attivato la funzionalità, potrebbero essere necessari fino a 60 minuti per consentire agli utenti finali di visualizzare l'aggiornamento. È possibile verificare che la configurazione sia stata applicata correttamente visualizzando il valore **EnableAdminConsentRequests** nell'API `https://graph.microsoft.com/beta/settings`.

**Come revisore, perché non è possibile visualizzare tutte le richieste in sospeso?**

I revisori possono visualizzare solo le richieste di amministrazione create dopo che sono state designate come revisore. Quindi, se l'utente è stato aggiunto di recente come revisore, non vengono visualizzate le richieste create prima dell'assegnazione.

**Come revisore, perché vengono visualizzate più richieste per la stessa applicazione?**
  
Se uno sviluppatore di applicazioni ha configurato l'app per l'uso del consenso statico e dinamico per richiedere l'accesso ai dati dell'utente finale, verranno visualizzate due richieste di consenso dell'amministratore. Una richiesta rappresenta le autorizzazioni statiche e l'altra rappresenta le autorizzazioni dinamiche.

**Come richiedente, posso controllare lo stato della mia richiesta?**  

No, per ora i richiedenti sono in grado di ottenere solo gli aggiornamenti tramite notifiche tramite posta elettronica.

**Come revisore, è possibile approvare l'applicazione, ma non per tutti?**
 
Se si è preoccupati di concedere il consenso dell'amministratore e consentire a tutti gli utenti del tenant di usare l'applicazione, si consiglia di negare la richiesta. Quindi, concedere manualmente il consenso dell'amministratore limitando l'accesso all'applicazione richiedendo l'assegnazione dell'utente e assegnando gli utenti o i gruppi all'applicazione. Per altre informazioni, vedere [Metodi per l'assegnazione di utenti e gruppi](methods-for-assigning-users-and-groups.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul consenso alle applicazioni, vedere [framework di consenso di Azure Active Directory](../develop/consent-framework.md).

[Consenso e integrazione di applicazioni con Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Consenso e concessione delle autorizzazioni per le app con convergenza di Azure Active Directory v2.0](../develop/active-directory-v2-scopes.md)

[Azure AD in Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
