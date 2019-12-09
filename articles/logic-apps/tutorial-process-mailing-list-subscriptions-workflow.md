---
title: Creare flussi di lavoro automatizzati basati su approvazione
description: Esercitazione - Creare un flusso di lavoro automatizzato basato su approvazione che elabora le sottoscrizioni di liste di distribuzione tramite App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: bcd90859066911797d78737187cae6d361029ddd
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74784664"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Esercitazione: Creare flussi di lavoro automatizzati basati su approvazione tramite App per la logica di Azure

Questa esercitazione illustra come creare un'[app per la logica](../logic-apps/logic-apps-overview.md) che automatizza un flusso di lavoro basato su approvazione. In particolare, questa app per la logica elabora le richieste di sottoscrizione di una lista di distribuzione gestita dal servizio [MailChimp](https://mailchimp.com/). Questa app per la logica monitora un account di posta elettronica per individuare queste richieste, invia le richieste per l'approvazione e aggiunge i membri approvati alla lista di distribuzione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'app per la logica vuota.
> * Aggiungere un trigger per il monitoraggio dei messaggi di posta elettronica per individuare le richieste di sottoscrizione.
> * Aggiungere un'azione per l'invio di messaggi di posta elettronica per l'approvazione o il rifiuto di tali richieste.
> * Aggiungere una condizione per il controllo della risposta di approvazione.
> * Aggiungere un'azione per l'aggiunta dei membri approvati alla lista di distribuzione.
> * Aggiungere una condizione per il controllo dell'aggiunta o meno dei membri alla lista.
> * Aggiungere un'azione per l'invio di messaggi di posta elettronica per confermare se i membri sono stati o meno aggiunti alla lista.

Al termine, a livello generale l'app per la logica dovrebbe avere un flusso di lavoro simile al seguente:

![Panoramica generale dell'app per la logica completata](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, [iscriversi per creare un account di Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

* Un account MailChimp che contiene un elenco denominato "test-members-ML", in cui l'app per la logica può aggiungere gli indirizzi di posta elettronica dei membri approvati. Se non si ha un account, [registrarsi per ottenerne uno gratuito](https://login.mailchimp.com/signup/) e vedere [come creare una lista MailChimp](https://us17.admin.mailchimp.com/lists/#).

* Una account di posta elettronica in Office 365 Outlook o Outlook.com, che supporta i flussi di lavoro basati su approvazione. Questo articolo usa Outlook per Office 365. Se si usa un account di posta elettronica diverso, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

## <a name="create-your-logic-app"></a>Creare l'app per la logica

1. Dal menu principale di Azure selezionare **Crea una risorsa** > **Integrazione** > **App per la logica**.

   ![Creare la nuova risorsa app per la logica](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. In **Crea app per la logica** specificare le informazioni sull'app per la logica come mostrato e descritto di seguito. Al termine, selezionare **Crea**.

   ![Specificare le informazioni sull'app per la logica](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Proprietà | Valore | DESCRIZIONE |
   |----------|-------|-------------|
   | **Nome** | LA-MailingList | Il nome dell'app per la logica, che può contenere solo lettere, numeri, trattini (`-`), caratteri di sottolineatura (`_`), parentesi (`(`, `)`) e punti (`.`). Questo esempio usa "LA-MailingList". |
   | **Sottoscrizione** | <*nome-sottoscrizione-Azure*> | Il nome della sottoscrizione di Azure |
   | **Gruppo di risorse** | LA-MailingList-RG | Il nome del [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) usato per organizzare le risorse correlate. Questo esempio usa "LA-MailingList-RG". |
   | **Posizione** | Stati Uniti occidentali | L'area in cui archiviare le informazioni sull'app per la logica. Questo esempio usa "Stati Uniti occidentali". |
   | **Log Analytics** | Off | Lasciare l'impostazione **No** per la registrazione diagnostica. |
   ||||

1. Dopo che Azure ha distribuito l'app, sulla barra degli strumenti di Azure selezionare **Notifiche** > **Vai alla risorsa** per l'app per la logica distribuita.

   ![Passare alla nuova risorsa app per la logica](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   In alternativa, è possibile trovare e selezionare l'app per la logica digitando il nome nella casella di ricerca.

   Viene visualizzata la finestra Progettazione app per la logica, che mostra una pagina con un video introduttivo, oltre ai trigger e ai modelli di app per la logica più usati. In **Modelli** selezionare **App per la logica vuota**.

   ![Selezionare il modello di app per la logica vuota](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Aggiungere quindi un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) che resta in ascolto dei messaggi di posta elettronica in arrivo con richieste di sottoscrizione. Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un determinato evento o quando i nuovi dati soddisfano una condizione specifica. Per altre informazioni, vedere [Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md) (Creare la prima app per la logica).

## <a name="add-trigger-to-monitor-emails"></a>Aggiungere un trigger per il monitoraggio dei messaggi di posta elettronica

1. Nella casella di ricerca della finestra Progettazione app per la logica immettere `when email arrives` come filtro. Nell'elenco **Trigger** selezionare il trigger **All'arrivo di un nuovo messaggio di posta elettronica** per il provider di posta elettronica.

   Questo esempio usa il trigger di Office 365 Outlook:

   ![Selezionare il trigger "All'arrivo di un nuovo messaggio di posta elettronica" per il provider di posta elettronica](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Per un account aziendale o dell'istituto di istruzione di Azure, selezionare Office 365 Outlook.
   * Per gli account Microsoft personali, selezionare Outlook.com.

1. Se richiesto, accedere all'account di posta elettronica con le proprie credenziali, in modo che App per la logica possa creare una connessione con tale account.

1. Nel trigger specificare i criteri per il controllo di tutti i nuovi messaggi di posta elettronica.

   1. Specificare cartella, intervallo e frequenza per la verifica dei messaggi di posta elettronica.

      ![Specificare cartella, intervallo e frequenza per la verifica dei messaggi di posta elettronica](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Proprietà | Valore | DESCRIZIONE |
      |----------|-------|-------------|
      | **Cartella** | `Inbox` | Cartella di posta elettronica da monitorare |
      | **Interval** | `1` | Numero di intervalli di attesa tra i controlli |
      | **Frequenza** | `Hour` | Unità di tempo da usare per la ricorrenza |
      ||||

   1. Aggiungere ora un'altra proprietà al trigger, in modo da filtrare in base alla riga dell'oggetto del messaggio. Aprire l'elenco **Aggiungi nuovo parametro** e selezionare la proprietà **Filtro oggetto**.

      ![Aggiungere la proprietà "filtro oggetto" al trigger](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Per altre informazioni sulle proprietà di questo trigger, vedere le [informazioni di riferimento sul connettore Office 365 Outlook](https://docs.microsoft.com/connectors/office365/) o le [informazioni di riferimento sul connettore Outlook.com](https://docs.microsoft.com/connectors/outlook/).

   1. Quando la proprietà viene visualizzata nel trigger, immettere questo testo: `subscribe-test-members-ML`

      ![Immettere il testo per la proprietà "filtro oggetto"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Per nascondere i dettagli del trigger per il momento, fare clic sulla barra del titolo del trigger.

   ![Comprimere la forma per nascondere i dettagli](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

L'app per la logica è ora attiva, ma non esegue alcuna operazione oltre a controllare la posta elettronica in arrivo. Aggiungere quindi un'azione per la risposta all'attivazione del trigger.

## <a name="send-approval-email"></a>Inviare messaggio di posta elettronica di approvazione

Ora che è stato creato il trigger, aggiungere un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) per l'invio di un messaggio di posta elettronica per approvare o rifiutare la richiesta.

1. Nel trigger selezionare **Nuovo passaggio**. 

1. Nella casella di ricerca di **Scegliere un'azione** immettere `approval` come filtro. Nell'elenco di azioni selezionare **Invia un messaggio di posta elettronica di approvazione** per il provider di posta elettronica. 

   Questo esempio usa l'azione di Office 365 Outlook:

   ![Selezionare "Invia un messaggio di posta elettronica di approvazione"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Specificare le informazioni su questa azione come descritto di seguito: 

   ![Proprietà di dell'invio di posta elettronica di approvazione](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Proprietà | Valore | DESCRIZIONE |
   |----------|-------|-------------|
   | **To** | <*indirizzo-posta-elettronica*> | Indirizzo di posta elettronica del responsabile approvazione. Per scopi di test, è possibile usare il proprio indirizzo. Questo esempio usa l'indirizzo di posta elettronica fittizio "sophia.owen@fabrikam.com". |
   | **Oggetto** | `Approve member request for test-members-ML` | Oggetto descrittivo per il messaggio di posta elettronica |
   | **Opzioni utente** | `Approve, Reject` | Le opzioni di risposta tra cui il responsabile approvazione può scegliere. Per impostazione predefinita, il responsabile approvazione può scegliere "Approva" o "Rifiuta" come risposta. |
   ||||

   Per ora ignorare l'elenco di contenuto dinamico visualizzato quando si fa clic all'interno di specifiche caselle di modifica. Questo elenco consente di selezionare l'output disponibile di azioni precedenti che è possibile usare come input nel flusso di lavoro.

   Per altre informazioni sulle proprietà di questa azione, vedere le [informazioni di riferimento sul connettore Office 365 Outlook](https://docs.microsoft.com/connectors/office365/) o le [informazioni di riferimento sul connettore Outlook.com](https://docs.microsoft.com/connectors/outlook/).
 
1. Salvare l'app per la logica.

Aggiungere quindi una condizione per controllare la risposta selezionata del responsabile approvazione.

## <a name="check-approval-response"></a>Controllare la risposta di approvazione

1. Nell'azione **Invia messaggio di posta elettronica di approvazione** selezionare **Nuovo passaggio**.

1. In **Scegliere un'azione** selezionare **Predefinita**. Nella casella di ricerca immettere `condition` come filtro. Nell'elenco di azioni selezionare **Condizione**.

   ![Trovare e selezionare l'azione "Condizione"](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Rinominare la condizione con una descrizione migliore.

   1. Sulla barra del titolo della condizione selezionare i **puntini di sospensione** ( **...** ) > **Rinomina**.

      ![Rinominare la descrizione della condizione](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Rinominare la condizione con questa descrizione: `If request approved`

1. Creare una condizione che controlli se il responsabile approvazione ha selezionato **Approva**.

   1. A sinistra della condizione fare clic all'interno della casella **Scegliere un valore**.

   1. Nell'elenco di contenuto dinamico che viene visualizzato, in **Invia messaggio di posta elettronica di approvazione**, selezionare la proprietà **SelectedOption**.

      ![Nell'elenco di contenuto dinamico selezionare "SelectedOption"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Nella casella di confronto centrale selezionare l'operatore **è uguale a**.

   1. Nella casella **Scegliere un valore** sul lato destro della condizione immettere questo testo: `Approve`

      Al termine, la condizione avrà un aspetto simile a questo esempio:

      ![Condizione completata per l'esempio "approvato"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Salvare l'app per la logica.

Specificare quindi l'azione che deve eseguire l'app per la logica quando il revisore approva la richiesta. 

## <a name="add-member-to-mailchimp-list"></a>Aggiungere un membro all'elenco MailChimp

Impostare ora un'azione per aggiungere il membro approvato alla lista di distribuzione.

1. Nel ramo **È true** della condizione selezionare **Aggiungi un'azione**.

1. In **Scegliere un'azione** immettere `mailchimp` come filtro e selezionare l'azione **Aggiungi membro all'elenco**.

   ![Selezionare l'azione "Aggiungi membro all'elenco"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Se viene chiesto di accedere all'account MailChimp, accedere con le credenziali di MailChimp.

1. Specificare le informazioni per questa azione come mostrato e descritto di seguito:

   ![Specificare le informazioni per "Aggiungi membro all'elenco"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Proprietà | Obbligatoria | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **ID elenco** | Sì | `test-members-ML` | Il nome della lista di distribuzione MailChimp. Questo esempio usa "test-members-ML". |
   | **Status** | Sì | `subscribed` | Selezionare lo stato della sottoscrizione per il nuovo membro. Questo esempio usa "subscribed". <p>Per altre informazioni, vedere [Manage subscribers with the MailChimp API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/) (Gestire le sottoscrizioni con l'API MailChimp). |
   | **Indirizzo di posta elettronica** | Sì | <*indirizzo-posta-elettronica-nuovo-membro*> | Nell'elenco di contenuto dinamico selezionare l'opzione **Da** in **All'arrivo di un nuovo messaggio di posta elettronica**, che passa l'indirizzo di posta elettronica per il nuovo membro. |
   ||||

   Per altre informazioni sulle proprietà di questa azione, vedere le [informazioni di riferimento sul connettore MailChimp](https://docs.microsoft.com/connectors/mailchimp/).

1. Salvare l'app per la logica.

Aggiungere quindi una condizione in modo che sia possibile controllare se il nuovo membro è stato o meno aggiunto alla lista di distribuzione. In questo modo, l'app per la logica comunica se l'operazione è o non è riuscita.

## <a name="check-for-success-or-failure"></a>Controllare se l'operazione riesce

1. Nel ramo **È true** nell'azione **Aggiungi membro all'elenco** selezionare **Aggiungi un'azione**.

1. In **Scegliere un'azione** selezionare **Predefinita**. Nella casella di ricerca immettere `condition` come filtro. Nell'elenco di azioni selezionare **Condizione**.

1. Rinominare la condizione con questa descrizione: `If add member succeeded`

1. Creare una condizione che controlli se il membro approvato sia stato o meno aggiunto alla lista di distribuzione:

   1. A sinistra della condizione fare clic all'interno della casella **Scegliere un valore**. Nell'elenco di contenuto dinamico selezionare la proprietà **Stato** in **Aggiungi membro all'elenco**.

      La condizione dovrebbe avere ora un aspetto simile all'esempio seguente:

      ![In "Aggiungere membro all'elenco" selezionare "Status"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Nella casella di confronto centrale selezionare l'operatore **è uguale a**.

   1. Nella casella **Scegliere un valore** sul lato destro della condizione immettere questo testo: `subscribed`

      Al termine, la condizione avrà un aspetto simile a questo esempio:

      ![Condizione completata per l'esempio "sottoscritto"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Configurare quindi i messaggi di posta elettronica da inviare quando un membro approvato viene o meno aggiunto alla lista di distribuzione.

## <a name="send-email-if-member-added"></a>Inviare un messaggio di posta elettronica se il membro viene aggiunto

1. Nel ramo **È true** per la condizione **If add member succeeded** (Se l'aggiunta del membro è riuscita) selezionare **Aggiungi un'azione**.

   ![Nel ramo "È true" selezionare "Aggiungi un'azione"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Nella casella di ricerca di **Scegliere un'azione** immettere `outlook send email` come filtro e selezionare l'azione **Invia un messaggio di posta elettronica**.

   ![Aggiungere l'azione "Invia un messaggio di posta elettronica"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Rinominare l'azione con questa descrizione: `Send email on success`

1. Specificare le informazioni per questa azione come mostrato e descritto di seguito:

   ![Specificare le informazioni per il messaggio di posta elettronica di conferma](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Proprietà | Obbligatoria | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **To** | Sì | <*indirizzo-posta-elettronica*> | Indirizzo di posta elettronica a cui inviare il messaggio di posta elettronica di conferma. AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica. |
   | **Oggetto** | Sì | <*oggetto-messaggio-conferma*> | Oggetto per il messaggio di posta elettronica di conferma. Per questa esercitazione, immettere questo testo: <p>`Success! Member added to "test-members-ML": ` <p>Nell'elenco di contenuto dinamico selezionare la proprietà **Indirizzo di posta elettronica** in **Aggiungi membro all'elenco**. |
   | **Corpo** | Sì | <*corpo-per-messaggio-conferma*> | Contenuto del corpo per il messaggio di posta elettronica di conferma. Per questa esercitazione, immettere questo testo: <p>`New member has joined "test-members-ML":` <p>Nell'elenco di contenuto dinamico selezionare la proprietà **Indirizzo di posta elettronica**. <p>Nella riga successiva immettere questo testo: `Member opt-in status: ` <p> Nell'elenco di contenuto dinamico selezionare la proprietà **Stato** in **Aggiungi membro all'elenco**. |
   |||||

1. Salvare l'app per la logica.

## <a name="send-email-if-member-not-added"></a>Inviare un messaggio di posta elettronica se il membro non viene aggiunto

1. Nel ramo **È false** per la condizione **If add member succeeded** (Se l'aggiunta del membro è riuscita) selezionare **Aggiungi un'azione**.

   ![Nel ramo "È false" selezionare "Aggiungi un'azione"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Nella casella di ricerca di **Scegliere un'azione** immettere `outlook send email` come filtro e selezionare l'azione **Invia un messaggio di posta elettronica**.

   ![Aggiungere un'azione per "Invia un messaggio di posta elettronica"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Rinominare l'azione con questa descrizione: `Send email on failure`

1. Specificare le informazioni per questa azione come mostrato e descritto di seguito:

   ![Specificare le informazioni per il messaggio di posta elettronica di mancata conferma](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Proprietà | Obbligatoria | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **To** | Sì | <*indirizzo-posta-elettronica*> | Indirizzo di posta elettronica a cui inviare il messaggio di posta elettronica di mancata conferma. AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica. |
   | **Oggetto** | Sì | <*oggetto-messaggio-mancata-conferma*> | Oggetto per il messaggio di posta elettronica di mancata conferma. Per questa esercitazione, immettere questo testo: <p>`Failed, member not added to "test-members-ML": ` <p>Nell'elenco di contenuto dinamico selezionare la proprietà **Indirizzo di posta elettronica** in **Aggiungi membro all'elenco**. |
   | **Corpo** | Sì | <*corpo-messaggio-mancata-conferma*> | Contenuto del corpo per il messaggio di posta elettronica di mancata conferma. Per questa esercitazione, immettere questo testo: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Salvare l'app per la logica. 

Testare quindi l'app per la logica, che sarà simile a questo esempio:

![Esempio di flusso di lavoro dell'app per la logica completato](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Eseguire l'app per la logica

1. Inviare a se stessi una richiesta tramite posta elettronica per l'aggiunta alla lista di distribuzione. Attendere che la richiesta venga visualizzata nella cartella Posta in arrivo.

1. Per avviare manualmente l'app per la logica, sulla barra degli strumenti della finestra di progettazione selezionare **Esegui**. 

   Se il messaggio di posta elettronica ha un oggetto che corrisponde al filtro oggetto del trigger, si riceverà dall'app per la logica il messaggio di posta elettronica per l'approvazione della richiesta di sottoscrizione.

1. Nel messaggio di posta elettronica di approvazione selezionare **Approva**.

1. Se l'indirizzo di posta elettronica del sottoscrittore non è incluso nella lista di distribuzione, l'app per la logica aggiunge l'indirizzo di questa persona e invia un messaggio di posta elettronica come quello in questo esempio:

   ![Indirizzo di posta elettronica di esempio: sottoscrizione riuscita](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Se l'app per la logica non può aggiungere il sottoscrittore, si riceverà un messaggio di posta elettronica simile a quello in questo esempio:

   ![Indirizzo di posta elettronica di esempio: sottoscrizione non riuscita](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Se non si riceve alcun messaggio di posta elettronica, controllare la cartella della posta indesiderata. Il filtro della posta indesiderata potrebbe reindirizzare questi tipi di messaggi di posta elettronica. In caso contrario, se non si è certi che l'app per la logica sia stata eseguita correttamente, vedere [Risolvere i problemi dell'app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

È stata creata ed eseguita un'app per la logica che integra informazioni tra Azure, servizi Microsoft e altre app SaaS.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando l'app per la logica di esempio non è più necessaria, eliminare il gruppo di risorse che contiene l'app e le risorse correlate. 

1. Nel menu principale di Azure passare a **Gruppi di risorse** e selezionare il gruppo di risorse per l'app per la logica.

1. Nel menu Gruppo di risorse selezionare **Panoramica** > **Elimina gruppo di risorse**. 

   !["Panoramica" > "Elimina gruppo di risorse"](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Immettere il nome del gruppo di risorse per conferma e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'app per la logica per la gestione delle approvazioni per le richieste di sottoscrizione di una lista di distribuzione. Si apprenderà ora come creare un'app per la logica per l'elaborazione e l'archiviazione degli allegati di posta elettronica integrando servizi di Azure, come Archiviazione di Azure e Funzioni di Azure.

> [!div class="nextstepaction"]
> [Elaborare gli allegati di posta elettronica](../logic-apps/tutorial-process-email-attachments-workflow.md)
