---
title: Esercitazione - Automatizzare l'elaborazione di messaggi di posta elettronica e allegati - App per la logica di Azure | Microsoft Docs
description: Esercitazione - Creare flussi di lavoro automatizzati per la gestione di messaggi di posta elettronica e allegati con App per la logica di Azure, Archiviazione di Azure e Funzioni di Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
manager: jeconnoc
ms.topic: tutorial
ms.custom: mvc
ms.date: 07/20/2018
ms.openlocfilehash: 7efa2f19aedfbd00ccced8b755d3b02847b31444
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508607"
---
# <a name="tutorial-automate-handling-emails-and-attachments-with-azure-logic-apps"></a>Esercitazione: automatizzare la gestione di messaggi di posta elettronica e allegati con App per la logica di Azure

App per la logica di Azure permette di automatizzare i flussi di lavoro e integrare dati tra servizi di Azure, servizi Microsoft, altre app software come un servizio (SaaS) e sistemi locali. Questa esercitazione illustra come creare un'[app per la logica](../logic-apps/logic-apps-overview.md) che gestisce i messaggi di posta elettronica in arrivo e gli eventuali allegati. Questa app per la logica analizza il contenuto dei messaggi di posta elettronica, lo salva in Archiviazione di Azure e invia notifiche per la verifica del contenuto. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare [Archiviazione di Azure](../storage/common/storage-introduction.md) e Storage Explorer per controllare i messaggi di posta elettronica e gli allegati salvati.
> * Creare una [funzione di Azure](../azure-functions/functions-overview.md) che rimuove il codice HTML dai messaggi di posta elettronica. L'esercitazione include il codice che è possibile usare per questa funzione.
> * Creare un'app per la logica vuota.
> * Aggiungere un trigger per il monitoraggio dei messaggi di posta elettronica per individuare gli allegati.
> * Aggiungere una condizione che controlla se i messaggi di posta elettronica hanno allegati.
> * Aggiungere un'azione che chiama la funzione di Azure quando un messaggio di posta elettronica ha allegati.
> * Aggiungere un'azione che crea BLOB di archiviazione per i messaggi di posta elettronica e gli allegati.
> * Aggiungere un'azione che invia notifiche tramite posta elettronica.

Al termine, a livello generale l'app per la logica dovrebbe avere un flusso di lavoro simile al seguente:

![App per la logica completata](./media/tutorial-process-email-attachments-workflow/overview.png)

Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account di Azure gratuito</a> prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

