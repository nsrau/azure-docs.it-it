---
title: Monitorare le modifiche delle macchine virtuali - Griglia di eventi e App per la logica di Azure
description: Controllare le modifiche delle macchine virtuali (VM) tramite Griglia di eventi e App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: 5d852378812d8e69480ceb2c5dcea95f1d5f3770
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488620"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Esercitazione: Monitorare le modifiche delle macchine virtuali tramite Griglia di eventi e App per la logica di Azure

Per monitorare e rispondere a specifici eventi che si verificano nelle risorse di Azure o di terze parti, è possibile automatizzare ed eseguire attività come flusso di lavoro creando un'[app per la logica](../logic-apps/logic-apps-overview.md) con codice minimo. Queste risorse possono pubblicare eventi in una [Griglia di eventi di Azure](../event-grid/overview.md), che a sua volta esegue il push degli eventi ai sottoscrittori che hanno code, webhook o [hub eventi](../event-hubs/event-hubs-what-is-event-hubs.md) come endpoint. Le app per la logica dei sottoscrittori possono quindi attendere gli eventi da Griglia di eventi prima di eseguire i flussi di lavoro automatizzati per l'esecuzione di determinate attività.

Di seguito sono elencati alcuni esempi di eventi che le risorse di pubblicazione possono inviare ai sottoscrittori tramite il servizio Griglia di eventi di Azure:

* Creare, leggere, aggiornare o eliminare una risorsa. È possibile, ad esempio, monitorare le modifiche che possono generare addebiti sulla sottoscrizione di Azure e influire quindi sui costi.

* Aggiungere o rimuovere un utente da una sottoscrizione di Azure.

* L'app esegue una determinata azione.

* In una coda viene visualizzato un nuovo messaggio.

Questa esercitazione crea un'app per la logica che consente di monitorare le modifiche a una macchina virtuale e inviare messaggi di posta elettronica su tali modifiche. Se si crea un'app per la logica con una sottoscrizione eventi per una risorsa di Azure, gli eventi vengono inviati dalla risorsa all'app per la logica tramite Griglia di eventi. L'esercitazione illustra la creazione di questa app per la logica:

![Panoramica - Monitorare una macchina virtuale con Griglia di eventi e un'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'app per la logica che consenta di monitorare gli eventi da Griglia di eventi.
> * Aggiungere una condizione che controlli in modo specifico le modifiche apportate a una macchina virtuale.
> * Inviare un messaggio di posta elettronica quando viene apportata una modifica alla macchina virtuale.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un account di posta elettronica di un provider di posta elettronica supportato da App per la logica per l'invio di notifiche, come Office 365 Outlook, Outlook.com o Gmail. Per altri provider, [vedere qui l'elenco dei connettori](/connectors/).

  In questa esercitazione viene usato un account di Office 365 Outlook. Se si usa un account di posta elettronica diverso, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa.

