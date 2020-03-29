---
title: Configurare il flusso di lavoro per il consenso dell'amministratore - Azure Active Directory Documenti Microsoft
description: Informazioni su come configurare un modo per gli utenti finali di richiedere l'accesso alle applicazioni che richiedono il consenso dell'amministratore.
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
ms.openlocfilehash: 83b3f0d97daf0b4ac17f74981119b380d1776d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430212"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Configurare il flusso di lavoro di consenso dell'amministratore (anteprima)Configure the admin consent workflow (preview)

In questo articolo viene descritto come abilitare la funzionalità del flusso di lavoro di consenso dell'amministratore (anteprima), che consente agli utenti finali di richiedere l'accesso alle applicazioni che richiedono il consenso dell'amministratore.

Senza un flusso di lavoro di consenso dell'amministratore, un utente in un tenant in cui il consenso dell'utente è disabilitato verrà bloccato quando tenta di accedere a qualsiasi app che richiede le autorizzazioni per accedere ai dati dell'organizzazione. L'utente visualizza un messaggio di errore generico che indica che non è autorizzato ad accedere all'app e deve chiedere aiuto all'amministratore. Ma spesso, l'utente non sa a chi rivolgersi, quindi rinuncia o crea un nuovo account locale nell'applicazione. Anche quando un amministratore riceve una notifica, non esiste sempre un processo semplificato per consentire all'amministratore di concedere l'accesso e inviare una notifica agli utenti.
 
Il flusso di lavoro di consenso dell'amministratore offre agli amministratori un modo sicuro per concedere l'accesso alle applicazioni che richiedono l'approvazione dell'amministratore. Quando un utente tenta di accedere a un'applicazione ma non è in grado di fornire il consenso, può inviare una richiesta di approvazione dell'amministratore. La richiesta viene inviata tramite posta elettronica agli amministratori designati come revisori. Un revisore esegue un'azione sulla richiesta e l'utente riceve una notifica dell'azione.

Per approvare le richieste, un revisore deve essere un amministratore globale, un amministratore di applicazioni cloud o un amministratore dell'applicazione. Il revisore deve avere già uno di questi ruoli di amministratore assegnati. semplicemente designandoli come un revisore non eleva i loro privilegi.

## <a name="enable-the-admin-consent-workflow"></a>Abilitare il flusso di lavoro di consenso dell'amministratoreEnable the admin consent workflow

Per abilitare il flusso di lavoro di consenso dell'amministratore e scegliere i revisori:

