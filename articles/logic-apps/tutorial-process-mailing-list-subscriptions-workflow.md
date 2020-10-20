---
title: Creare flussi di lavoro automatizzati basati su approvazione
description: Esercitazione - Creare un flusso di lavoro automatizzato basato su approvazione che elabora le sottoscrizioni di liste di distribuzione tramite App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/07/2020
ms.openlocfilehash: 102b1946021aff7f8ab5491ed70fbc6cf772e3a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842261"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Esercitazione: Creare flussi di lavoro automatizzati basati su approvazione tramite App per la logica di Azure

Questa esercitazione illustra come creare un'[app per la logica](../logic-apps/logic-apps-overview.md) di esempio che automatizza un flusso di lavoro basato su approvazione. In particolare, questa app per la logica di esempio elabora le richieste di sottoscrizione di una lista di distribuzione gestita dal servizio [MailChimp](https://mailchimp.com/). L'app per la logica include vari passaggi, ovvero il monitoraggio iniziale di un account di posta elettronica per individuare richieste, l'invio delle richieste di approvazione, la verifica se richiesta viene o meno approvata, l'aggiunta dei membri approvati alla lista di distribuzione e la conferma dell'aggiunta o meno dei nuovi membri.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
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

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un account MailChimp in cui in precedenza è stata creata una lista denominata "test-members-ML", in cui l'app per la logica può aggiungere gli indirizzi di posta elettronica dei membri approvati. Se non si ha un account, [registrarsi per ottenerne uno gratuito](https://login.mailchimp.com/signup/) e vedere [come creare una lista MailChimp](https://us17.admin.mailchimp.com/lists/#).