* Una [macchina virtuale](https://azure.microsoft.com/services/virtual-machines) isolata nel rispettivo gruppo di risorse di Azure. Se questa operazione non è già stata eseguita, creare una macchina virtuale mediante l'[esercitazione Creare una macchina virtuale](../virtual-machines/windows/quick-create-portal.md). Per consentire alla macchina virtuale di pubblicare eventi, [non è necessario effettuare nessun'altra operazione](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Creare un'app per la logica vuota

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Dal menu principale di Azure selezionare **Crea una risorsa** > **Integrazione** > **App per la logica**.

   ![Creare l'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. In **App per la logica** fornire informazioni sulla risorsa app per la logica. Al termine, selezionare **Crea**.

   ![Specificare i dettagli dell'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Proprietà | Obbligatoria | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Nome** | Sì | <*logic-app-name*> | Specificare un nome univoco per l'app per la logica. |
   | **Sottoscrizione** | Sì | <*nome sottoscrizione di Azure*> | Selezionare la stessa sottoscrizione di Azure per tutti i servizi illustrati in questa esercitazione. |
   | **Gruppo di risorse** | Sì | <*Azure-resource-group*> | Il nome del gruppo di risorse di Azure per l'app per la logica, che è possibile selezionare per tutti i servizi di questa esercitazione. |
   | **Posizione** | Sì | <*Area di Azure*> | In questa esercitazione selezionare la stessa area per tutti i servizi. |
   |||

1. Dopo che Azure ha distribuito l'app per la logica, Progettazione app per la logica visualizza una pagina con un video introduttivo e i trigger più usati. Scorrere dopo il video e i trigger.

1. In **Modelli** selezionare **App per la logica vuota**.

   ![Selezionare il modello App per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Progettazione di App per la logica mostra ora i [*trigger*](../logic-apps/logic-apps-overview.md#logic-app-concepts) che è possibile usare per avviare l'app per la logica. Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, App per la logica di Azure crea un'istanza dell'app per il flusso di lavoro che esegue l'app per la logica.

## <a name="add-an-event-grid-trigger"></a>Aggiungere un trigger Griglia di eventi

Aggiungere ora un trigger di Griglia di eventi, da usare per monitorare il gruppo di risorse per la macchina virtuale.

1. Nella casella di ricerca della finestra di progettazione immettere `event grid` come filtro. Nell'elenco di trigger selezionare **Quando si verifica un evento della risorsa**.

   ![Selezionare questo trigger: "Per un evento della risorsa"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Quando richiesto, accedere a Griglia di eventi di Azure con le credenziali dell'account Azure. Nell'elenco **Tenant**, che mostra il tenant di Azure Active Directory associato alla sottoscrizione di Azure, verificare che venga visualizzato il tenant corretto, ad esempio:

   ![Accedere con le credenziali di Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Se è stato effettuato l'accesso con un account Microsoft personale, ad esempio @outlook.com o @hotmail.com, è possibile che il trigger di Griglia di eventi di Azure non venga visualizzato correttamente. In alternativa, selezionare [Connetti usando l'entità servizio](../active-directory/develop/howto-create-service-principal-portal.md) o eseguire l'autenticazione come membro dell'istanza di Azure Active Directory associata alla sottoscrizione di Azure, ad esempio *nome-utente*@emailoutlook.onmicrosoft.com.

1. A questo punto eseguire la sottoscrizione dell'app per la logica agli eventi dell'autore. Specificare i dettagli per la sottoscrizione degli eventi, come indicato nella tabella seguente:

   ![Specificare i dettagli della sottoscrizione eventi](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Proprietà | Obbligatoria | Value | DESCRIZIONE |
   | -------- | -------- | ----- | ----------- |
   | **Sottoscrizione** | Sì | <*event-publisher-Azure-subscription-name*> | Selezionare il nome della sottoscrizione di Azure associata all'*autore di eventi*. Per questa esercitazione selezionare il nome della sottoscrizione di Azure per la macchina virtuale. |
   | **Tipo di risorsa** | Sì | <*event-publisher-Azure-resource-type*> | Selezionare il tipo di risorsa di Azure per l'autore di eventi. Per altre informazioni sui tipi di risorse di Azure, vedere [Provider e tipi di risorse di Azure](../azure-resource-manager/resource-manager-supported-services.md). Per questa esercitazione, selezionare il valore `Microsoft.Resources.ResourceGroups` per monitorare i gruppi di risorse di Azure. |
   | **Nome risorsa** |  Sì | <*event-publisher-Azure-resource-name*> | Selezionare il nome della risorsa di Azure per l'autore di eventi. Questo elenco varia in base al tipo di risorsa selezionato. Per questa esercitazione, selezionare il nome per il gruppo di risorse di Azure che include la macchina virtuale. |
   | **Elemento tipo di evento** |  No | <*event-types*> | Selezionare uno o più tipi di evento specifici per filtrare e inviare alla griglia di eventi. Ad esempio, è facoltativamente possibile aggiungere questi tipi di evento per rilevare quando le risorse vengono modificate o eliminate: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Per altre informazioni, vedere gli argomenti seguenti: <p><p>- [Schema di eventi di Griglia di eventi di Azure per i gruppi di risorse](../event-grid/event-schema-resource-groups.md) <br>- [Comprendere il filtro eventi](../event-grid/event-filtering.md) <br>- [Filtrare gli eventi per Griglia di eventi](../event-grid/how-to-filter-events.md) |
   | Per aggiungere proprietà facoltative, selezionare **Aggiungi nuovo parametro** e quindi le proprietà desiderate. | No | {vedere descrizioni} | * **Filtro per prefisso**: Per questa esercitazione, lasciare vuota questa proprietà. Il comportamento predefinito corrisponde a tutti i valori. È possibile tuttavia specificare una stringa di prefisso come filtro, ad esempio un percorso o un parametro relativo a una determinata risorsa. <p>* **Filtro per suffisso**: Per questa esercitazione, lasciare vuota questa proprietà. Il comportamento predefinito corrisponde a tutti i valori. È possibile tuttavia specificare una stringa di suffisso come filtro, ad esempio un'estensione se si vuole usare solo specifici tipi di file. <p>* **Nome della sottoscrizione**: per questa esercitazione, è possibile specificare un nome univoco per la sottoscrizione di eventi. |
   |||

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**. Per comprimere e nascondere i dettagli di un'azione nell'app per la logica, selezionare la relativa barra del titolo.

   ![Salvare l'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Quando si salva l'app per la logica con un trigger di Griglia di eventi, Azure crea automaticamente una sottoscrizione eventi per l'app per la logica alla risorsa specificata. Quando la risorsa pubblica un evento in Griglia di eventi, quindi, il servizio esegue automaticamente il push dell'evento all'app per la logica. L'evento attiva l'app per la logica, che crea ed esegue un'istanza di un flusso di lavoro che verrà definito nei passaggi successivi.

L'app per la logica è ora disponibile e in ascolto degli eventi generati da Griglia di eventi, ma non esegue alcuna operazione fino a quando non verranno aggiunte azioni al flusso di lavoro.

## <a name="add-a-condition"></a>Aggiungere una condizione

Se si vuole che l'app per la logica venga eseguita solo quando si verifica uno specifico evento o un'operazione, aggiungere una condizione che controlli l'operazione `Microsoft.Compute/virtualMachines/write`. Se questa condizione viene soddisfatta, l'app per la logica invia un messaggio di posta elettronica con i dettagli della macchina virtuale aggiornata.

1. In Progettazione app per la logica, nel trigger di Griglia di eventi, selezionare **Nuovo passaggio**.

   ![Selezionare "Nuovo passaggio"](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Nella casella di ricerca di **Scegliere un'azione** immettere `condition` come filtro. Nell'elenco di azioni selezionare **Condizione**.

   ![Aggiungere una condizione](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Progettazione app per la logica aggiunge una condizione vuota al flusso di lavoro, inclusi i percorsi di azioni da seguire se la condizione viene soddisfatta o meno.

   ![Viene visualizzata una condizione vuota](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Rinominare il riquadro della condizione in `If a virtual machine in your resource group has changed`. Sulla barra del titolo della condizione fare clic sui puntini di sospensione ( **...** ) e scegliere **Rinomina**.

   ![Rinominare la condizione](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Creare una condizione che controlli l'evento `body` per un oggetto `data` in cui la proprietà `operationName` è uguale all'operazione `Microsoft.Compute/virtualMachines/write`. Per altre informazioni, vedere [Schema di eventi di Griglia di eventi](../event-grid/event-schema.md).

   1. Nella prima riga sotto **E** fare clic all'interno della casella di sinistra. Nell'elenco di contenuto dinamico che viene visualizzato selezionare **Espressione**.

      ![Selezionare "Espressione" per aprire l'editor di espressioni](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. Nell'editor di espressioni immettere l'espressione, che restituisce il nome dell'operazione del trigger, quindi selezionare **OK**:

      `triggerBody()?['data']['operationName']`

      Ad esempio:

      ![Immettere l'espressione per estrarre il nome dell'operazione](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. Nella casella centrale mantenere l'operatore **è uguale a**.

   1. Nella casella destra immettere questo valore, che corrisponde all'operazione specifica da monitorare:

      `Microsoft.Compute/virtualMachines/write`

   La condizione completata dovrebbe avere ora un aspetto simile all'esempio seguente:

   ![Condizione completata che confronta l'operazione](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Se si passa dalla visualizzazione progettazione per creare il codice di visualizzazione e nuovamente alla visualizzazione progettazione, l'espressione specificata nella condizione si risolve nel token **data.operationName**:

   ![Token risolti nella condizione](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Salvare l'app per la logica.

## <a name="send-email-notifications"></a>Inviare notifiche di posta elettronica

Aggiungere ora un'[*azione*](../logic-apps/logic-apps-overview.md#logic-app-concepts) che consenta di ricevere un messaggio di posta elettronica quando viene soddisfatta la condizione specificata.

1. Nella casella **È true** della condizione selezionare **Aggiungi un'azione**.

   ![Aggiungere un'azione se la condizione viene soddisfatta](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Nella casella di ricerca di **Scegliere un'azione** immettere `send an email` come filtro. Selezionare il connettore corrispondente al provider di posta elettronica in uso. Selezionare quindi l'azione di invio di un messaggio di posta elettronica relativa al connettore selezionato. Ad esempio:

   * Per un account aziendale o dell'istituto di istruzione di Azure, selezionare il connettore Office 365 Outlook.

   * Per gli account Microsoft personali, selezionare il connettore Outlook.com.

   * Per gli account Gmail, selezionare il connettore Gmail.

   Questa esercitazione continua a usare il connettore Office 365 Outlook. Se si usa un provider diverso, la procedura rimane invariata ma è possibile che l'interfaccia utente abbia un aspetto leggermente diverso.

   ![Selezionare l'azione di invio di un messaggio di posta elettronica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Se non si ha ancora una connessione per il provider di posta elettronica, accedere al proprio account di posta elettronica quando viene richiesta l'autenticazione.

1. Rinominare l'azione di invio del messaggio di posta elettronica con questo titolo: `Send email when virtual machine updated`

1. Specificare informazioni sul messaggio di posta elettronica come illustrato nella tabella seguente:

   ![Specificare informazioni sull'azione di invio del messaggio di posta elettronica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Per selezionare l'output dei passaggi precedenti nel flusso di lavoro, fare clic in una casella di modifica in modo che venga visualizzato l'elenco di contenuto dinamico oppure selezionare **Aggiungi contenuto dinamico**. Per altri risultati, selezionare **Vedi altro** per ogni sezione nell'elenco. Per chiudere l'elenco di contenuto dinamico selezionare nuovamente **Aggiungi contenuto dinamico**.

   | Proprietà | Obbligatoria | Value | DESCRIZIONE |
   | -------- | -------- | ----- | ----------- |
   | **To** | Sì | <*recipient\@domain*> | Immettere l'indirizzo di posta elettronica del destinatario. AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica. |
   | **Oggetto** | Sì | `Resource updated:` **Oggetto** | Immettere il contenuto per l'oggetto del messaggio di posta elettronica. Per questa esercitazione, immettere il testo specificato e selezionare il campo **Oggetto** dell'evento. In questo caso, l'oggetto del messaggio di posta elettronica include il nome per la risorsa aggiornata (macchina virtuale). |
   | **Corpo** | Sì | `Resource:` **Argomento** <p>`Event type:` **Tipo di evento**<p>`Event ID:` **ID**<p>`Time:` **Ora dell'evento** | Immettere il contenuto per il corpo del messaggio di posta elettronica. Per questa esercitazione immettere il testo specificato e selezionare **Argomento**, Tipo di evento**Argomento**, **ID** e **Ora evento** per l'evento in modo che nel messaggio di posta elettronica siano riportata la risorsa che ha attivato l'evento, il tipo di evento, il timestamp dell'evento e l'ID dell'evento relativo all'aggiornamento. Per questa esercitazione la risorsa è il gruppo di risorse di Azure selezionato nel trigger. <p>Per aggiungere righe vuote nel contenuto, premere MAIUSC + INVIO. |
   ||||

   > [!NOTE]
   > Se si seleziona un campo che rappresenta una matrice, la finestra di progettazione aggiunge automaticamente un ciclo **For each** all'azione che fa riferimento alla matrice. In questo modo, l'app per la logica esegue l'azione su ogni elemento della matrice.

   L'azione di invio di un messaggio di posta elettronica avrà ora un aspetto simile all'esempio seguente:

   ![Selezionare gli output da includere nel messaggio di posta elettronica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Al termine di questa operazione, l'app per la logica dovrebbe avere un aspetto simile all'esempio seguente:

   ![App per la logica completata](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Salvare l'app per la logica. Per comprimere e nascondere i dettagli di un'azione nell'app per la logica, selezionare la relativa barra del titolo.

   L'app per la logica è ora disponibile, ma prima di eseguire un'operazione attende che vengano apportate modifiche alla macchina virtuale. Se a questo punto si vuole testare l'app per la logica, passare alla sezione successiva.

## <a name="test-your-logic-app-workflow"></a>Testare il flusso di lavoro per l'app per la logica

1. Per verificare che l'app per la logica riceva gli eventi specificati, aggiornare la macchina virtuale.

   È possibile, ad esempio, ridimensionare la macchina virtuale nel portale di Azure o [ridimensionare la macchina virtuale con Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Dopo alcuni istanti, si dovrebbe ricevere un messaggio di posta elettronica. Ad esempio:

   ![Messaggio di posta elettronica sull'aggiornamento della macchina virtuale](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Per rivedere le esecuzioni e la cronologia dei trigger dell'app per la logica, selezionare **Panoramica** dal menu dell'app per la logica. Per visualizzare altri dettagli su un'esecuzione, selezionare la riga per tale esecuzione.

   ![Cronologia di esecuzioni di app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Per visualizzare gli input e gli output per ogni passaggio, espandere il passaggio da revisionare. Queste informazioni consentono di eseguire la diagnostica e il debug di problemi nell'app per la logica.

   ![Dettagli della cronologia di esecuzioni dell'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Congratulazioni, è stata creata ed eseguita un'app per la logica in grado di monitore gli eventi di risorse tramite Griglia di eventi e di inviare un messaggio di posta elettronica quando si verificano tali eventi. Si è inoltre appreso come creare con facilità flussi di lavoro per l'automazione dei processi e come integrare sistemi e servizi cloud.

È possibile monitorare altre modifiche alla configurazione con le griglie di evento e le app per la logica, ad esempio:

* L'acquisizione da parte di una macchina virtuale dei diritti di controllo degli accessi in base al ruolo.
* L'implementazione di modifiche a un gruppo di sicurezza di rete o a un'interfaccia di rete.
* L'aggiunta o la rimozione di dischi in una macchina virtuale.
* L'assegnazione di un indirizzo IP pubblico all'interfaccia di rete di una macchina virtuale.

## <a name="clean-up-resources"></a>Pulire le risorse

In questa esercitazione vengono usate risorse ed eseguite azioni che generano addebiti sulla sottoscrizione di Azure. Al termine dell'esercitazione e dei test, accertarsi di disabilitare o eliminare le risorse per evitare di incorrere in addebiti.

* Per interrompere l'esecuzione dell'app per la logica senza eliminare il lavoro, disabilitare l'app. Selezionare **Panoramica** dal menu dell'app per la logica. Sulla barra degli strumenti selezionare **Disabilita**.

  ![Disabilitare l'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Se il menu dell'app per la logica non è visualizzato, provare a tornare al dashboard di Azure e riaprire l'app per la logica.

* Per eliminare in modo definitivo l'app per la logica, selezionare **Panoramica** dal menu delle app per la logica. Sulla barra degli strumenti selezionare **Elimina**. Confermare di voler eliminare l'app per la logica e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

* [Creare e instradare eventi personalizzati con Griglia di eventi](../event-grid/custom-event-quickstart.md)
