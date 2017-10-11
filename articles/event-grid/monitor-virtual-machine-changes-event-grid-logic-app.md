---
title: Monitorare le modifiche a una macchina virtuale - Griglia di eventi di Azure e app per la logica | Microsoft Docs
description: Controllare le modifiche di configurazione apportate a macchine virtuali usando Griglia di eventi di Azure e app per la logica
keywords: app per la logica, griglie di eventi, macchina virtuale, VM
services: logic-apps
author: ecfan
manager: anneta
ms.assetid: 
ms.workload: logic-apps
ms.service: logic-apps
ms.topic: article
ms.date: 08/16/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 4d4c16860dbec10162797a13c8f9f57106abd17f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Monitorare le modifiche a una macchina virtuale con Griglia di eventi di Azure e app per la logica

È possibile avviare un [flusso di lavoro di app per la logica](../logic-apps/logic-apps-what-are-logic-apps.md) automatizzato quando si verificano determinati eventi in risorse di Azure o in risorse di terze parti. Le risorse possono pubblicare questi eventi nel servizio [Griglia di eventi di Azure](../event-grid/overview.md), che a sua volta esegue il push degli eventi ai sottoscrittori che hanno code, webhook o [hub eventi](../event-hubs/event-hubs-what-is-event-hubs.md) come endpoint. Le app per la logica dei sottoscrittori possono quindi attendere gli eventi da Griglia di eventi prima di eseguire i flussi di lavoro automatizzati per l'esecuzione di determinate attività, senza bisogno di scrivere codice.

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

* Un account di posta elettronica di [un provider di posta elettronica supportato da App per la logica di Azure](../connectors/apis-list.md), come Office 365 Outlook, Outlook.com o Gmail, per l'invio di notifiche. In questa esercitazione viene usato Office 365 Outlook.