1. Accedere al [portale](https://portal.azure.com) di Azure come amministratore globale.
2. Fare clic su **Tutti i servizi** nella parte superiore del menu di spostamento a sinistra. Aprire l'estensione **Azure Active Directory**.
3. Nella casella di ricerca del filtro digitare "**Azure Active Directory**" e selezionare **l'elemento di Azure Active Directory.**
4. Scegliere **Applicazioni aziendali** dal menu di spostamento. 
5. In **Gestisci**selezionare **Impostazioni utente**.
6. In Richieste di **consenso amministratore (anteprima)** impostare **Gli utenti possono richiedere il consenso dell'amministratore alle app a cui non sono in grado di acconsentire a** **Sì**.

   ![Configurare le impostazioni del flusso di lavoro di consenso amministrativo](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Configurare le seguenti impostazioni:

   * **Selezionare gli utenti per esaminare le richieste di consenso dell'amministratore**. Selezionare i revisori per questo flusso di lavoro da un set di utenti con i ruoli di amministratore globale, amministratore dell'applicazione cloud e amministratore dell'applicazione.
   * **Gli utenti selezionati riceveranno notifiche e-mail per le richieste**. Abilitare o disabilitare le notifiche tramite posta elettronica ai revisori quando viene effettuata una richiesta.  
   * **Gli utenti selezionati riceveranno promemoria**di scadenza delle richieste. Abilitare o disabilitare le notifiche tramite posta elettronica di promemoria per i revisori quando una richiesta sta per scadere.  
   * La richiesta di **consenso scade dopo (giorni)**. Specificare per quanto tempo le richieste rimangano valide.

7. Selezionare **Salva**. L'abilitazione della funzionalità può richiedere fino a un'ora.

> [!NOTE]
> È possibile aggiungere o rimuovere revisori per questo flusso di lavoro modificando l'elenco **Seleziona revisori richieste consenso amministratore.** Si noti che una limitazione corrente di questa funzionalità è che i revisori possono mantenere la possibilità di esaminare le richieste effettuate mentre sono state designate come revisore.

## <a name="how-users-request-admin-consent"></a>Come gli utenti richiedono il consenso dell'amministratore

Dopo aver abilitato il flusso di lavoro di consenso dell'amministratore, gli utenti possono richiedere l'approvazione dell'amministratore per un'applicazione a cui non sono autorizzati ad acconsentire. I passaggi seguenti descrivono l'esperienza dell'utente quando si richiede l'approvazione. 

1. L'utente tenta di accedere all'applicazione.

2. Viene visualizzato il messaggio **Approvazione richiesta.** L'utente digita una motivazione per la necessità di accedere all'app e quindi seleziona **Richiedi approvazione**.

   ![Richiesta e giustificazione dell'utente del consenso dell'amministratore](media/configure-admin-consent-workflow/end-user-justification.png)

3. Un messaggio **di richiesta inviata** conferma che la richiesta è stata inviata all'amministratore. Se l'utente invia più richieste, solo la prima richiesta viene inviata all'amministratore.

   ![Richiesta e giustificazione dell'utente del consenso dell'amministratore](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. L'utente riceve una notifica tramite posta elettronica quando la richiesta viene approvata, rifiutata o bloccata. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Esaminare ed eseguire azioni sulle richieste di consenso dell'amministratoreReview and take action on admin consent requests

Per esaminare le richieste di consenso dell'amministratore ed eseguire un'azione:

1. Accedere al [portale](https://portal.azure.com) di Azure come uno dei revisori registrati del flusso di lavoro di consenso dell'amministratore.
2. Selezionare **Tutti i servizi** nella parte superiore del menu di navigazione a sinistra. Aprire l'estensione **Azure Active Directory**.
3. Nella casella di ricerca del filtro digitare "**Azure Active Directory**" e selezionare l'elemento di Azure Active **Directory.**
4. Scegliere **Applicazioni aziendali** dal menu di spostamento.
5. In **Attività**selezionare Richieste di **consenso amministratore (anteprima)**.

   > [!NOTE]
   > I revisori vedranno solo le richieste di amministrazione create dopo che sono state designate come revisore.

1. Selezionare l'applicazione richiesta.
2. Esaminare i dettagli relativi alla richiesta:  

   * Per vedere chi richiede l'accesso e perché, selezionare la scheda **Richiesto da.**
   * Per visualizzare le autorizzazioni richieste dall'applicazione, selezionare **Controlla autorizzazioni e consente di**ottenere il consenso .

8. Valutare la richiesta ed eseguire l'azione appropriata:

   * **Approvare la richiesta**. Per approvare una richiesta, concedere il consenso dell'amministratore all'applicazione. Una volta approvata una richiesta, tutti i richiedenti ricevono una notifica che gli è stato concesso l'accesso.  
   * **Negare la richiesta**. Per rifiutare una richiesta, è necessario fornire una giustificazione che verrà fornita a tutti i richiedenti. Una volta negata una richiesta, a tutti i richiedenti viene notificato che è stato loro negato l'accesso all'applicazione. La negazione di una richiesta non impedirà agli utenti di richiedere nuovamente il consenso dell'amministratore all'app in futuro.  
   * **Bloccare la richiesta**. Per bloccare una richiesta, è necessario fornire una giustificazione che verrà fornita a tutti i richiedenti. Una volta bloccata una richiesta, a tutti i richiedenti viene notificato che è stato negato l'accesso all'applicazione. Il blocco di una richiesta crea un oggetto entità servizio per l'applicazione nel tenant in uno stato disabilitato. Gli utenti non saranno in grado di richiedere il consenso dell'amministratore per l'applicazione in futuro.
 
## <a name="email-notifications"></a>Notifiche di posta elettronica
 
Se configurato, tutti i revisori riceveranno notifiche e-mail quando:

* È stata creata una nuova richiesta
* Una richiesta è scaduta
* Una richiesta sta per scadere la data di scadenza  
 
I richiedenti riceveranno notifiche via email quando:

* Inviano una nuova richiesta di accesso
* La loro richiesta è scaduta
* La loro richiesta è stata respinta o bloccata
* La loro richiesta è stata approvata
 
## <a name="audit-logs"></a>Log di controllo 
 
Nella tabella seguente vengono descritti gli scenari e i valori di controllo disponibili per il flusso di lavoro di consenso dell'amministratore. 

> [!NOTE]
> Il contesto utente dell'attore di controllo è attualmente mancante in tutti gli scenari. Si tratta di una limitazione nota nella versione di anteprima.


|Scenario  |Servizio di controllo  |Categoria di controllo  |Attività di controllo  |Attore di controllo  |Limitazioni del registro di controllo  |
|---------|---------|---------|---------|---------|---------|
|Amministratore che abilita il flusso di lavoro delle richieste di consenso        |Verifiche di accesso           |Gestione utente           |Creare un modello di criteri di governanceCreate governance policy template          |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente            |
|Amministrazione disabilitazione del flusso di lavoro delle richieste di consenso       |Verifiche di accesso           |Gestione utente           |Eliminare il modello di criteri di governanceDelete governance policy template          |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente           |
|Amministratore che aggiorna le configurazioni del flusso di lavoro di consenso        |Verifiche di accesso           |Gestione utente           |Aggiornare il modello di criteri di governanceUpdate governance policy template          |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente           |
|Utente finale che crea una richiesta di consenso dell'amministratore per un'app       |Verifiche di accesso           |Policy         |Crea richiesta           |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente           |
|Revisori che approvano una richiesta di consenso dell'amministratore       |Verifiche di accesso           |Gestione utente           |Approvare tutte le richieste nel flusso aziendale          |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente o l'ID dell'app a cui è stato concesso il consenso dell'amministratore.           |
|Revisori che negano una richiesta di consenso dell'amministratore       |Verifiche di accesso           |Gestione utente           |Approvare tutte le richieste nel flusso aziendale          |Contesto dell'app            | Attualmente non è possibile trovare il contesto utente dell'attore che ha negato una richiesta di consenso amministratore          |

## <a name="faq"></a>Domande frequenti 

**Ho attivato questo flusso di lavoro, ma durante il test della funzionalità, perché non è possibile visualizzare il nuovo prompt "Approvazione richiesta" che consente di richiedere l'accesso?**

Dopo aver attivato la funzionalità, potrebbero essere necessario fino a 60 minuti per visualizzare l'aggiornamento da parte degli utenti finali. È possibile verificare che la configurazione abbia avuto effetto correttamente visualizzando il valore **EnableAdminConsentRequests** nell'API. `https://graph.microsoft.com/beta/settings`

**In qualità di revisore, perché non è possibile visualizzare tutte le richieste in sospeso?**

I revisori possono visualizzare solo le richieste di amministrazione create dopo che sono state designate come revisori. Pertanto, se sono stati aggiunti di recente come revisore, non verranno visualizzate le richieste create prima dell'assegnazione.

**Come revisore, perché vengono visualizzate più richieste per la stessa applicazione?**
  
Se uno sviluppatore di applicazioni ha configurato la propria app per l'utilizzo del consenso statico e dinamico per richiedere l'accesso ai dati dell'utente finale, verranno visualizzate due richieste di consenso dell'amministratore. Una richiesta rappresenta le autorizzazioni statiche e l'altra rappresenta le autorizzazioni dinamiche.

**Come richiedente, posso controllare lo stato della mia richiesta?**  

No, per ora i richiedenti sono in grado di ricevere aggiornamenti solo tramite notifiche e-mail.

**Come revisore, è possibile approvare l'applicazione, ma non per tutti?**
 
Se si teme di concedere il consenso dell'amministratore e consentire a tutti gli utenti del tenant di utilizzare l'applicazione, è consigliabile rifiutare la richiesta. Concedere quindi manualmente il consenso dell'amministratore limitando l'accesso all'applicazione richiedendo l'assegnazione dell'utente e assegnando utenti o gruppi all'applicazione. Per altre informazioni, vedere [Metodi per l'assegnazione di utenti e gruppi](methods-for-assigning-users-and-groups.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul consenso alle applicazioni, vedere [framework di consenso di Azure Active Directory](../develop/consent-framework.md).

[Configurare il modo in cui gli utenti finali consentono di concedere il consenso alle applicazioni](configure-user-consent.md)

[Concedere il consenso di amministratore a livello di tenant a un'applicazioneGrant tenant-wide admin consent to an application](grant-admin-consent.md)

[Autorizzazioni e consenso nella piattaforma di identità Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD on StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