* Un account di posta elettronica di un provider supportato da App per la logica, ad esempio un account Office 365 Outlook, Outlook.com o Gmail. Per altri provider, [vedere qui l'elenco dei connettori](https://docs.microsoft.com/connectors/).

  Questa app per la logica usa un account Office 365 Outlook. 
  Se si usa un account di posta elettronica diverso, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa.

* Scaricare e installare lo <a href="https://storageexplorer.com/" target="_blank">strumento gratuito Microsoft Azure Storage Explorer</a>. Questo strumento consente di controllare che il contenitore di archiviazione sia configurato correttamente.

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure

Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a> con le credenziali dell'account Azure.

## <a name="set-up-storage-to-save-attachments"></a>Configurare l'archiviazione per salvare gli allegati

È possibile salvare i messaggi di posta elettronica in arrivo e gli allegati come BLOB in un [contenitore di archiviazione di Azure](../storage/common/storage-introduction.md). 

1. Prima di creare un contenitore di archiviazione, [creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md) con queste impostazioni:

   | Impostazione | Valore | DESCRIZIONE | 
   |---------|-------|-------------| 
   | **Nome** | attachmentstorageacct | Nome per l'account di archiviazione | 
   | **Modello di distribuzione** | Resource Manager | [Modello di distribuzione](../azure-resource-manager/resource-manager-deployment-model.md) per la gestione della distribuzione delle risorse | 
   | **Tipo di account** | Scopo generico | [Tipo di account di archiviazione](../storage/common/storage-introduction.md#types-of-storage-accounts) | 
   | **Posizione** | Stati Uniti occidentali | Area in cui archiviare le informazioni sull'account di archiviazione | 
   | **Replica** | Archiviazione con ridondanza locale (LRS) | Questa impostazione specifica come vengono copiati, archiviati, gestiti e sincronizzati i dati. Vedere [Archiviazione con ridondanza locale (LRS): ridondanza dei dati a basso costo per Archiviazione di Azure](../storage/common/storage-redundancy-lrs.md). | 
   | **Prestazioni** | Standard | Questa impostazione specifica i tipi di dati supportati e il supporto per l'archiviazione dei dati. Vedere [Tipi di account di archiviazione](../storage/common/storage-introduction.md#types-of-storage-accounts). | 
   | **Trasferimento sicuro necessario** | Disabled | Questa impostazione specifica la sicurezza necessaria per le richieste dalle connessioni. Vedere [Richiedere il trasferimento sicuro](../storage/common/storage-require-secure-transfer.md). | 
   | **Sottoscrizione** | <*nome-sottoscrizione-Azure*> | Nome della sottoscrizione di Azure | 
   | **Gruppo di risorse** | LA-Tutorial-RG | Nome del [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) usato per organizzare e gestire le risorse correlate. <p>**Nota:** un gruppo di risorse si trova in un'area specifica. Anche se gli elementi in questa esercitazione potrebbero non essere disponibili in tutte le aree, provare a usare la stessa area, se possibile. | 
   | **Configurare reti virtuali** | Disabled | Per questa esercitazione, mantenere l'impostazione **Disattivato**. | 
   |||| 

   Per creare l'account di archiviazione è anche possibile usare [Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) o l'[interfaccia della riga di comando di Azure](../storage/common/storage-quickstart-create-storage-account-cli.md).

2. Dopo che Azure ha distribuito l'account di archiviazione, ottenere la relativa chiave di accesso:

   1. Nel menu dell'account di archiviazione selezionare **Chiavi di accesso** in **Impostazioni**. 

   2. Copiare il nome dell'account di archiviazione e **key1** e quindi salvare tali valori in una posizione sicura.

      ![Copiare e salvare la chiave e il nome dell'account di archiviazione](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   Per ottenere la chiave di accesso dell'account di archiviazione è anche possibile usare [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccountkey) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az-storage-account-keys-list). 

3. Creare un contenitore di archiviazione BLOB per gli allegati di posta elettronica.
   
   1. Nel menu dell'account di archiviazione selezionare **Panoramica**. 
   In **Servizi** selezionare **BLOB**.

      ![Aggiungere un contenitore di archiviazione BLOB](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   2. Nella pagina **Contenitori** visualizzata selezionare **Contenitore** sulla barra degli strumenti. 

   3. In **Nuovo contenitore** immettere "attachments" come nome del contenitore. 
   In **Livello di accesso pubblico** selezionare **Contenitore (accesso in lettura anonimo per contenitori e BLOB)** e quindi scegliere **OK**.

      Al termine, è possibile trovare il contenitore di archiviazione nell'account di archiviazione nel portale di Azure:

      ![Contenitore di archiviazione completato](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   Per creare un contenitore di archiviazione è anche possibile usare [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create). 

Connettere quindi Storage Explorer all'account di archiviazione.

## <a name="set-up-storage-explorer"></a>Configurare Storage Explorer

A questo punto, connettere Storage Explorer all'account di archiviazione per poter verificare che l'app per la logica possa salvare correttamente gli allegati come BLOB nel contenitore di archiviazione.

1. Aprire Microsoft Azure Storage Explorer. 

   Storage Explorer richiede una connessione all'account di archiviazione. 

2. Nel riquadro **Connect to Azure Storage** (Connetti ad Archiviazione di Azure) selezionare **Use a storage account name and key** (Usa nome e chiave di un account di archiviazione) e quindi scegliere **Next** (Avanti). 

   ![Storage Explorer: stabilire la connessione all'account di archiviazione](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > Se non viene visualizzata alcuna richiesta, scegliere **Add account** (Aggiungi account) sulla barra degli strumenti di Storage Explorer.

3. In **Account name** (Nome account) specificare il nome dell'account di archiviazione. In **Account key** (Chiave account) specificare la chiave di accesso salvata in precedenza. Scegliere **Avanti**.

4. Verificare le informazioni di connessione e quindi scegliere **Connect** (Connetti). 

   Storage Explorer crea la connessione e visualizza l'account di archiviazione nella finestra Explorer sotto **(Local and Attached)** (Locale e collegato) > **Storage Accounts** (Account di archiviazione). 

5. Per trovare il contenitore di archiviazione BLOB, in **Storage Accounts** (Account di archiviazione) espandere l'account di archiviazione (in questo caso, **attachmentstorageacct**) e quindi **Blob Containers** (Contenitori BLOB), in cui si trova **attachments**. Ad esempio: 

   ![Storage Explorer: trovare il contenitore di archiviazione](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Creare quindi una [funzione di Azure](../azure-functions/functions-overview.md) che rimuove il codice HTML dai messaggi di posta elettronica in arrivo.

## <a name="create-function-to-clean-html"></a>Creare una funzione per eliminare il codice HTML

Usare ora il frammento di codice fornito in questi passaggi per creare una funzione di Azure che rimuove il codice HTML da ogni messaggio di posta elettronica in arrivo. In questo modo, il contenuto di posta elettronica sarà più immediato e semplice da elaborare. È quindi possibile chiamare questa funzione dall'app per la logica.

1. Prima di creare una funzione, [creare un'app per le funzioni](../azure-functions/functions-create-function-app-portal.md) con queste impostazioni:

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Nome app** | CleanTextFunctionApp | Nome univoco globale e descrittivo per l'app per le funzioni | 
   | **Sottoscrizione** | <*nome-sottoscrizione-Azure*> | La stessa sottoscrizione di Azure usata in precedenza | 
   | **Gruppo di risorse** | LA-Tutorial-RG | Lo stesso gruppo di risorse di Azure usato in precedenza | 
   | **Piano di hosting** | Piano a consumo | Questa impostazione determina la modalità di allocazione e scalabilità delle risorse, ad esempio la potenza di calcolo, per l'esecuzione dell'app per le funzioni. Vedere il [confronto tra piani di hosting](../azure-functions/functions-scale.md). | 
   | **Posizione** | Stati Uniti occidentali | La stessa area usata in precedenza | 
   | **Archiviazione** | cleantextfunctionstorageacct | Creare un account di archiviazione per l'app per le funzioni. Usare solo lettere minuscole e numeri. <p>**Nota:** questo account di archiviazione contiene le app per le funzioni ed è diverso dall'account di archiviazione creato in precedenza per gli allegati di posta elettronica. | 
   | **Application Insights** | Off | Attiva il monitoraggio delle applicazioni con [Application Insights](../azure-monitor/app/app-insights-overview.md). Per questa esercitazione, tuttavia, scegliere l'impostazione **No**. | 
   |||| 

   Se l'app per le funzioni non si apre automaticamente dopo la distribuzione, individuare l'app nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a>. Nel menu principale di Azure selezionare **App per le funzioni** e selezionare l'app per le funzioni. 

   ![Selezionare l'app per le funzioni](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   Se la voce **App per le funzioni** non viene visualizzata nel menu di Azure, passare a **Tutti i servizi**. Nella casella di ricerca trovare e selezionare **App per le funzioni**. Per altre informazioni, vedere [Creare una funzione](../azure-functions/functions-create-first-azure-function.md).

   In caso contrario, Azure apre automaticamente l'app per le funzioni come illustrato di seguito:

   ![App per le funzioni creata](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Per creare un'app per le funzioni è anche possibile usare l'[interfaccia della riga di comando di Azure](../azure-functions/functions-create-first-azure-function-azure-cli.md) oppure [PowerShell e i modelli di Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

2. In **App per le funzioni** espandere **CleanTextFunctionApp** e selezionare **Funzioni**. Sulla barra degli strumenti delle funzioni selezionare **Nuova funzione**.

   ![Creare una nuova funzione](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

3. In **Scegliere un modello sotto oppure passare all'Avvio rapido** aprire l'elenco **Scenario** e selezionare **Core**. Nel modello **Trigger HTTP** selezionare **C#**.

   ![Selezionare il modello di funzione](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   > [!NOTE]
   > In questo caso viene fornito il codice di esempio C# e non è quindi necessario conoscere C# per seguire l'esempio.

4. Nel riquadro **Nuova funzione** immettere ```RemoveHTMLFunction``` in **Nome**. Mantenere l'opzione **Livello di autorizzazione** impostata su **Funzione** e scegliere **Crea**.

   ![Assegnare un nome alla funzione](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

5. Dopo l'apertura dell'editor, sostituire il codice del modello con questo codice di esempio, che rimuove il codice HTML e restituisce i risultati al chiamante:

   ``` CSharp
   using System.Net;
   using System.Text.RegularExpressions;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await req.Content.ReadAsStringAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return req.CreateResponse(HttpStatusCode.OK, new { updatedBody });
   }
   ```

6. Al termine dell'operazione, scegliere **Salva**. Per testare la funzione, sul bordo destro dell'editor scegliere **Test** sotto l'icona a forma di freccia (**<**). 

   ![Aprire il riquadro "Test"](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

7. Nel riquadro **Test** immettere questa riga in **Corpo della richiesta** e scegliere **Esegui**.

   ```json
   {"name": "<p><p>Testing my function</br></p></p>"}
   ```

   ![Testare la funzione](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   La finestra **Output** mostra il risultato della funzione:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Dopo aver controllato il funzionamento della funzione, creare l'app per la logica. Anche se questa esercitazione illustra come creare una funzione che rimuove il codice HTML dai messaggi di posta elettronica, App per la logica offre anche un connettore per la conversione di **HTML in testo**.

## <a name="create-your-logic-app"></a>Creare l'app per la logica

1. Nel menu principale di Azure selezionare **Crea una risorsa** > 
**Integrazione** > **App per la logica**.

   ![Creare l'app per la logica](./media/tutorial-process-email-attachments-workflow/create-logic-app.png)

2. In **Crea app per la logica** specificare le informazioni sull'app per la logica come mostrato e descritto di seguito. Al termine, scegliere **Aggiungi al dashboard** > **Crea**.

   ![Specificare le informazioni sull'app per la logica](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Nome** | LA-ProcessAttachment | Nome dell'app per la logica | 
   | **Sottoscrizione** | <*nome-sottoscrizione-Azure*> | La stessa sottoscrizione di Azure usata in precedenza | 
   | **Gruppo di risorse** | LA-Tutorial-RG | Lo stesso gruppo di risorse di Azure usato in precedenza |
   | **Posizione** | Stati Uniti occidentali | La stessa area usata in precedenza | 
   | **Log Analytics** | Off | Per questa esercitazione, scegliere l'impostazione **No**. | 
   |||| 

3. Dopo che Azure distribuisce l'app, si apre Progettazione app per la logica, che mostra un video introduttivo e alcuni modelli di app per la logica comuni. In **Modelli** scegliere **App per la logica vuota**.

   ![Scegliere il modello App per la logica vuota](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Aggiungere quindi un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) che resta in ascolto dei messaggi di posta elettronica in arrivo con allegati. Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un determinato evento o quando nuovi dati soddisfano una condizione specifica. Per altre informazioni, vedere [Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md) (Creare la prima app per la logica).

## <a name="monitor-incoming-email"></a>Monitorare la posta elettronica in arrivo

1. Nella casella di ricerca della finestra di progettazione immettere "all'arrivo di un nuovo messaggio di posta elettronica" come filtro. Selezionare questo trigger per il provider di posta elettronica: **All'arrivo di un nuovo messaggio di posta elettronica - <*provider di posta elettronica*>**

   Ad esempio: 

   ![Selezionare questo trigger per il provider di posta elettronica: "All'arrivo di un nuovo messaggio di posta elettronica"](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Per un account aziendale o dell'istituto di istruzione di Azure, selezionare Office 365 Outlook. 
   * Per gli account Microsoft personali, selezionare Outlook.com. 

2. Se vengono richieste le credenziali, accedere all'account di posta elettronica in modo che App per la logica possa connettersi all'account.

3. Specificare quindi i criteri usati dal trigger per filtrare i nuovi messaggi di posta elettronica.

   1. Specificare cartella, intervallo e frequenza per la verifica dei messaggi di posta elettronica.

      ![Specificare cartella, intervallo e frequenza per la verifica dei messaggi di posta elettronica](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Impostazione | Valore | DESCRIZIONE | 
      | ------- | ----- | ----------- | 
      | **Cartella** | Posta in arrivo | Cartella di posta elettronica da controllare | 
      | **Interval** | 1 | Numero di intervalli di attesa tra i controlli | 
      | **Frequenza** | Minuto | Unità di tempo per ogni intervallo tra i controlli | 
      |  |  |  | 
  
   2. Scegliere **Mostra opzioni avanzate** e specificare queste impostazioni:

      | Impostazione | Valore | DESCRIZIONE | 
      | ------- | ----- | ----------- | 
      | **Con allegato** | Sì | Recupera solo i messaggi di posta elettronica con allegati. <p>**Nota:** il trigger non rimuove alcun messaggio di posta elettronica dall'account, ma controlla solo i nuovi messaggi ed elabora esclusivamente quelli che corrispondono al filtro dell'oggetto. | 
      | **Includi allegati** | Sì | Recupera gli allegati da usare come input per il flusso di lavoro, invece che limitarsi a controllare la presenza di allegati. | 
      | **Filtro oggetto** | ```Business Analyst 2 #423501``` | Testo da trovare nell'oggetto del messaggio di posta elettronica | 
      |  |  |  | 

4. Per nascondere i dettagli del trigger per il momento, fare clic sulla barra del titolo del trigger.

   ![Comprimere la forma per nascondere i dettagli](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

5. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

   L'app per la logica è ora attiva, ma non esegue alcuna operazione oltre a controllare i messaggi di posta elettronica. 
   Aggiungere quindi una condizione che specifica i criteri per continuare il flusso di lavoro.

## <a name="check-for-attachments"></a>Controllare la presenza di allegati

Aggiungere quindi una condizione per selezionare solo i messaggi di posta elettronica con allegati.

1. Nel trigger scegliere **Nuovo passaggio** > **Aggiungi una condizione**.

   !["Nuovo passaggio", "Aggiungi una condizione"](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

2. Rinominare la condizione con una descrizione migliore.

   1. Sulla barra del titolo della condizione fare clic sui **puntini di sospensione** (**...**) > **Rinomina**.

      ![Rinominare la condizione](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   2. Rinominare la condizione con questa descrizione: ```If email has attachments and key subject phrase```

3. Creare una condizione per verificare la presenza di messaggi di posta elettronica con allegati. 

   1. Nella prima riga sotto **E** fare clic all'interno della casella di sinistra. 
   Nell'elenco di contenuto dinamico visualizzato selezionare la proprietà **Con allegato**.

      ![Creare una condizione](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   2. Nella casella centrale mantenere l'operatore **è uguale a**.

   3. Nella casella di destra immettere **true** come valore da confrontare con il valore della proprietà **Has Attachment** del trigger.

      ![Creare una condizione](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      Se i due valori sono uguali, il messaggio di posta elettronica ha almeno un allegato, la condizione viene soddisfatta e il flusso di lavoro continua.

   Nella definizione dell'app per la logica sottostante, visualizzabile nella finestra dell'editor di codice, questa condizione si presenta come in questo esempio:

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

4. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

### <a name="test-your-condition"></a>Testare la condizione

Verificare ora se la condizione funziona correttamente:

1. Se l'app per la logica non è già in esecuzione, scegliere **Esegui** sulla barra degli strumenti della finestra di progettazione.

   Questo passaggio avvia manualmente l'app per la logica senza che sia necessario attendere la scadenza dell'intervallo specificato. 
   Tuttavia, non accadrà nulla finché il messaggio di posta elettronica di test non arriva nella Posta in arrivo. 

2. Inviare a se stessi un messaggio di posta elettronica che soddisfi questi criteri:

   * L'oggetto del messaggio di posta elettronica contiene il testo specificato per l'impostazione **Filtro oggetto** del trigger: ```Business Analyst 2 #423501```

   * Il messaggio di posta elettronica ha un allegato. 
   Per il momento, è sufficiente creare un file di testo vuoto e allegarlo al messaggio di posta elettronica.

   Quando si riceve il messaggio di posta elettronica, l'app per la logica controlla la presenza di allegati e del testo dell'oggetto specificato.
   Se la condizione viene soddisfatta, il trigger viene attivato e il motore di App per la logica crea un'istanza di app per la logica e avvia il flusso di lavoro. 

3. Per controllare che il trigger sia stato attivato e l'app per la logica sia stata eseguita correttamente, dal menu dell'app per la logica scegliere **Panoramica**.

   ![Controllare il trigger e la cronologia di esecuzione](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Se l'app per la logica non è stata attivata o eseguita nonostante l'esito positivo di un trigger, vedere [Risolvere i problemi di un'app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

Definire quindi le azioni da eseguire per il ramo **È true**. Per salvare il messaggio di posta elettronica insieme a eventuali allegati, rimuovere il codice HTML dal corpo del messaggio e quindi creare BLOB nel contenitore di archiviazione per il messaggio di posta elettronica e gli allegati.

> [!NOTE]
> L'app per la logica non deve eseguire alcuna azione per il ramo **È false** quando un messaggio di posta elettronica non ha allegati. Come esercizio aggiuntivo dopo aver completato questa esercitazione, è possibile aggiungere un'azione appropriata da eseguire per il ramo **È false**.

## <a name="call-removehtmlfunction"></a>Chiamare la funzione RemoveHTMLFunction

Questo passaggio aggiunge la funzione di Azure creata in precedenza all'app per la logica e passa il contenuto del corpo del messaggio di posta elettronica dal trigger di posta elettronica alla funzione.

1. Nel menu dell'app per la logica selezionare **Progettazione app per la logica**. Nel ramo **È true** scegliere **Aggiungi un'azione**.

   ![Aggiungere un'azione all'interno di "È true"](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

2. Nella casella di ricerca cercare "Funzioni di Azure" e selezionare questa azione: **Scegliere una funzione di Azure - Funzioni di Azure**

   ![Selezionare l'azione "Scegliere una funzione di Azure"](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

3. Selezionare l'app per la funzione creata in precedenza: **CleanTextFunctionApp**

   ![Selezionare l'app per le funzioni di Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

4. A questo punto selezionare la funzione: **RemoveHTMLFunction**

   ![Selezionare la funzione di Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

5. Rinominare la forma della funzione con questa descrizione: ```Call RemoveHTMLFunction to clean email body```

6. Specificare quindi l'input che dovrà essere elaborato dalla funzione. 

   1. In **Corpo della richiesta** immettere il testo seguente con uno spazio finale: 
   
      ```{ "emailBody": ``` 

      Nel corso delle operazioni sull'input dei passaggi successivi verrà visualizzato un errore di codice JSON non valido finché all'input non verrà applicato un formato JSON corretto.
      Quando in precedenza questa funzione è stata testata, per l'input specificato per la funzione è stato usato JSON (JavaScript Object Notation). 
      Di conseguenza, anche il corpo della richiesta deve usare lo stesso formato.

      Posizionando il cursore all'interno della casella **Corpo della richiesta** viene visualizzato l'elenco di contenuto dinamico ed è così possibile selezionare i valori delle proprietà disponibili dalle azioni precedenti. 
      
   2. Nell'elenco di contenuto dinamico selezionare la proprietà **Corpo** in **All'arrivo di un nuovo messaggio di posta elettronica**. Ricordarsi di aggiungere la parentesi graffa di chiusura ```}``` dopo questa proprietà.

      ![Specificare il corpo della richiesta da passare alla funzione](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   Al termine, l'input per la funzione si presenterà come in questo esempio:

   ![Corpo della richiesta completato da passare alla funzione](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

7. Salvare l'app per la logica.

Aggiungere quindi un'azione che crea un BLOB nel contenitore di archiviazione per poter salvare il corpo del messaggio di posta elettronica.

## <a name="create-blob-for-email-body"></a>Creare un BLOB per il corpo del messaggio di posta elettronica

1. Nel blocco **È true** scegliere **Aggiungi un'azione** sotto la funzione di Azure. 

2. Nella casella di ricerca immettere "Crea BLOB" come filtro e selezionare questa azione: **Crea BLOB - Archivio BLOB di Azure**

   ![Aggiungere un'azione per creare un BLOB per il corpo del messaggio di posta elettronica](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

3. Creare una connessione all'account di archiviazione con le impostazioni descritte e illustrate di seguito. Al termine dell'operazione, scegliere **Crea**.

   ![Creare una connessione all'account di archiviazione](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Connection Name** (Nome connessione) | AttachmentStorageConnection | Nome descrittivo per la connessione | 
   | **Storage Account** | attachmentstorageacct | Nome dell'account di archiviazione creato in precedenza per il salvataggio degli allegati | 
   |||| 

4. Rinominare l'azione **Crea BLOB** con questa descrizione: ```Create blob for email body```

5. Nell'azione **Crea BLOB** specificare le informazioni e selezionare i campi per creare il BLOB come illustrato e descritto:

   ![Specificare le informazioni sul BLOB per il corpo del messaggio di posta elettronica](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Percorso cartella** | /attachments | Percorso e nome del contenitore creato in precedenza. Per questo esempio, fare clic sull'icona a forma di cartella e quindi selezionare il contenitore "/attachments". | 
   | **Nome BLOB** | Campo **Da** | Per questo esempio, come nome del BLOB usare il nome del mittente. Fare clic all'interno di questa casella in modo da visualizzare l'elenco di contenuto dinamico e quindi selezionare il campo **Da** sotto l'azione **All'arrivo di un nuovo messaggio di posta elettronica**. | 
   | **Contenuto BLOB** | Campo **Contenuto** | Per questo esempio, come contenuto del BLOB usare il corpo del messaggio di posta elettronica senza codice HTML. Fare clic all'interno di questa casella in modo da visualizzare l'elenco di contenuto dinamico e quindi selezionare **Corpo** sotto l'azione **Call RemoveHTMLFunction to clean email body**. |
   |||| 

   Al termine, l'azione si presenterà come in questo esempio:

   ![Azione "Crea BLOB" completata](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

6. Salvare l'app per la logica. 

### <a name="check-attachment-handling"></a>Controllare la gestione degli allegati

Verificare ora se l'app per la logica gestisce i messaggi di posta elettronica nel modo specificato:

1. Se l'app per la logica non è già in esecuzione, scegliere **Esegui** sulla barra degli strumenti della finestra di progettazione.

2. Inviare a se stessi un messaggio di posta elettronica che soddisfi questi criteri:

   * L'oggetto del messaggio di posta elettronica contiene il testo specificato per l'impostazione **Filtro oggetto** del trigger: ```Business Analyst 2 #423501```

   * Il messaggio di posta elettronica ha almeno un allegato. 
   Per il momento, è sufficiente creare un file di testo vuoto e allegarlo al messaggio di posta elettronica.

   * Il messaggio di posta elettronica ha contenuto di test nel corpo, ad esempio: 

     ```
     Testing my logic app
     ```

   Se l'app per la logica non è stata attivata o eseguita nonostante l'esito positivo di un trigger, vedere [Risolvere i problemi di un'app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

3. Verificare che l'app per la logica abbia salvato il messaggio di posta elettronica nel contenitore di archiviazione corretto. 

   1. In Storage Explorer espandere **(Local and Attached)** (Locale e collegato) > 
   **Account di archiviazione** > **attachmentstorageacct (Esterno)** > 
   **Blob Containers** (Contenitori BLOB) > **attachments**.

   2. Controllare la presenza del messaggio di posta elettronica nel contenitore **attachments**. 

      A questo punto, nel contenitore è presente solo il messaggio di posta elettronica perché l'app per la logica non ha ancora elaborato gli allegati.

      ![Controllare la presenza del messaggio di posta elettronica salvato in Storage Explorer](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   3. Al termine, eliminare il messaggio di posta elettronica in Storage Explorer.

4. Facoltativamente, per testare il ramo **È false**, che in questo caso non esegue alcuna azione, è possibile inviare un messaggio di posta elettronica che non soddisfa i criteri.

Aggiungere quindi un ciclo per elaborare tutti gli allegati di posta elettronica.

## <a name="process-attachments"></a>Elaborare gli allegati

Per elaborare ogni allegato del messaggio di posta elettronica, aggiungere un ciclo **For each** al flusso di lavoro dell'app per la logica.

1. Sotto la forma **Create blob for email body** selezionare **Altro** > **Aggiungi For each**.

   ![Aggiungere un ciclo For each](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

2. Rinominare il ciclo con questa descrizione: ```For each email attachment```

3. Specificare ora i dati che il ciclo deve elaborare. Fare clic all'interno della casella **Selezionare un output dai passaggi precedenti** per aprire l'elenco di contenuto dinamico e quindi selezionare **Allegati**. 

   ![Selezionare "Allegati"](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Il campo **Allegati** passa una matrice contenente tutti gli allegati inclusi in un messaggio di posta elettronica. 
   Il ciclo **For each** ripete le azioni su ogni elemento passato con la matrice.

4. Salvare l'app per la logica.

Aggiungere quindi l'azione che consente di salvare ogni allegato come BLOB nel contenitore di archiviazione **attachments**.

## <a name="create-blob-for-each-attachment"></a>Creare un BLOB per ogni allegato

1. Nel ciclo **For each email attachment** scegliere **Aggiungi un'azione** per poter specificare l'attività da eseguire su ogni allegato trovato.

   ![Aggiungere un'azione al ciclo](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

2. Nella casella di ricerca immettere "Crea BLOB" come filtro e selezionare questa azione: **Crea BLOB - Archivio BLOB di Azure**

   ![Aggiungere un'azione per creare un BLOB](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

3. Rinominare l'azione **Crea BLOB 2** con questa descrizione: ```Create blob for each email attachment```

4. Nell'azione **Create blob for each email attachment** specificare le informazioni e selezionare le proprietà per ogni BLOB da creare come illustrato e descritto:

   ![Specificare le informazioni sul BLOB](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Percorso cartella** | /attachments | Percorso e nome del contenitore creato in precedenza. Per questo esempio, fare clic sull'icona a forma di cartella e quindi selezionare il contenitore "/attachments". | 
   | **Nome BLOB** | Campo **Nome** | Per questo esempio, come nome del BLOB usare il nome dell'allegato. Fare clic all'interno di questa casella in modo da visualizzare l'elenco di contenuto dinamico e quindi selezionare il campo **Nome** sotto l'azione **All'arrivo di un nuovo messaggio di posta elettronica**. | 
   | **Contenuto BLOB** | Campo **Contenuto** | Per questo esempio, come contenuto del BLOB usare il campo **Contenuto**. Fare clic all'interno di questa casella in modo da visualizzare l'elenco di contenuto dinamico e quindi selezionare il campo **Contenuto** sotto l'azione **All'arrivo di un nuovo messaggio di posta elettronica**. |
   |||| 

   Al termine, l'azione si presenterà come in questo esempio:

   ![Azione "Crea BLOB" completata](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

5. Salvare l'app per la logica. 

### <a name="check-attachment-handling"></a>Controllare la gestione degli allegati

Verificare ora se l'app per la logica gestisce gli allegati nel modo specificato:

1. Se l'app per la logica non è già in esecuzione, scegliere **Esegui** sulla barra degli strumenti della finestra di progettazione.

2. Inviare a se stessi un messaggio di posta elettronica che soddisfi questi criteri:

   * L'oggetto del messaggio di posta elettronica contiene il testo specificato per l'impostazione **Filtro oggetto** del trigger: ```Business Analyst 2 #423501```

   * Il messaggio di posta elettronica ha almeno due allegati. 
   Per il momento, è sufficiente creare due file di testo vuoti e allegarli al messaggio di posta elettronica.

   Se l'app per la logica non è stata attivata o eseguita nonostante l'esito positivo di un trigger, vedere [Risolvere i problemi di un'app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

3. Verificare che l'app per la logica abbia salvato il messaggio di posta elettronica e gli allegati nel contenitore di archiviazione corretto. 

   1. In Storage Explorer espandere **(Local and Attached)** (Locale e collegato) > 
   **Account di archiviazione** > **attachmentstorageacct (Esterno)** > 
   **Blob Containers** (Contenitori BLOB) > **attachments**.

   2. Controllare la presenza del messaggio di posta elettronica e degli allegati nel contenitore **attachments**.

      ![Controllare la presenza di messaggi di posta elettronica e allegati salvati](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   3. Al termine, eliminare il messaggio di posta elettronica e gli allegati in Storage Explorer.

Aggiungere quindi un'azione in modo che l'app per la logica invii un messaggio di posta elettronica per l'esame degli allegati.

## <a name="send-email-notifications"></a>Inviare notifiche di posta elettronica

1. Nel ramo **If true** nel ciclo **For each email attachment** (Per ogni allegato di posta elettronica) scegliere **Aggiungi un'azione**. 

   ![Aggiungere un'azione nel ciclo "For each"](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

2. Nella casella di ricerca immettere "invia messaggio di posta elettronica" come filtro e quindi selezionare l'azione "Invia un messaggio di posta elettronica" per il proprio provider di posta elettronica. 

   Per filtrare l'elenco di azioni in base a un servizio specifico, si può selezionare prima il connettore.

   ![Selezionare l'azione "invia un messaggio di posta elettronica" per il provider di posta elettronica](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Per un account aziendale o dell'istituto di istruzione di Azure, selezionare Office 365 Outlook. 
   * Per gli account Microsoft personali, selezionare Outlook.com. 

3. Se viene chiesto di immettere le credenziali, accedere all'account di posta elettronica in modo che App per la logica crei una connessione all'account.

4. Rinominare l'azione **Invia un messaggio di posta elettronica** con questa descrizione: ```Send email for review```

5. Specificare le informazioni per l'azione e selezionare i campi da includere nel messaggio di posta elettronica come illustrato e descritto. Per aggiungere righe vuote in una casella di modifica, premere MAIUSC+INVIO.  

   ![Inviare notifiche di posta elettronica](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Se nell'elenco di contenuto dinamico non si trova un campo previsto, scegliere **Vedi altro** accanto a **All'arrivo di un nuovo messaggio di posta elettronica**. 

   | Impostazione | Valore | Note | 
   | ------- | ----- | ----- | 
   | **Corpo** | ```Please review new applicant:``` <p>```Applicant name: ``` **Da** <p>```Application file location: ``` **Percorso** <p>```Application email content: ``` **Corpo** | Contenuto del corpo del messaggio di posta elettronica. Fare clic all'interno della casella, immettere il testo dell'esempio e quindi selezionare i campi seguenti nell'elenco di contenuto dinamico: <p>- Campo **Da** in **All'arrivo di un nuovo messaggio di posta elettronica** </br>- Campo **Percorso** in **Create blob for email body** </br>- Campo **Corpo** in **Call RemoveHTMLFunction to clean email body** | 
   | **Oggetto**  | ```ASAP - Review applicant for position: ``` **Oggetto** | Oggetto del messaggio di posta elettronica da includere. Fare clic all'interno della casella, immettere il testo dell'esempio e quindi selezionare il campo **Oggetto** nell'elenco di contenuto dinamico sotto **All'arrivo di un nuovo messaggio di posta elettronica**. | 
   | **To** | <*indirizzo-posta-elettronica-destinatario*> | AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica. | 
   |||| 

   > [!NOTE] 
   > Se si seleziona un campo contenente una matrice, ad esempio il campo **Contenuto** che è una matrice contenente allegati, la finestra di progettazione aggiunge automaticamente un ciclo "For each" intorno all'azione che fa riferimento a tale campo. In questo modo, l'app per la logica può eseguire l'azione su ogni elemento della matrice. Per rimuovere il ciclo, rimuovere il campo per la matrice, spostare l'azione di riferimento fuori dal ciclo, scegliere i puntini di sospensione (**...** ) sulla barra del titolo del ciclo e scegliere **Elimina**.
     
6. Salvare l'app per la logica. 

Testare quindi l'app per la logica, che si presenterà ora come in questo esempio:

![App per la logica completata](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Eseguire l'app per la logica

1. Inviare a se stessi un messaggio di posta elettronica che soddisfi questi criteri:

   * L'oggetto del messaggio di posta elettronica contiene il testo specificato per l'impostazione **Filtro oggetto** del trigger: ```Business Analyst 2 #423501```

   * Il messaggio di posta elettronica ha uno o più allegati. 
   È possibile riutilizzare un file di testo vuoto dal test precedente. 
   Per uno scenario più realistico, allegare un file di un curriculum.

   * Il corpo del messaggio di posta elettronica contiene testo, che è possibile copiare e incollare:

     ```
     Name: Jamal Hartnett   
     
     Street address: 12345 Anywhere Road   
     
     City: Any Town   
     
     State or Country: Any State   
     
     Postal code: 00000   
     
     Email address: jamhartnett@outlook.com   
     
     Phone number: 000-000-0000   
     
     Position: Business Analyst 2 #423501   

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook   

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  
     
     Certifications: Six Sigma Green Belt, Lean Project Management   
     
     Language skills: English, Mandarin, Spanish   
     
     Education: Master of Business Administration   
     ```

2. Eseguire l'app per la logica. In caso di esito positivo, l'app per la logica invia un messaggio di posta elettronica simile a questo esempio:

   ![Notifica tramite posta elettronica inviata dall'app per la logica](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Se non si riceve alcun messaggio di posta elettronica, controllare la cartella della posta indesiderata. 
   Il filtro della posta indesiderata potrebbe reindirizzare questi tipi di messaggi di posta elettronica. 
   In caso contrario, se non si è certi che l'app per la logica sia stata eseguita correttamente, vedere [Risolvere i problemi dell'app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

È stata creata ed eseguita un'app per la logica che automatizza le attività in diversi servizi di Azure e chiama codice personalizzato.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse contenente l'app per la logica e le risorse correlate. Nel menu principale di Azure passare a **Gruppi di risorse** e quindi selezionare il gruppo di risorse per l'app per la logica. Scegliere **Elimina gruppo di risorse**. Immettere il nome del gruppo di risorse come conferma e scegliere **Elimina**.

![Eliminare il gruppo di risorse dell'app per la logica](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'app per la logica per l'elaborazione e l'archiviazione degli allegati di posta elettronica integrando servizi di Azure, come Archiviazione di Azure e Funzioni di Azure. Leggere ora altre informazioni sui connettori che è possibile usare per creare app per la logica.

> [!div class="nextstepaction"]
> [Altre informazioni sui connettori di App per la logica](../connectors/apis-list.md)