* Un account di posta elettronica di un provider supportato da App per la logica, ad esempio un account Office 365 Outlook, Outlook.com o Gmail. Per altri provider, [vedere qui l'elenco dei connettori](/connectors/). Questa guida di avvio rapido usa Office 365 Outlook con un account aziendale o dell'istituto di istruzione. Se si usa un account di posta elettronica diverso, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa.

* Una account di posta elettronica in Office 365 Outlook o Outlook.com, che supporta i flussi di lavoro basati su approvazione. In questa esercitazione viene usato Office 365 Outlook. Se si usa un account di posta elettronica diverso, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa.

## <a name="create-your-logic-app"></a>Creare l'app per la logica

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure. Nella home page di Azure selezionare **Crea una risorsa**.

1. Nel menu di Azure Marketplace selezionare **Integrazione** > **App per la logica**.

   ![Screenshot che mostra il menu di Azure Marketplace con le opzioni "Integrazione" e "App per la logica" selezionate.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. Nel riquadro **App per la logica** specificare le informazioni riportate qui sull'app per la logica da creare.

   ![Screenshot che mostra il riquadro di creazione dell'app per la logica e le informazioni da fornire per la nuova app per la logica.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **Sottoscrizione** | <*nome sottoscrizione di Azure*> | Il nome della sottoscrizione di Azure. In questo esempio viene utilizzato `Pay-As-You-Go`. |
   | **Gruppo di risorse** | LA-MailingList-RG | Il nome del [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) usato per organizzare le risorse correlate. Questo esempio crea un nuovo gruppo di risorse denominato `LA-MailingList-RG`. |
   | **Nome** | LA-MailingList | Il nome dell'app per la logica, che può contenere solo lettere, numeri, trattini (`-`), caratteri di sottolineatura (`_`), parentesi (`(`, `)`) e punti (`.`). In questo esempio viene utilizzato `LA-MailingList`. |
   | **Posizione** | Stati Uniti occidentali | L'area in cui archiviare le informazioni sull'app per la logica. In questo esempio viene utilizzato `West US`. |
   | **Log Analytics** | Disattivato | Lasciare l'impostazione **No** per la registrazione diagnostica. |
   ||||

1. Al termine, selezionare **Rivedi e crea**. Una volta convalidate le informazioni sull'app per la logica in Azure, selezionare **Crea**.

1. Dopo la distribuzione dell'app in Azure, selezionare **Vai alla risorsa**.

   Azure apre il riquadro di selezione del modello App per la logica, che visualizza un video introduttivo, i trigger di uso comune e i criteri del modello di app per la logica.

1. Scorrere verso il basso dopo le sezioni del video e dei trigger comuni fino alla sezione **Modelli** e selezionare **App per la logica vuota**.

   ![Screenshot che mostra il riquadro di selezione del modello App per la logica con l'opzione "App per la logica vuota" selezionata.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Aggiungere quindi un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) di Outlook che resta in ascolto di messaggi di posta elettronica in arrivo con richieste di sottoscrizione. Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un evento specifico o quando nuovi dati soddisfano una condizione specifica. Per altre informazioni, vedere [Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md) (Creare la prima app per la logica).

## <a name="add-trigger-to-monitor-emails"></a>Aggiungere un trigger per il monitoraggio dei messaggi di posta elettronica

1. Nella casella di ricerca di Progettazione app per la logica immettere `when email arrives` e selezionare il trigger **All'arrivo di un nuovo messaggio di posta elettronica**.

   * Per gli account aziendali o dell'istituto di istruzione di Azure, selezionare **Office 365 Outlook**.
   * Per gli account Microsoft personali, selezionare **Outlook.com**.

   Questo esempio continua con la selezione di Office 365 Outlook.

   ![Screenshot che mostra la casella di ricerca di Progettazione app per la logica, che contiene il termine di ricerca "all'arrivo di posta elettronica" e il trigger "All'arrivo di un nuovo messaggio di posta elettronica" selezionato.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Se non si ha già una connessione, accedere e autenticare l'accesso all'account di posta elettronica quando richiesto.

   App per la logica di Azure crea una connessione all'account di posta elettronica.

1. Nel trigger specificare i criteri per il controllo dei nuovi messaggi di posta elettronica.

   1. Specificare la cartella per il controllo e lasciare le altre proprietà impostate sui valori predefiniti.

      ![Screenshot che mostra la finestra di progettazione con l'azione "All'arrivo di un nuovo messaggio di posta elettronica" e la cartella impostata su "Posta in arrivo".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. Aggiungere la proprietà **Filtro oggetto** del trigger in modo da filtrare i messaggi in base alla riga dell'oggetto. Aprire l'elenco **Aggiungi nuovo parametro** selezionare **Filtro oggetto**.

      ![Screenshot che mostra l'elenco "Aggiungi nuovo parametro" aperto con l'opzione "Filtro oggetto" selezionata.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Per altre informazioni sulle proprietà di questo trigger, vedere le [informazioni di riferimento sul connettore Office 365 Outlook](/connectors/office365/) o le [informazioni di riferimento sul connettore Outlook.com](/connectors/outlook/).

   1. Quando la proprietà viene visualizzata nel trigger, immettere questo testo: `subscribe-test-members-ML`

      ![Screenshot che mostra la proprietà "Filtro oggetto" con il testo "subscribe-test-members-ML" immesso.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Per nascondere i dettagli del trigger per il momento, fare clic all'interno della barra del titolo della forma per comprimerla.

   ![Screenshot che mostra la forma compressa del trigger.](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

L'app per la logica è ora attiva, ma non esegue alcuna operazione oltre a controllare la posta elettronica in arrivo. Aggiungere quindi un'azione per la risposta all'attivazione del trigger.

## <a name="send-approval-email"></a>Inviare messaggio di posta elettronica di approvazione

Ora che è stato creato il trigger, aggiungere un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) per l'invio di un messaggio di posta elettronica per approvare o rifiutare la richiesta.

1. In Progettazione app per la logica, sotto il trigger **All'arrivo di un nuovo messaggio di posta elettronica**, selezionare **Nuovo passaggio**.

1. Nella casella di ricerca di **Scegliere un'operazione** immettere `send approval` come filtro e selezionare l'azione **Invia messaggio di posta elettronica di approvazione**.

   ![Screenshot che mostra l'elenco "Scegliere un'operazione" filtrato in base alle azioni "approvazione" e l'azione "Invia messaggio di posta elettronica di approvazione" selezionata.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Immettere ora i valori per le proprietà specificate illustrati e descritti qui. Mantenere i valori predefiniti in tutti gli altri campi. Per altre informazioni su queste proprietà, vedere le [informazioni di riferimento sul connettore Office 365 Outlook](/connectors/office365/) o le [informazioni di riferimento sul connettore Outlook.com](/connectors/outlook/).

   ![Screenshot che mostra le proprietà di "Invia messaggio di posta elettronica di approvazione"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **To** | <*approval-email-address*> | Indirizzo di posta elettronica del responsabile approvazione. Per scopi di test, è possibile usare il proprio indirizzo. Questo esempio usa l'indirizzo di posta elettronica fittizio `sophiaowen@fabrikam.com`. |
   | **Oggetto** | `Approve member request for test-members-ML` | Oggetto descrittivo per il messaggio di posta elettronica |
   | **Opzioni utente** | `Approve, Reject` | Assicurarsi che questa proprietà specifichi le opzioni di risposta che il responsabile approvazione può selezionare, che per impostazione predefinita sono **Approva** o **Rifiuta**. |
   ||||

   > [!NOTE]
   > Quando si fa clic all'interno di alcune caselle di modifica, viene visualizzato l'elenco di contenuto dinamico, che per il momento è possibile ignorare. Questo elenco mostra gli output delle azioni precedenti disponibili per la selezione come input per le azioni successive nel flusso di lavoro.
 
1. Salvare l'app per la logica.

Aggiungere quindi una condizione per controllare la risposta selezionata del responsabile approvazione.

## <a name="check-approval-response"></a>Controllare la risposta di approvazione

1. Nell'azione **Invia messaggio di posta elettronica di approvazione** selezionare **Nuovo passaggio**.

1. In **Scegliere un'operazione** selezionare **Predefinita**. Nella casella di ricerca immettere `condition` e selezionare l'azione **Condizione**.

   ![Screenshot che mostra la casella di ricerca di "Scegliere un'operazione" con il termine "predefinita" selezionato, il termine di ricerca "condizione" e l'azione "Condizione" selezionata.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Sulla barra del titolo **Condizione** selezionare il pulsante con i **puntini di sospensione** ( **...** ) e scegliere **Rinomina**. Rinominare la condizione con questa descrizione: `If request approved`

   ![Screenshot che mostra il pulsante con i puntini di sospensione selezionato, l'elenco "Impostazioni" aperto e il comando "Rinomina" selezionato.](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Creare una condizione che controlli se il responsabile approvazione ha selezionato **Approva**.

   1. Sul lato sinistro della condizione fare clic all'interno della casella **Scegliere un valore**.

   1. Nell'elenco di contenuto dinamico che viene visualizzato, in **Invia messaggio di posta elettronica di approvazione**, selezionare la proprietà **SelectedOption**.

      ![Screenshot che mostra l'elenco di contenuto dinamico in cui nella sezione "Invia messaggio di posta elettronica di approvazione" è selezionato l'output "SelectedOption".](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Nella casella di confronto centrale selezionare l'operatore **è uguale a**.

   1. Sul lato destro della condizione, nella casella **Scegliere un valore**, immettere il testo `Approve`.

      Al termine, la condizione avrà un aspetto simile a questo esempio:

      ![Screenshot che mostra la condizione completata per l'esempio di richiesta approvata](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Salvare l'app per la logica.

Specificare quindi l'azione che deve eseguire l'app per la logica quando il revisore approva la richiesta. 

## <a name="add-member-to-mailchimp-list"></a>Aggiungere un membro all'elenco MailChimp

Impostare ora un'azione per aggiungere il membro approvato alla lista di distribuzione.

1. Nel ramo **True** della condizione selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca di **Scegliere un'operazione** selezionare **Tutte**. Nella casella di ricerca immettere `mailchimp` e selezionare l'azione **Aggiungi membro all'elenco**.

   ![Screenshot che mostra la casella "Scegliere un'operazione" con il termine di ricerca "mailchimp" e l'azione "Aggiungi membro all'elenco" selezionata.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Se non è ancora disponibile una connessione all'account MailChimp, viene chiesto di effettuare l'accesso.

1. Nell'azione **Aggiungi membro all'elenco** fornire le informazioni mostrate e descritte qui:

   ![Screenshot che mostra le informazioni per l'azione "Aggiungi membro all'elenco".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **ID elenco** | Sì | <*mailing-list-name*> | Selezionare il nome della lista di distribuzione MailChimp. In questo esempio viene utilizzato `test-members-ML`. |
   | **Indirizzo di posta elettronica** | Sì | <*indirizzo-posta-elettronica-nuovo-membro*> | Nella sezione **All'arrivo di un nuovo messaggio di posta elettronica** dell'elenco di contenuto dinamico visualizzato selezionare **Da**, che corrisponde all'output del trigger e specifica l'indirizzo di posta elettronica del nuovo membro. |
   | **Status** | Sì | <*member-subscription-status*> | Selezionare lo stato della sottoscrizione da impostare per il nuovo membro. Questo esempio seleziona `subscribed`. <p>Per altre informazioni, vedere [Manage subscribers with the MailChimp API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/) (Gestire le sottoscrizioni con l'API MailChimp). |
   |||||

   Per altre informazioni sulle proprietà dell'azione **Aggiungi membro all'elenco**, vedere le [informazioni di riferimento sul connettore MailChimp](/connectors/mailchimp/).

1. Salvare l'app per la logica.

Aggiungere quindi una condizione in modo che sia possibile controllare se il nuovo membro è stato o meno aggiunto alla lista di distribuzione. In questo modo, l'app per la logica può comunicare se l'operazione è riuscita o meno.

## <a name="check-for-success-or-failure"></a>Controllare se l'operazione riesce

1. Nel ramo **True** nell'azione **Aggiungi membro all'elenco** selezionare **Aggiungi un'azione**.

1. In **Scegliere un'operazione** selezionare **Predefinita**. Nella casella di ricerca immettere `condition` e selezionare l'azione **Condizione**.

1. Rinominare la condizione con questa descrizione: `If add member succeeded`

1. Creare una condizione che controlli se il membro approvato sia stato o meno aggiunto alla lista di distribuzione:

   1. Sul lato sinistro della condizione fare clic all'interno della casella **Scegliere un valore**. Nella sezione **Aggiungi membro all'elenco** dell'elenco di contenuto dinamico visualizzato selezionare la proprietà **Stato**.

      La condizione dovrebbe avere ora un aspetto simile all'esempio seguente:

      ![Screenshot che mostra la casella "Scegliere un valore" sul lato sinistro della condizione con il valore "Stato" immesso.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Nella casella di confronto centrale selezionare l'operatore **è uguale a**.

   1. Sul lato destro della condizione, nella casella **Scegliere un valore**, immettere il testo `subscribed`

      Al termine, la condizione avrà un aspetto simile a questo esempio:

      ![Screenshot che mostra la condizione finale per la verifica della riuscita o meno della sottoscrizione.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Configurare quindi i messaggi di posta elettronica da inviare quando un membro approvato viene o meno aggiunto alla lista di distribuzione.

## <a name="send-email-if-member-added"></a>Inviare un messaggio di posta elettronica se il membro viene aggiunto

1. Nel ramo **True** per la condizione **If add member succeeded** (Se l'aggiunta del membro è riuscita) selezionare **Aggiungi un'azione**.

   ![Screenshot che mostra il ramo "True" della condizione "If Add Member succeeded" con l'opzione "Aggiungi un'azione" selezionata.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Nella casella di ricerca di **Scegliere un'operazione** immettere `outlook send email` e selezionare l'azione **Invia un messaggio di posta elettronica**.

   ![Screenshot che mostra la casella di ricerca di "Scegliere un'operazione" con il testo "outlook send email" immesso e l'azione "Invia un messaggio di posta elettronica" selezionata per la notifica.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Rinominare l'azione con questa descrizione: `Send email on success`

1. Nell'azione **Send email on success** (Invia messaggio di posta elettronica in caso di esito positivo) fornire le informazioni mostrate e descritte qui:

   ![Screenshot che mostra l'azione "Send email on success" e le informazioni fornite per il messaggio di posta elettronica.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Corpo** | Sì | <*success-email-body*> | Contenuto del corpo per il messaggio di posta elettronica di conferma. Per questa esercitazione, seguire questa procedura: <p>1. Immettere il testo `New member has joined "test-members-ML":` con uno spazio finale. <p>2. Nell'elenco di contenuto dinamico che viene visualizzato selezionare la proprietà **Indirizzo di posta elettronica**. <p>**Nota**: se questa proprietà non viene visualizzata, accanto all'intestazione della sezione **Aggiungi membro all'elenco** selezionare **Mostra più**. <p>3. Nella riga successiva immettere il testo `Member opt-in status: ` con uno spazio finale. <p>4. Nell'elenco di contenuto dinamico selezionare la proprietà **Stato** in **Aggiungi membro all'elenco**. |
   | **Oggetto** | Sì | <*success-email-subject*> | Oggetto per il messaggio di posta elettronica di conferma. Per questa esercitazione, seguire questa procedura: <p>1. Immettere il testo `Success! Member added to "test-members-ML": ` con uno spazio finale. <p>2. Nell'elenco di contenuto dinamico selezionare la proprietà **Indirizzo di posta elettronica** in **Aggiungi membro all'elenco**. |
   | **To** | Sì | <*indirizzo-posta-elettronica*> | Indirizzo di posta elettronica a cui inviare il messaggio di posta elettronica di conferma. AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica. |
   |||||

1. Salvare l'app per la logica.

## <a name="send-email-if-member-not-added"></a>Inviare un messaggio di posta elettronica se il membro non viene aggiunto

1. Nel ramo **False** per la condizione **If add member succeeded** (Se l'aggiunta del membro è riuscita) selezionare **Aggiungi un'azione**.

   ![Screenshot che mostra il ramo "False" della condizione "If Add Member succeeded" con l'opzione "Aggiungi un'azione" selezionata.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Nella casella di ricerca di **Scegliere un'operazione** immettere `outlook send email` e selezionare l'azione **Invia un messaggio di posta elettronica**.

   ![Screenshot che mostra la casella di ricerca di "Scegliere un'operazione" con il testo "outlook send email" immesso e l'azione "Invia un messaggio di posta elettronica" selezionata.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Rinominare l'azione con questa descrizione: `Send email on failure`

1. Specificare le informazioni per questa azione come mostrato e descritto di seguito:

   ![Screenshot che mostra l'azione "Send email on failure" e le informazioni fornite per il messaggio di posta elettronica sull'esito negativo.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Corpo** | Sì | <*corpo-messaggio-mancata-conferma*> | Contenuto del corpo per il messaggio di posta elettronica di mancata conferma. Per questa esercitazione, immettere questo testo: <p>`Member might already exist. Check your MailChimp account.` |
   | **Oggetto** | Sì | <*oggetto-messaggio-mancata-conferma*> | Oggetto per il messaggio di posta elettronica di mancata conferma. Per questa esercitazione, seguire questa procedura: <p>1. Immettere il testo `Failed, member not added to "test-members-ML": ` con uno spazio finale. <p>2. Nell'elenco di contenuto dinamico selezionare la proprietà **Indirizzo di posta elettronica** in **Aggiungi membro all'elenco**. |
   | **To** | Sì | <*indirizzo-posta-elettronica*> | Indirizzo di posta elettronica a cui inviare il messaggio di posta elettronica di mancata conferma. AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica. |
   |||||

1. Salvare l'app per la logica. 

Testare quindi l'app per la logica, che sarà simile a questo esempio:

![Screenshot che mostra l'esempio completato del flusso di lavoro dell'app per la logica.](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Eseguire l'app per la logica

1. Inviare a se stessi una richiesta tramite posta elettronica per l'aggiunta alla lista di distribuzione. Attendere che la richiesta venga visualizzata nella cartella Posta in arrivo.

1. Per avviare manualmente l'app per la logica, selezionare **Esegui** sulla barra degli strumenti della finestra di progettazione. 

   Se il messaggio di posta elettronica ha un oggetto che corrisponde al filtro oggetto del trigger, si riceverà dall'app per la logica il messaggio di posta elettronica per l'approvazione della richiesta di sottoscrizione.

1. Nel messaggio di posta elettronica di approvazione che si riceve selezionare **Approva**.

1. Se l'indirizzo di posta elettronica del sottoscrittore non è incluso nella lista di distribuzione, l'app per la logica aggiunge l'indirizzo di questa persona e invia un messaggio di posta elettronica come quello in questo esempio:

   ![Screenshot che mostra il messaggio di posta elettronica di esempio per una sottoscrizione riuscita.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Se l'app per la logica non può aggiungere il sottoscrittore, si riceverà un messaggio di posta elettronica simile a quello in questo esempio:

   ![Screenshot che mostra il messaggio di posta elettronica di esempio per una sottoscrizione non riuscita.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > Se non si riceve alcun messaggio di posta elettronica, controllare la cartella della posta indesiderata. Il filtro della posta indesiderata potrebbe reindirizzare questi tipi di messaggi di posta elettronica. In caso contrario, se non si è certi che l'app per la logica sia stata eseguita correttamente, vedere [Risolvere i problemi dell'app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

È stata creata ed eseguita un'app per la logica che integra informazioni tra Azure, servizi Microsoft e altre app SaaS.

## <a name="clean-up-resources"></a>Pulire le risorse

L'app per la logica continuerà a essere eseguita finché non viene disabilitata o eliminata. Quando l'app per la logica di esempio non è più necessaria, eliminare il gruppo di risorse che contiene l'app e le risorse correlate.

1. Nella casella di ricerca del portale di Azure immettere il nome del gruppo di risorse creato. Nei risultati selezionare il gruppo di risorse in **Gruppi di risorse**.

   Questo esempio ha creato il gruppo di risorse denominato `LA-MailingList-RG`.

   ![Screenshot che mostra la casella di ricerca di Azure con la stringa "la-mailinglist-rg" immessa e il risultato **LA-MailingList-RG** selezionato.](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Se la home page di Azure mostra il gruppo di risorse in **Risorse recenti**, è possibile selezionarlo qui.

1. Nel menu gruppo di risorse verificare che sia selezionata l'opzione **Panoramica**. Sulla barra degli strumenti del riquadro **Panoramica** selezionare **Elimina gruppo di risorse**.

   ![Screenshot che mostra il riquadro "Panoramica" del gruppo di risorse e l'opzione "Elimina gruppo di risorse" selezionata sulla barra degli strumenti del riquadro.](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Nel riquadro di conferma visualizzato immettere il nome del gruppo di risorse, quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'app per la logica che gestisce le approvazioni per le richieste di sottoscrizione di una lista di distribuzione. Si apprenderà ora come creare un'app per la logica per l'elaborazione e l'archiviazione degli allegati di posta elettronica integrando servizi di Azure, come Archiviazione di Azure e Funzioni di Azure.

> [!div class="nextstepaction"]
> [Elaborare gli allegati di posta elettronica](../logic-apps/tutorial-process-email-attachments-workflow.md)