* Una [macchina virtuale](https://azure.microsoft.com/services/virtual-machines). Se questa operazione non è già stata eseguita, creare una macchina virtuale mediante l'[esercitazione Creare una macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/). Per consentire alla macchina virtuale di pubblicare eventi, [non è necessario effettuare nessun'altra operazione](../event-grid/overview.md).

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Creare un'app per la logica che consenta di monitorare gli eventi da Griglia di eventi

Per prima cosa, creare un'app per la logica e aggiungere un trigger di Griglia di eventi che monitori il gruppo di risorse per la macchina virtuale. 

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Nell'angolo in alto a sinistra del menu principale di Azure scegliere **Nuovo** > **Integrazione aziendale** > **App per la logica**.

   ![Creare l'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. Creare l'app per la logica con le impostazioni specificate nella tabella seguente:

   ![Specificare i dettagli dell'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *{nome-app-per-logica}* | Specificare un nome univoco per l'app per la logica. | 
   | **Sottoscrizione** | *{sottoscrizione-Azure-personale}* | In questa esercitazione selezionare la stessa sottoscrizione di Azure per tutti i servizi. | 
   | **Gruppo di risorse** | *{gruppo-risorse-Azure-personale}* | In questa esercitazione selezionare lo stesso gruppo di risorse di Azure per tutti i servizi. | 
   | **Posizione** | *{area-Azure-personale}* | In questa esercitazione selezionare la stessa area per tutti i servizi. | 
   | | | 

4. Selezionare quindi **Aggiungi al dashboard** e scegliere **Crea**.

   È stata creata ora una risorsa di Azure per l'app per la logica. 
   Al termine della distribuzione dell'app per la logica, Progettazione app per la logica mostra i modelli comuni disponibili per poter iniziare più rapidamente.

   > [!NOTE] 
   > Se si seleziona **Aggiungi al dashboard**, l'app per la logica viene aperta automaticamente in Progettazione app per la logica. In caso contrario, è possibile trovare e aprire manualmente l'app per la logica.

5. Scegliere ora un modello di app per la logica. In **Modelli** scegliere **App per la logica vuota** per creare un'app per la logica completamente nuova.

   ![Selezionare il modello dell'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Progettazione app per la logica mostra ora i [*connettori*](../connectors/apis-list.md) e i [*trigger*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) che è possibile usare per avviare l'app per la logica e anche le azioni che è possibile aggiungere dopo un trigger per l'esecuzione di attività. Un trigger è un evento che crea un'istanza dell'app per la logica e avvia il flusso di lavoro dell'app per la logica. 
   Come primo elemento, all'app per la logica deve essere associato un trigger.

6. Nella casella di ricerca digitare "event grid" (griglia di eventi) come filtro. Selezionare questo trigger: **Azure Event Grid - On a resource event** (Griglia di eventi di Azure - Per un evento della risorsa)

   ![Selezionare questo trigger: Azure Event Grid - On a resource event (Griglia di eventi di Azure - Per un evento della risorsa)](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. Quando richiesto, accedere a Griglia di eventi di Azure con le credenziali di Azure.

   ![Accedere con le credenziali di Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Se è stato effettuato l'accesso con un account Microsoft personale, ad esempio @outlook.com o @hotmail.com, è possibile che il trigger di Griglia di eventi di Azure non venga visualizzato correttamente. In alternativa, scegliere [Connetti usando l'entità servizio](/azure-resource-manager/resource-group-create-service-principal-portal.md) o eseguire l'autenticazione come membro di Azure Active Directory associato alla sottoscrizione di Azure, ad esempio *nome-utente*@emailoutlook.onmicrosoft.com.

8. A questo punto eseguire la sottoscrizione dell'app per la logica agli eventi dell'editore. Specificare i dettagli per la sottoscrizione dell'evento, come indicato nella tabella seguente:

   ![Specificare i dettagli della sottoscrizione eventi](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | **Sottoscrizione** | *{sottoscrizione-Azure-macchina-virtuale}* | Selezionare la sottoscrizione di Azure dell'editore di eventi. Per questa esercitazione, selezionare la sottoscrizione di Azure per la macchina virtuale. | 
   | **Tipo di risorsa** | Microsoft.Resources.resourceGroups | Selezionare il tipo di risorsa dell'editore di eventi. Per questa esercitazione selezionare il valore specificato in modo che l'app per la logica monitori solo i gruppi di risorse. | 
   | **Nome risorsa** | *{nome-gruppo-risorse-macchina-virtuale}* | Selezionare il nome della risorsa dell'editore. Per questa esercitazione, selezionare il nome del gruppo di risorse per la macchina virtuale. | 
   | Per visualizzare le impostazioni facoltative, scegliere **Mostra opzioni avanzate**. | *{Vedere descrizioni}* | * **Prefix Filter** (Filtro prefisso): per questa esercitazione, lasciare vuota questa impostazione. Il comportamento predefinito corrisponde a tutti i valori. È possibile tuttavia specificare una stringa di prefisso come filtro, ad esempio un percorso o un parametro relativo a una determinata risorsa. <p>* **Suffix Filter** (Filtro suffisso): per questa esercitazione, lasciare vuota questa impostazione. Il comportamento predefinito corrisponde a tutti i valori. È possibile tuttavia specificare una stringa di suffisso come filtro, ad esempio un'estensione se si vuole usare solo specifici tipi di file.<p>* **Nome sottoscrizione**: specificare un nome univoco per la sottoscrizione eventi. |
   | | | 

   Al termine di questa operazione, il trigger di Griglia di eventi dovrebbe avere un aspetto simile all'esempio seguente:
   
   ![Dettagli del trigger di Griglia di eventi di esempio](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**. Per comprimere e nascondere i dettagli di un'azione nell'app per la logica, scegliere la barra del titolo dell'azione.

   ![Salvare l'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Quando si salva l'app per la logica con un trigger di Griglia di eventi, Azure crea automaticamente una sottoscrizione eventi per l'app per la logica alla risorsa specificata. Quando la risorsa pubblica un evento in Griglia di eventi, quindi, il servizio esegue automaticamente il push dell'evento all'app per la logica. L'evento attiva l'app per la logica, che crea ed esegue un'istanza di un flusso di lavoro che verrà definito nei passaggi successivi.

L'app per la logica è ora disponibile e in ascolto degli eventi generati da Griglia di eventi, ma non esegue alcuna operazione fino a quando non verranno aggiunte azioni al flusso di lavoro. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>Aggiungere una condizione che controlli le modifiche apportate a una macchina virtuale

Per eseguire il flusso di lavoro dell'app per la logica solo quando si verifica un evento specifico, aggiungere una condizione che controlli le operazioni di scrittura sulla macchina virtuale. Se questa condizione viene soddisfatta, l'app per la logica invia un messaggio di posta elettronica con i dettagli della macchina virtuale aggiornata.

1. In Progettazione app per la logica, nel trigger di Griglia di eventi, scegliere **Nuovo passaggio** > **Aggiungi una condizione**.

   ![Aggiungere una condizione all'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   Progettazione app per la logica aggiunge una condizione vuota al flusso di lavoro, inclusi i percorsi di azioni da seguire se la condizione viene soddisfatta o meno.

   ![Condizione vuota](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. Nella casella **Condizione** scegliere **Modifica in modalità avanzata**.
Immettere questa espressione:

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   La condizione dovrebbe avere ora un aspetto simile all'esempio seguente:

   ![Condizione vuota](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   Questa espressione controlla l'evento `body` per un oggetto `data` in cui la proprietà `operationName` è costituita dall'operazione `Microsoft.Compute/virtualMachines/write`. 
   Per altre informazioni, vedere [Schema di eventi di Griglia di eventi](../event-grid/event-schema.md).

3. Per fornire una descrizione della condizione, fare clic sui **puntini di sospensione** (**...** ) sulla forma della condizione e quindi scegliere **Rinomina**.

   > [!NOTE] 
   > Gli esempi più avanti in questa esercitazione descrivono anche i passaggi del flusso di lavoro dell'app per la logica.

4. Scegliere ora **Modifica in modalità di base** in modo che l'espressione venga risolta automaticamente, come illustrato:

   ![Condizione dell'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. Salvare l'app per la logica.

## <a name="send-email-when-your-virtual-machine-changes"></a>Inviare un messaggio di posta elettronica quando viene apportata una modifica alla macchina virtuale

Aggiungere ora un'[*azione* ](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) che consenta di ricevere un messaggio di posta elettronica quando viene soddisfatta la condizione specificata.

1. Nella casella **Se vero** della condizione scegliere **Aggiungi un'azione**.

   ![Aggiungere un'azione se la condizione viene soddisfatta](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. Nella casella di ricerca digitare "email" come filtro. Selezionare il connettore corrispondente al provider di posta elettronica in uso. Selezionare quindi l'azione di invio di un messaggio di posta elettronica relativa al connettore selezionato. ad esempio: 

   * Per un account aziendale o dell'istituto di istruzione di Azure, selezionare il connettore Office 365 Outlook. 
   * Per gli account Microsoft personali, selezionare il connettore Outlook.com. 
   * Per gli account Gmail, selezionare il connettore Gmail. 

   Si continuerà a usare il connettore Office 365 Outlook. 
   Se si usa un provider diverso, la procedura rimane invariata ma è possibile che l'interfaccia utente abbia un aspetto diverso. 

   ![Selezionare l'azione di invio di un messaggio di posta elettronica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. Se non si ha ancora una connessione per il provider di posta elettronica, accedere al proprio account di posta elettronica quando viene richiesta l'autenticazione.

4. Fornire i dettagli per il messaggio di posta elettronica, come specificato nella tabella seguente:

   ![Azione di posta elettronica vuota](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Per selezionare i campi disponibili nel flusso di lavoro, fare clic in una casella di modifica in modo che venga aperto l'elenco **Contenuto dinamico** oppure scegliere **Aggiungi contenuto dinamico**. Per altri campi, scegliere **Altro** per ogni sezione nell'elenco. Per chiudere l'elenco **Contenuto dinamico** scegliere **Aggiungi contenuto dinamico**.

   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | **To** | *{indirizzo-postaelettronica-destinatario}* |Immettere l'indirizzo di posta elettronica del destinatario. AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica. | 
   | **Oggetto** | Risorse aggiornate: **Oggetto**| Immettere il contenuto per l'oggetto del messaggio di posta elettronica. Per questa esercitazione, immettere il testo suggerito e selezionare il campo **Oggetto** dell'evento. In questo caso, l'oggetto del messaggio di posta elettronica include il nome per la risorsa aggiornata (macchina virtuale). | 
   | **Corpo** | Gruppo di risorse: **Argomento** <p>Tipo di evento: **Tipo di evento**<p>ID evento: **ID**<p>Ora: **Ora evento** | Immettere il contenuto per il corpo del messaggio di posta elettronica. Per questa esercitazione immettere il testo suggerito e selezionare **Argomento**, Tipo di evento**Argomento**, **ID** e **Ora evento** per l'evento in modo che nel messaggio di posta elettronica siano riportati il nome del gruppo di risorse, il tipo di evento, il timestamp dell'evento e l'ID dell'evento relativo all'aggiornamento. <p>Per aggiungere righe vuote nel contenuto, premere MAIUSC + INVIO. | 
   | | | 

   > [!NOTE] 
   > Se si seleziona un campo che rappresenta una matrice, la finestra di progettazione aggiunge automaticamente un ciclo **For each** all'azione che fa riferimento alla matrice. In questo modo, l'app per la logica esegue l'azione su ogni elemento della matrice.

   L'azione di invio di un messaggio di posta elettronica avrà ora un aspetto simile all'esempio seguente:

   ![Selezionare gli output da includere nel messaggio di posta elettronica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Al termine di questa operazione, l'app per la logica dovrebbe avere un aspetto simile all'esempio seguente:

   ![App per la logica completata](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. Salvare l'app per la logica. Per comprimere e nascondere i dettagli di ogni azione nell'app per la logica, scegliere la barra del titolo dell'azione.

   ![Salvare l'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   L'app per la logica è ora disponibile, ma prima di eseguire un'operazione attende che vengano apportate modifiche alla macchina virtuale. 
   Se a questo punto si vuole testare l'app per la logica, passare alla sezione successiva.

## <a name="test-your-logic-app-workflow"></a>Testare il flusso di lavoro per l'app per la logica

1. Per verificare che l'app per la logica riceva gli eventi specificati, aggiornare la macchina virtuale. 

   È possibile, ad esempio, ridimensionare la macchina virtuale nel portale di Azure o [ridimensionare la macchina virtuale con Azure PowerShell](../virtual-machines/windows/resize-vm.md). 

   Dopo alcuni istanti, si dovrebbe ricevere un messaggio di posta elettronica. ad esempio:

   ![Messaggio di posta elettronica sull'aggiornamento della macchina virtuale](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. Per rivedere le esecuzioni e la cronologia dei trigger dell'app per la logica, scegliere **Panoramica** dal menu dell'app per la logica. Per visualizzare altri dettagli su un'esecuzione, scegliere la riga per tale esecuzione.

   ![Cronologia di esecuzioni di app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. Per visualizzare gli input e gli output per ogni passaggio, espandere il passaggio da revisionare. Queste informazioni consentono di eseguire la diagnostica e il debug di problemi nell'app per la logica.
 
   ![Dettagli della cronologia di esecuzioni dell'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Congratulazioni, è stata creata ed eseguita un'app per la logica in grado di monitore gli eventi di risorse tramite Griglia di eventi e di inviare un messaggio di posta elettronica quando si verificano tali eventi. Si è inoltre appreso come creare con facilità flussi di lavoro per l'automazione dei processi e come integrare sistemi e servizi cloud.

## <a name="clean-up-resources"></a>Pulire le risorse

In questa esercitazione vengono usate risorse ed eseguite azioni che generano addebiti sulla sottoscrizione di Azure. Al termine dell'esercitazione e dei test, accertarsi di disabilitare o eliminare le risorse per evitare di incorrere in addebiti.

È possibile impedire l'esecuzione dell'app per la logica e l'invio di posta elettronica senza eliminare l'app. Scegliere **Panoramica** dal menu dell'app per la logica. Sulla barra degli strumenti scegliere **Disabilita**.

![Disabilitare l'app per la logica](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>domande frequenti

**D**: Quali altre attività di monitoraggio delle macchine virtuali è possibile eseguire con Griglia di eventi e le app per la logica? </br>
**R**: È possibile monitorare altre modifiche di configurazione, ad esempio:

* L'acquisizione da parte di una macchina virtuale dei diritti di controllo degli accessi in base al ruolo.
* L'implementazione di modifiche a un gruppo di sicurezza di rete o a un'interfaccia di rete.
* L'aggiunta o la rimozione di dischi in una macchina virtuale.
* L'assegnazione di un indirizzo IP pubblico all'interfaccia di rete di una macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Griglia di eventi](../event-grid/overview.md)
* [Concetti relativi a Griglia di eventi](../event-grid/concepts.md)
* [Guida introduttiva: Creare e instradare eventi personalizzati con la griglia di eventi](../event-grid/custom-event-quickstart.md)
* [Schema di eventi di Griglia di eventi](../event-grid/event-schema.md)
* [App per la logica di Azure](../logic-apps/logic-apps-what-are-logic-apps.md)
* [Creare flussi di lavoro di app per la logica con modelli predefiniti](../logic-apps/logic-apps-use-logic-app-templates.md)