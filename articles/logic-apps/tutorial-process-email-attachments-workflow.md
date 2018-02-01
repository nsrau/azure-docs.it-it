---
title: Creare flussi di lavoro per elaborare messaggi di posta elettronica e allegati - App per la logica di Azure | Microsoft Docs
description: Questa esercitazione illustra come creare flussi di lavoro automatizzati per l'elaborazione di messaggi di posta elettronica e allegati con App per la logica di Azure, Archiviazione di Azure e Funzioni di Azure
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 210731ce2e792452650b7a92cfc542c78a0e8014
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="process-emails-and-attachments-with-a-logic-app"></a>Elaborare messaggi di posta elettronica e allegati con un'app per la logica

App per la logica di Azure permette di automatizzare i flussi di lavoro e integrare dati tra servizi di Azure, servizi Microsoft, altre app software come un servizio (SaaS) e sistemi locali. Questa esercitazione illustra come creare un'[app per la logica](../logic-apps/logic-apps-overview.md) che gestisce i messaggi di posta elettronica in arrivo e gli eventuali allegati. L'app per la logica elabora il contenuto, lo salva in Archiviazione di Azure e invia notifiche per la verifica del contenuto. 

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

## <a name="prerequisites"></a>prerequisiti

* Un account di posta elettronica di un provider supportato da App per la logica, ad esempio un account Office 365 Outlook, Outlook.com o Gmail. Per altri provider, [vedere qui l'elenco dei connettori](https://docs.microsoft.com/connectors/).

  Questa app per la logica usa un account Office 365 Outlook. 
  Se si usa un account di posta elettronica diverso, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa.

* Scaricare e installare lo <a href="http://storageexplorer.com/" target="_blank">strumento gratuito Microsoft Azure Storage Explorer</a>. Questo strumento consente di controllare che il contenitore di archiviazione sia configurato correttamente.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a> con le credenziali dell'account Azure.

## <a name="set-up-storage-to-save-attachments"></a>Configurare l'archiviazione per salvare gli allegati

È possibile salvare i messaggi di posta elettronica in arrivo e gli allegati come BLOB in un [contenitore di archiviazione di Azure](../storage/common/storage-introduction.md). 

1. Prima di creare un contenitore di archiviazione, [creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) con queste impostazioni:

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Nome** | attachmentstorageacct | Nome per l'account di archiviazione | 
   | **Modello di distribuzione** | Resource Manager | [Modello di distribuzione](../azure-resource-manager/resource-manager-deployment-model.md) per la gestione della distribuzione delle risorse | 
   | **Tipo di account** | Scopo generico | [Tipo di account di archiviazione](../storage/common/storage-introduction.md#types-of-storage-accounts) | 
   | **Prestazioni** | Standard | Questa impostazione specifica i tipi di dati supportati e il supporto per l'archiviazione dei dati. Vedere [Tipi di account di archiviazione](../storage/common/storage-introduction.md#types-of-storage-accounts). | 
   | **Replica** | Archiviazione con ridondanza locale (LRS) | Questa impostazione specifica come vengono copiati, archiviati, gestiti e sincronizzati i dati. Vedere [Replica](../storage/common/storage-introduction.md#replication). | 
   | **Trasferimento sicuro necessario** | Disattivato | Questa impostazione specifica la sicurezza necessaria per le richieste dalle connessioni. Vedere [Richiedere il trasferimento sicuro](../storage/common/storage-require-secure-transfer.md). | 
   | **Sottoscrizione** | <*nome-sottoscrizione-Azure*> | Nome della sottoscrizione di Azure | 
   | **Gruppo di risorse** | LA-Tutorial-RG | Nome del [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) usato per organizzare e gestire le risorse correlate. <p>**Nota:** un gruppo di risorse si trova in un'area specifica. Anche se gli elementi di questa esercitazione potrebbero non essere disponibili in tutte le aree, provare a usare la stessa area, se possibile. | 
   | **Posizione** | Stati Uniti orientali 2 | Area in cui archiviare le informazioni sull'account di archiviazione | 
   | **Configurare reti virtuali** | Disattivato | Per questa esercitazione, mantenere l'impostazione **Disattivato**. | 
   |||| 

   È anche possibile usare [Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) o l'[interfaccia della riga di comando di Azure](../storage/common/storage-quickstart-create-storage-account-cli.md).
  
2. Dopo che Azure ha distribuito l'account di archiviazione, ottenere la relativa chiave di accesso:

   1. Dal menu dell'account di archiviazione in **Impostazioni** scegliere **Chiavi di accesso**. 
   2. Individuare **Key1** in **Chiavi predefinite** e il nome dell'account di archiviazione.

      ![Copiare e salvare la chiave e il nome dell'account di archiviazione](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   È anche possibile usare [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccountkey) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az_storage_account_keys_list). 

3. Creare un contenitore di archiviazione per gli allegati di posta elettronica.
   
   1. Dal menu dell'account di archiviazione, nel riquadro **Panoramica**, scegliere **BLOB** in **Servizi** e quindi scegliere **+ Contenitore**.

   2. Immettere "attachments" come nome del contenitore. In **Livello di accesso pubblico** selezionare **Contenitore (accesso in lettura anonimo per contenitori e BLOB)** e scegliere **OK**.

   È anche possibile usare [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az_storage_container_create). 
   Al termine, è possibile trovare il contenitore di archiviazione nell'account di archiviazione nel portale di Azure:

   ![Contenitore di archiviazione completato](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

Connettere quindi Storage Explorer all'account di archiviazione.

## <a name="set-up-storage-explorer"></a>Configurare Storage Explorer

A questo punto, connettere Storage Explorer all'account di archiviazione per poter verificare che l'app per la logica salvi correttamente gli allegati come BLOB nel contenitore di archiviazione.

1. Aprire Microsoft Azure Storage Explorer. Quando Storage Explorer chiede una connessione ad Archiviazione di Azure, scegliere **Use a storage account name and key** (Usa nome e chiave di un account di archiviazione) > **Avanti**.
Se non viene visualizzata alcuna richiesta, scegliere **Aggiungi account** sulla barra egli strumenti di Explorer.

2. In **Attach using Name and Key** (Collega con nome e chiave) immettere il nome dell'account di archiviazione e la chiave di accesso salvati in precedenza. Scegliere **Avanti** > **Connetti**.

3. Controllare che l'account di archiviazione e il contenitore vengano visualizzati correttamente in Storage Explorer:

   1. In **Explorer** espandere **(Local and Attached)** (Locale e collegato) > 
   **Account di archiviazione** > **attachmentstorageaccount** > 
   **Blob Containers** (Contenitori BLOB).

   2. Verificare che il contenitore "attachments" venga visualizzato. 
   Ad esempio: 

      ![Storage Explorer - verifica del contenitore di archiviazione](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Creare quindi una [funzione di Azure](../azure-functions/functions-overview.md) che rimuove il codice HTML dai messaggi di posta elettronica in arrivo.

## <a name="create-a-function-to-clean-html"></a>Creare una funzione per eliminare il codice HTML

Usare ora il frammento di codice fornito in questi passaggi per creare una funzione di Azure che rimuove il codice HTML da ogni messaggio di posta elettronica in arrivo. In questo modo, il contenuto di posta elettronica sarà più immediato e semplice da elaborare. È quindi possibile chiamare questa funzione dall'app per la logica.

1. Prima di creare una funzione, [creare un'app per le funzioni](../azure-functions/functions-create-function-app-portal.md) con queste impostazioni:

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Nome app** | CleanTextFunctionApp | Nome univoco globale e descrittivo per l'app per le funzioni | 
   | **Sottoscrizione** | <*nome-sottoscrizione-Azure*> | La stessa sottoscrizione di Azure usata in precedenza | 
   | **Gruppo di risorse** | LA-Tutorial-RG | Lo stesso gruppo di risorse di Azure usato in precedenza | 
   | **Piano di hosting** | Piano a consumo | Questa impostazione determina la modalità di allocazione e scalabilità delle risorse, ad esempio la potenza di calcolo, per l'esecuzione dell'app per le funzioni. Vedere il [confronto tra piani di hosting](../azure-functions/functions-scale.md). | 
   | **Posizione** | Stati Uniti orientali 2 | La stessa area usata in precedenza | 
   | **Archiviazione** | cleantextfunctionstorageacct | Creare un account di archiviazione per l'app per le funzioni. Usare solo lettere minuscole e numeri. <p>**Nota:** questo account di archiviazione contiene le app per le funzioni ed è diverso dall'account di archiviazione creato in precedenza per gli allegati di posta elettronica. | 
   | **Application Insights** | No | Attivare il monitoraggio delle applicazioni con [Application Insights](../application-insights/app-insights-overview.md), ma per questa esercitazione mantenere l'impostazione **No**. | 
   |||| 

   Se l'app per le funzioni non si apre automaticamente dopo la distribuzione, individuare l'app nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a>. Dal menu principale di Azure scegliere **Servizi app** e selezionare l'app per le funzioni.

   ![App per le funzioni creata](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Se la voce **Servizi app** non viene visualizzata nel menu di Azure, passare ad **Altri servizi**. Nella casella di ricerca trovare e selezionare **App per le funzioni**. Per altre informazioni, vedere [Creare una funzione](../azure-functions/functions-create-first-azure-function.md).

   È anche possibile usare l'[interfaccia della riga di comando di Azure](../azure-functions/functions-create-first-azure-function-azure-cli.md) o [PowerShell e i modelli di Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

2. In **App per le funzioni** espandere **CleanTextFunctionApp** e selezionare **Funzioni**. Sulla barra degli strumenti delle funzioni, scegliere **+ Nuova funzione**.

   ![Creare una nuova funzione](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

3. In **Scegliere un modello sotto oppure passare all'Avvio rapido** selezionare il modello di funzione **HttpTrigger - C#**.

   ![Selezionare il modello di funzione](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

4. In **Assegnare un nome alla funzione** immettere ```RemoveHTMLFunction```. In **HTTP trigger** (Trigger HTTP) > **Livello di autorizzazione** mantenere il valore predefinito **Funzione** e scegliere **Crea**.

   ![Assegnare un nome alla funzione](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

5. Dopo l'apertura dell'editor, sostituire il codice del modello con questo codice, che rimuove il codice HTML e restituisce i risultati al chiamante:

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

6. Al termine dell'operazione, scegliere **Salva**. Per testare la funzione, scegliere **Test** sotto l'icona a forma di freccia (**<**) sul bordo destro dell'editor. 

   ![Aprire il riquadro "Test"](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

7. Nel riquadro **Test**, in **Corpo della richiesta**, immettere questa riga e scegliere **Esegui**.

   ```json
   {"name": "<p><p>Testing my function</br></p></p>"}
   ```

   ![Testare la funzione](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   La finestra **Output** mostra il risultato della funzione:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Dopo aver controllato il funzionamento della funzione, creare l'app per la logica. Anche se questa esercitazione illustra come creare una funzione che rimuove il codice HTML dai messaggi di posta elettronica, App per la logica ha anche un connettore**** per la conversione da HTML a testo.

## <a name="create-your-logic-app"></a>Creare l'app per la logica

1. Dal menu principale di Azure scegliere **Nuovo** > **Enterprise Integration** > **App per la logica**.

   ![Creare l'app per la logica](./media/tutorial-process-email-attachments-workflow/create-logic-app.png)

2. In **Crea app per la logica** specificare le informazioni sull'app per la logica come mostrato e descritto di seguito. Al termine, scegliere **Aggiungi al dashboard** > **Crea**.

   ![Specificare le informazioni sull'app per la logica](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Nome** | LA-ProcessAttachment | Nome dell'app per la logica | 
   | **Sottoscrizione** | <*nome-sottoscrizione-Azure*> | La stessa sottoscrizione di Azure usata in precedenza | 
   | **Gruppo di risorse** | LA-Tutorial-RG | Lo stesso gruppo di risorse di Azure usato in precedenza |
   | **Posizione** | Stati Uniti orientali 2 | La stessa area usata in precedenza | 
   | **Log Analytics** | No | Per questa esercitazione, mantenere l'impostazione **No**. | 
   |||| 

3. Dopo che Azure distribuisce l'app, si apre Progettazione app per la logica, che mostra un video introduttivo e alcuni modelli di app per la logica comuni. In **Modelli** scegliere **App per la logica vuota**.

   ![Scegliere il modello App per la logica vuota](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Aggiungere quindi un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) che resta in ascolto dei messaggi di posta elettronica in arrivo con allegati. Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un determinato evento o quando nuovi dati soddisfano una condizione specifica. Per altre informazioni, vedere [Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md) (Creare la prima app per la logica).

## <a name="monitor-incoming-email"></a>Monitorare la posta elettronica in arrivo

1. Nella finestra di progettazione immettere "all'arrivo di un nuovo messaggio di posta elettronica" nella casella di ricerca. Selezionare il trigger per il provider di posta elettronica: **<*provider-posta-elettronica*> - All'arrivo di un nuovo messaggio di posta elettronica**, ad esempio:

   ![Selezionare il trigger per il provider di posta elettronica: "All'arrivo di un nuovo messaggio di posta elettronica"](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Per un account aziendale o dell'istituto di istruzione di Azure, selezionare Office 365 Outlook. 
   * Per gli account Microsoft personali, selezionare Outlook.com. 

2. Se viene chiesto di immettere le credenziali, accedere all'account di posta elettronica in modo che App per la logica possa connettersi all'account.

3. Specificare ora i criteri che il trigger deve usare per filtrare i nuovi messaggi di posta elettronica.

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
      | **Con allegato** | Sì | Recupera solo i messaggi di posta elettronica con allegati. <p>**Nota:** il trigger non rimuove alcun messaggio di posta elettronica dall'account, controlla solo i nuovi messaggi ed elabora solo i messaggi che corrispondono al filtro dell'oggetto. | 
      | **Includi allegati** | Sì | Recupera gli allegati da usare come input per il flusso di lavoro, invece che limitarsi a controllare la presenza di allegati. | 
      | **Filtro oggetto** | ```Business Analyst 2 #423501``` | Testo da trovare nell'oggetto del messaggio di posta elettronica | 
      |  |  |  | 

4. Per nascondere i dettagli del trigger per il momento, fare clic sulla barra del titolo del trigger.

   ![Comprimere la forma per nascondere i dettagli](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

5. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

   L'app per la logica è ora attiva, ma non esegue alcuna operazione oltre a controllare i messaggi di posta elettronica. 
   Aggiungere quindi una condizione che specifica i criteri per continuare il flusso di lavoro.

## <a name="check-for-attachments"></a>Controllare la presenza di allegati

1. Nel trigger scegliere **+ Nuovo passaggio** > **Aggiungi una condizione**.

   Quando viene visualizzata la forma Condizione, per impostazione predefinita viene visualizzato l'elenco dei parametri o l'elenco di contenuto dinamico che mostra tutti i parametri del passaggio precedente che è possibile includere come input del flusso di lavoro. 
   La larghezza del browser determina l'elenco visualizzato.

2. Rinominare la condizione con una descrizione migliore.

   1. Sulla barra del titolo della condizione fare clic sui **puntini di sospensione** (**...**) > **Rinomina**.

      Ad esempio, se il browser è in visualizzazione ridotta:

      ![Rinominare la condizione](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

      Se il browser è in visualizzazione estesa e l'elenco di contenuto dinamico blocca l'accesso al pulsante con i puntini di sospensione, chiudere l'elenco scegliendo **Aggiungi contenuto dinamico** all'interno della condizione. 
      
      ![Chiudere l'elenco di contenuto dinamico](./media/tutorial-process-email-attachments-workflow/close-dynamic-content-list.png)

   2. Rinominare la condizione con questa descrizione: ```If email has attachments and key subject phrase```

3. Descrivere la condizione fornendo un'espressione. 

   1. Nella forma Condizione scegliere **Modifica in modalità avanzata**.

      ![Modificare la condizione in modalità avanzata](./media/tutorial-process-email-attachments-workflow/edit-advanced-mode.png)

   2. Nella casella di testo immettere l'espressione:

      ```@equals(triggerBody()?['HasAttachment'], bool('true'))```

      Questa espressione confronta il valore della proprietà **Include allegati** del corpo del trigger, ovvero il messaggio di posta elettronica in questa esercitazione, con l'oggetto booleano ```True```. 
      Se i due valori sono uguali, il messaggio di posta elettronica ha almeno un allegato, la condizione viene soddisfatta e il flusso di lavoro continua.

      La condizione dovrebbe avere ora un aspetto simile all'esempio seguente:

      ![Espressione della condizione](./media/tutorial-process-email-attachments-workflow/condition-expression.png)

   3. Scegliere **Modifica in modalità di base**. L'espressione viene ora risolta come illustrato di seguito:

      ![Espressione risolta](./media/tutorial-process-email-attachments-workflow/condition-expression-resolved.png)

      > [!NOTE]
      > Per creare manualmente un'espressione, è necessario lavorare in modalità di base e tenere aperto l'elenco dinamico, per poter usare il generatore di espressioni. In **Espressione** è possibile selezionare le funzioni. In **Contenuto dinamico** è possibile selezionare i campi dei parametri da usare nelle funzioni.
      > Più avanti in questa esercitazione viene illustrato come creare manualmente le espressioni.

4. Salvare l'app per la logica.

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

## <a name="call-the-removehtmlfunction"></a>Chiamare la funzione RemoveHTMLFunction

1. Dal menu dell'app per la logica scegliere **Progettazione app per la logica**. Nel ramo **È true** scegliere **Aggiungi un'azione**.

2. Cercare "funzioni di azure" e selezionare l'azione: **Funzioni di Azure - Scegliere una funzione di Azure**

   ![Selezionare l'azione "Funzioni di Azure - Scegliere una funzione di Azure"](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

3. Selezionare l'app per le funzioni creata in precedenza: **CleanTextFunctionApp**

   ![Selezionare l'app per le funzioni di Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

4. Selezionare ora la funzione: **RemoveHTMLFunction**

   ![Selezionare la funzione di Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

5. Rinominare la forma della funzione con questa descrizione: ```Call RemoveHTMLFunction to clean email body``` 

6. Nella forma della funzione immettere l'input che la funzione deve elaborare. Specificare il corpo del messaggio di posta elettronica come illustrato e descritto di seguito:

   ![Specificare il corpo della richiesta per la funzione](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   1. In **Corpo della richiesta** immettere il testo: 
   
      ```{ "emailBody": ``` 

      Fino a quando questa voce non viene completata con i passaggi successivi, viene visualizzato un errore che indica che il formato JSON non è valido.
      Quando in precedenza questa funzione è stata testata, per l'input specificato per la funzione è stato usato JSON (JavaScript Object Notation). 
      Il corpo della richiesta deve quindi usare lo stesso formato. 

   2. Nell'elenco di contenuto dinamico o nell'elenco di parametri selezionare il campo **Corpo** in **All'arrivo di un nuovo messaggio di posta elettronica**.
   Dopo il campo **Corpo** aggiungere la parentesi graffa chiusa:```}```

      ![Specificare il corpo della richiesta da passare alla funzione](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing2.png)

      Nella definizione dell'app per la logica questa voce è nel formato seguente:

      ```{ "emailBody": "@triggerBody()?['Body']" }```

7. Salvare l'app per la logica.

Aggiungere quindi un'azione che crea un BLOB nel contenitore di archiviazione per salvare il corpo del messaggio di posta elettronica.

## <a name="create-blob-for-email-body"></a>Creare un BLOB per il corpo del messaggio di posta elettronica

1. Nella forma della funzione di Azure scegliere **Aggiungi un'azione**. 

2. In **Scegliere un'azione** cercare"blob" e selezionare questa azione: **Archiviazione BLOB di Azure - Crea BLOB**

   ![Aggiungere un'azione per creare un BLOB per il corpo del messaggio di posta elettronica](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

3. Se non si ha una connessione a un account di archiviazione di Azure, creare una connessione all'account di archiviazione con le impostazioni descritte e illustrate di seguito. Al termine dell'operazione, scegliere **Crea**.

   ![Creare una connessione all'account di archiviazione](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Connection Name** (Nome connessione) | AttachmentStorageConnection | Nome descrittivo per la connessione | 
   | **Storage Account** | attachmentstorageacct | Nome dell'account di archiviazione creato in precedenza per il salvataggio degli allegati | 
   |||| 

4. Rinominare l'azione **Crea BLOB** con questa descrizione: ```Create blob for email body```

5. Nell'azione **Crea BLOB** specificare le informazioni e selezionare i parametri per creare il BLOB come illustrato e descritto:

   ![Specificare le informazioni sul BLOB per il corpo del messaggio di posta elettronica](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Percorso cartella** | /attachments | Percorso e nome del contenitore creato in precedenza. È anche possibile cercare e selezionare un contenitore. | 
   | **Nome BLOB** | Campo **Da** | Passare il nome del mittente di posta elettronica come nome del BLOB. Nell'elenco di contenuto dinamico o nell'elenco di parametri selezionare **Da** in **All'arrivo di un nuovo messaggio di posta elettronica**. | 
   | **Contenuto BLOB** | Campo **Contenuto** | Passare il corpo del messaggio di posta elettronica senza codice HTML come contenuto del BLOB. Nell'elenco di contenuto dinamico o nell'elenco di parametri selezionare **Corpo** in **Call RemoveHTMLFunction to clean email body**. |
   |||| 

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

L'app per la logica usa un ciclo **For each** per elaborare ogni allegato al messaggio di posta elettronica.

1. Nella forma **Create blob for email body** scegliere **… Altro** e selezionare il comando: **Aggiungi For each**

   ![Aggiungere un ciclo For each](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

2. Rinominare il ciclo con questa descrizione: ```For each email attachment```

3. Specificare ora i dati che il ciclo deve elaborare. Fare clic nella casella **Selezionare un output dai passaggi precedenti**. Nell'elenco di contenuto dinamico o nell'elenco di parametri selezionare **Allegati**. 

   ![Selezionare "Allegati"](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Il campo **Allegati** passa una matrice che contiene tutti gli allegati inclusi in un messaggio di posta elettronica. 
   Il ciclo **For each** ripete le azioni su ogni elemento passato con la matrice.

4. Salvare l'app per la logica.

Aggiungere quindi l'azione che consente di salvare ogni allegato come BLOB nel contenitore di archiviazione **attachments**.

## <a name="create-blobs-for-attachments"></a>Creare BLOB per gli allegati

1. Nel ciclo **For each** scegliere **Aggiungi un'azione**per poter specificare l'attività da eseguire su ogni allegato trovato.

   ![Aggiungere un'azione al ciclo](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

2. In **Scegliere un'azione** cercare "blob" e selezionare questa azione: **Archiviazione BLOB di Azure - Crea BLOB**

   ![Aggiungere un'azione per creare un BLOB](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

3. Rinominare l'azione **Crea BLOB 2** con questa descrizione: ```Create blob for each email attachment```

4. Nell'azione **Create blob for each email attachment** specificare le informazioni e selezionare i parametri per creare il BLOB come illustrato e descritto:

   ![Specificare le informazioni sul BLOB](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Percorso cartella** | /attachments | Percorso e nome del contenitore creato in precedenza. È anche possibile cercare e selezionare un contenitore. | 
   | **Nome BLOB** | Campo **Nome** | Nell'elenco di contenuto dinamico o nell'elenco di parametri selezionare **Nome** per passare il nome dell'allegato come nome del BLOB. | 
   | **Contenuto BLOB** | Campo **Contenuto** | Nell'elenco di contenuto dinamico o nell'elenco di parametri selezionare **Contenuto** per passare il contenuto dell'allegato come contenuto del BLOB. |
   |||| 

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

1. Nel ramo **È true** nel ciclo **For each email attachment** scegliere **Aggiungi un'azione**. 

   ![Aggiungere un'azione nel ciclo "For each"](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

2. In **Scegliere un'azione** cercare "invia messaggio di posta elettronica" e quindi selezionare l'azione "invia messaggio di posta elettronica" per il provider di posta elettronica desiderato. Per filtrare l'elenco di azioni in base a un servizio specifico, selezionare prima di tutto il connettore in **Connettori**.

   ![Selezionare l'azione "invia un messaggio di posta elettronica" per il provider di posta elettronica](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Per un account aziendale o dell'istituto di istruzione di Azure, selezionare Office 365 Outlook. 
   * Per gli account Microsoft personali, selezionare Outlook.com. 

3. Se viene chiesto di immettere le credenziali, accedere all'account di posta elettronica in modo che App per la logica crei una connessione all'account.

4. Rinominare l'azione **Invia un messaggio di posta elettronica** con questa descrizione: ```Send email for review```

5. Specificare le informazioni per l'azione e selezionare i campi da includere nel messaggio di posta elettronica come illustrato e descritto. Per aggiungere righe vuote in una casella di modifica, premere MAIUSC+INVIO.  

   Ad esempio, se si usa l'elenco di contenuto dinamico:

   ![Inviare notifiche di posta elettronica](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Se nell'elenco non è possibile trovare il campo desiderato, selezionare **Vedi altro** accanto a **All'arrivo di un nuovo messaggio di posta elettronica** nell'elenco di contenuto dinamico o alla fine dell'elenco di parametri.

   | Impostazione | Valore | Note | 
   | ------- | ----- | ----- | 
   | **To** | <*indirizzo-posta-elettronica-destinatario*> | AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica. | 
   | **Oggetto**  | ```ASAP - Review applicant for position: ``` **Oggetto** | Oggetto del messaggio di posta elettronica da includere. Nell'elenco di contenuto dinamico o nell'elenco di parametri selezionare il campo **Oggetto** in **All'arrivo di un nuovo messaggio di posta elettronica**. | 
   | **Corpo** | ```Please review new applicant:``` <p>```Applicant name: ``` **Da** <p>```Application file location: ``` **Percorso** <p>```Application email content: ``` **Corpo** | Contenuto del corpo del messaggio di posta elettronica. Nell'elenco di contenuto dinamico o nell'elenco di parametri selezionare questi campi: <p>- Campo **Da** in **All'arrivo di un nuovo messaggio di posta elettronica** </br>- Campo **Percorso** in **Create blob for email body** </br>- Campo **Corpo** in **Call RemoveHTMLFunction to clean email body** | 
   |||| 

   Se si seleziona un campo che contiene una matrice, ad esempio **Contenuto**, che è una matrice contenente allegati, la finestra di progettazione aggiunge automaticamente un ciclo "For each" intorno all'azione che fa riferimento a tale campo. 
   In questo modo, l'app per la logica può eseguire l'azione su ogni elemento della matrice. 
   Per rimuovere il ciclo, rimuovere il campo per la matrice, spostare l'azione di riferimento fuori dal ciclo, scegliere i puntini di sospensione (**...** ) sulla barra del titolo del ciclo e scegliere **Elimina**.
     
6. Salvare l'app per la logica. 

Testare quindi l'app per la logica, che sarà simile a questo esempio:

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

Quando non è più necessario, eliminare il gruppo di risorse contenente l'app per la logica e le risorse correlate. Nel menu principale di Azure passare a **Gruppi di risorse** e selezionare il gruppo di risorse per l'app per la logica. Scegliere **Elimina gruppo di risorse**. Immettere il nome del gruppo di risorse come conferma e scegliere **Elimina**.

![Eliminare il gruppo di risorse dell'app per la logica](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'app per la logica per l'elaborazione e l'archiviazione degli allegati di posta elettronica integrando servizi di Azure, come Archiviazione di Azure e Funzioni di Azure. Leggere ora altre informazioni sui connettori che è possibile usare per creare app per la logica.

> [!div class="nextstepaction"]
> [Altre informazioni sui connettori di App per la logica](../connectors/apis-list.md)