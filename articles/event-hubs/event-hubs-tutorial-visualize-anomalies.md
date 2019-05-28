---
title: Visualizzare le anomalie dei dati in eventi in tempo reale - Hub eventi di Azure | Microsoft Docs
description: 'Esercitazione: Visualizzare le anomalie dei dati in eventi in tempo reale inviati a Hub eventi di Microsoft Azure'
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.author: shvija
ms.topic: tutorial
ms.service: event-hubs
ms.custom: seodec18
ms.date: 02/26/2019
ms.openlocfilehash: d6786e4e3382c7c4d7a6a6a28c3cd3621df221c1
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867127"
---
# <a name="tutorial-visualize-data-anomalies-in-real-time-events-sent-to-azure-event-hubs"></a>Esercitazione: Visualizzare le anomalie dei dati in eventi in tempo reale inviati a Hub eventi di Azure

Con Hub eventi di Azure, è possibile usare Analisi di flusso di Azure per controllare i dati in ingresso e individuare le anomalie, per poi visualizzarle in Power BI. Supponiamo di avere migliaia di dispositivi che inviano costantemente dati in tempo reale a un hub eventi, aggiungendo fino a milioni di eventi al secondo. Come si controlla tale quantità di dati per individuare eventuali anomalie o errori? Ad esempio, cosa fare se i dispositivi inviano transazioni con carta di credito ed è necessario acquisire informazioni ogniqualvolta vengano eseguite più transazioni in diversi paesi/aree geografiche in un intervallo di tempo di 5 secondi? Questo può succedere se le carte di credito sono state rubate e qualcuno le sta usando per fare acquisti in più paesi allo stesso tempo. 

In questa esercitazione verrà simulato proprio questo esempio. Viene eseguita un'applicazione che crea e invia transazioni con carta di credito a un hub eventi. In seguito vengono letti i flussi di dati in tempo reale con Analisi di flusso di Azure, che separa le transazioni valide da quelle non valide, e quindi si usa Power BI per identificare visivamente le transazioni contrassegnate come non valide.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare uno spazio dei nomi di Hub eventi
> * Creare un hub eventi
> * Eseguire l'app che invia le transazioni con carta di credito
> * Configurare un processo di Analisi di flusso per elaborare le transazioni
> * Configurare una visualizzazione di Power BI per visualizzare i risultati

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito][] prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

- Installare [Visual Studio](https://www.visualstudio.com/). 
- È necessario un account di Power BI per analizzare l'output da un processo di Analisi di flusso. È possibile [provare gratuitamente Power BI](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="set-up-resources"></a>Configurare le risorse

Ai fini di questa esercitazione, sono necessari uno spazio dei nomi di Hub eventi e un hub eventi. È possibile creare queste risorse usando l'interfaccia della riga di comando di Azure o Azure PowerShell. Usare lo stesso gruppo di risorse e lo stesso percorso per tutte le risorse. Alla fine, poi, è possibile rimuovere tutti gli elementi in un unico passaggio tramite l'eliminazione del gruppo di risorse.

Le sezioni seguenti descrivono come eseguire questa procedura necessaria. Seguire l'interfaccia della riga di comando *o* le istruzioni di PowerShell per eseguire la procedura seguente:

1. Creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md). 

2. Creare uno spazio dei nomi di Hub eventi. 

3. Creare un hub eventi.

> [!NOTE]
> In ogni script sono disponibili delle variabili impostate che saranno necessarie più avanti nell'esercitazione. Tra queste sono inclusi il nome del gruppo di risorse ($resourceGroup), lo spazio dei nomi dell'hub eventi ( **$eventHubNamespace**) e il nome dell'hub eventi ( **$eventHubName**). Più avanti in questo articolo viene fatto riferimento a queste variabili con il prefisso del segno del dollaro ($), in modo che sia chiaro che sono state impostate nello script.

<!-- some day they will approve the tab control; 
  When that happens, put CLI and PSH in tabs. -->

### <a name="set-up-your-resources-using-azure-cli"></a>Configurare le risorse usando l'interfaccia della riga di comando di Azure

Copiare e incollare lo script seguente in Cloud Shell. Presupponendo che si sia già eseguito l'accesso, lo script verrà eseguito una riga alla volta.

Le variabili che devono essere globalmente univoche hanno l'elemento `$RANDOM` concatenato. Quando viene eseguito lo script e le variabili vengono configurate, viene generata una stringa numerica casuale che viene concatenata alla fine della stringa fissa, rendendola univoca.

```azurecli-interactive
# Set the values for location and resource group name.
location=westus
resourceGroup=ContosoResourcesEH

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
eventHubNamespace=ContosoEHNamespace$RANDOM
echo "Event Hub Namespace = " $eventHubNamespace

# Create the Event Hubs namespace.
az eventhubs namespace create --resource-group $resourceGroup \
   --name $eventHubNamespace \
   --location $location \
   --sku Standard

# The event hub name must be globally unique, so add a random number to the end.
eventHubName=ContosoEHhub$RANDOM
echo "event hub name = " $eventHubName

# Create the event hub.
az eventhubs eventhub create --resource-group $resourceGroup \
    --namespace-name $eventHubNamespace \
    --name $eventHubName \
    --message-retention 3 \
    --partition-count 2

# Get the connection string that authenticates the app with the Event Hubs service.
connectionString=$(az eventhubs namespace authorization-rule keys list \
   --resource-group $resourceGroup \
   --namespace-name $eventHubNamespace \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString \
   --output tsv)
echo "Connection string = " $connectionString 
```

### <a name="set-up-your-resources-using-azure-powershell"></a>Configurare le risorse usando Azure PowerShell

Copiare e incollare lo script seguente in Cloud Shell. Presupponendo che si sia già eseguito l'accesso, lo script verrà eseguito una riga alla volta.

Le variabili che devono essere globalmente univoche hanno l'elemento `$(Get-Random)` concatenato. Quando viene eseguito lo script e le variabili vengono configurate, viene generata una stringa numerica casuale che viene concatenata alla fine della stringa fissa, rendendola univoca.

```azurepowershell-interactive
# Log in to Azure account.
Login-AzAccount

# Set the values for the location and resource group.
$location = "West US"
$resourceGroup = "ContosoResourcesEH"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
$eventHubNamespace = "contosoEHNamespace$(Get-Random)"
Write-Host "Event Hub Namespace is " $eventHubNamespace

# The event hub name must be globally unique, so add a random number to the end.
$eventHubName = "contosoEHhub$(Get-Random)"
Write-Host "Event hub Name is " $eventHubName

# Create the Event Hubs namespace.
New-AzEventHubNamespace -ResourceGroupName $resourceGroup `
     -NamespaceName $eventHubNamespace `
     -Location $location

# Create the event hub.
$yourEventHub = New-AzEventHub -ResourceGroupName $resourceGroup `
    -NamespaceName $eventHubNamespace `
    -Name $eventHubName `
    -MessageRetentionInDays 3 `
    -PartitionCount 2

# Get the event hub key, and retrieve the connection string from that object.
# You need this to run the app that sends test messages to the event hub.
$eventHubKey = Get-AzEventHubKey -ResourceGroupName $resourceGroup `
    -Namespace $eventHubNamespace `
    -AuthorizationRuleName RootManageSharedAccessKey

# Save this value somewhere local for later use.
Write-Host "Connection string is " $eventHubKey.PrimaryConnectionString
```

## <a name="run-app-to-produce-test-event-data"></a>Eseguire l'app per produrre dati di evento di test

Gli [esempi in GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet) di Hub eventi includono un'[app di rilevamento delle anomalie](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/AnomalyDetector) che genera dati di test. Simula l'uso di carte di credito scrivendo transazioni con carta di credito nell'hub eventi, tra cui, in alcuni casi, transazioni diverse per la stessa carta di credito in diverse posizioni, in modo che vengano contrassegnate come anomalie. Per eseguire l'applicazione, seguire questa procedura: 

1. Scaricare gli [esempi di Hub eventi di Azure](https://github.com/Azure/azure-event-hubs/archive/master.zip) da GitHub e decomprimere il file in locale.

2. Passare alla cartella \azure-event-hubs-master\samples\DotNet\AnomalyDetector\ e fare doppio clic su AnomalyDetector.sln per aprire la soluzione in Visual Studio. 

3. Aprire Program.cs e sostituire **Event Hubs connection string** (Stringa di connessione hub eventi) con la stringa di connessione salvata durante l'esecuzione dello script. 

4. Sostituire **Nome hub eventi** con il nome del proprio hub eventi. Fare clic su F5 per eseguire l'applicazione. L'app inizia a inviare gli eventi all'hub eventi e continua fino a quando non ne ha inviati 1000. Vi sono alcune istanze in cui è necessario che l'app sia in esecuzione perché possano essere recuperati dati. Questi casi sono descritti nelle istruzioni seguenti, dove necessario.

## <a name="set-up-azure-stream-analytics"></a>Configurare Analisi di flusso di Azure

A questo punto è possibile trasmettere i dati nell'hub eventi. Per usare i dati in una visualizzazione di Power BI, per prima cosa configurare un processo di Analisi di flusso per recuperare i dati che vengono poi inseriti nella visualizzazione di Power BI.

### <a name="create-the-stream-analytics-job"></a>Creare il processo di Analisi di flusso

1. Nel portale di Azure fare clic su **Crea una risorsa**. Digitare **stream analytics** (analisi di flusso) nella casella di ricerca e premere **INVIO**. Selezionare **Processo di Analisi di flusso**. Fare clic su **Crea** nel riquadro del processo di Analisi di flusso. 

2. Immettere le informazioni seguenti per il processo:

   **Nome processo**: usare **contosoEHjob**. Questo campo è il nome del processo e deve essere globalmente univoco.

   **Sottoscrizione** Selezionare la propria sottoscrizione.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub eventi dell'utente, ovvero **ContosoResourcesEH**.

   **Località**: usare la stessa località usata nello script di configurazione, ovvero **Stati Uniti occidentali**.

   ![Screenshot che illustra come creare un nuovo processo di Analisi di flusso di Azure.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-add-job.png)

    Accettare le impostazioni predefinite nei campi rimanenti. Fare clic su **Crea**. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Aggiungere un input al processo di Analisi di flusso

Se non ci si trova nel portale nel riquadro **Processo di Analisi di flusso**, è possibile tornare al processo di Analisi di flusso facendo clic su **Gruppi di risorse** nel portale e selezionando il proprio gruppo di risorse ( **ContosoResourcesEH**). Con questa azione vengono visualizzate tutte le risorse del gruppo ed è possibile selezionare il processo di analisi di flusso. 

Le transazioni con carta di credito dell'hub eventi sono gli input per il processo di Analisi di flusso.

> [!NOTE]
> I valori per le variabili che iniziano con il segno di dollaro ($) vengono configurati negli script di avvio nelle sezioni precedenti. Qui è necessario usare gli stessi valori quando si specificano questi campi, ovvero lo spazio dei nomi di Hub eventi e il nome dell'hub eventi.

1. In **Topologia processo** fare clic su **Input**.

2. Nel riquadro **Input** fare clic su **Aggiungi input del flusso** e selezionare Hub eventi. Nella schermata che viene visualizzata compilare i campi seguenti:

   **Alias di input**: usare **contosoinputs**. Questo campo è il nome del flusso di input, usato quando si definisce la query per i dati.

   **Sottoscrizione** Selezionare la propria sottoscrizione.

   **Spazio dei nomi degli hub eventi**: selezionare lo spazio dei nomi di Hub eventi, ovvero $**eventHubNamespace**. 

   **Nome hub eventi**: fare clic su **Usa esistente** e selezionare l'hub eventi, ovvero $**eventHubName**.

   **Nome criteri hub eventi**: Selezionare **RootManageSharedAccessKey**.

   **Gruppo di consumer dell'hub eventi**: lasciare vuoto questo campo per usare il gruppo di consumer predefinito.

   Accettare le impostazioni predefinite nei campi rimanenti.

   ![Screenshot che illustra come aggiungere un flusso di input al processo di Analisi di flusso.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-inputs.png)

5. Fare clic su **Salva**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Aggiungere un output al processo di Analisi di flusso

1. In **Topologia processo** fare clic su **Output**. Questo campo è il nome del flusso di output, usato quando si definisce la query per i dati.

2. Nel riquadro **Output** fare clic su **Aggiungi** e selezionare **Power BI**. Nella schermata che viene visualizzata compilare i campi seguenti:

   **Alias di output**: usare **contosooutputs**. Questo campo è l'alias univoco per l'output. 

   **Nome set di dati**: usare **contosoehdataset**. Questo campo è il nome del set di dati da usare in Power BI. 

   **Nome tabella**: usare **contosoehtable**. Questo campo è il nome della tabella da usare in Power BI. 

   Accettare le impostazioni predefinite nei campi rimanenti.

   ![Screenshot che illustra come configurare l'output per un processo di Analisi di flusso.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-outputs.png)

3. Fare clic su **Autorizza** e accedere all'account di Power BI.

4. Accettare le impostazioni predefinite nei campi rimanenti.

5. Fare clic su **Salva**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurare la query del processo di Analisi di flusso

Questa query viene usata per recuperare i dati che vengono infine inviati per la visualizzazione in Power BI. La query usa **contosoinputs** e **contosooutputs**, che sono stati definiti in precedenza durante la configurazione del processo. La query recupera le transazioni con carta di credito che ritiene fraudolente, vale a dire quelle in cui con lo stesso numero di carta di credito sono state eseguite più transazioni in posizioni diverse in un intervallo di cinque secondi.

1. In **Topologia processo** fare clic su **Query**.

2. Sostituire la query con quella riportato di seguito: 

   ```SQL
   /* criteria for fraud:
      credit card purchases with the same card
      in different locations within 5 seconds
   */
   SELECT System.Timestamp AS WindowEnd, 
     COUNT(*) as FraudulentUses      
   INTO contosooutputs
   FROM contosoinputs CS1 TIMESTAMP BY [Timestamp]
       JOIN contosoinputs CS2 TIMESTAMP BY [Timestamp]
       /* where the credit card # is the same */
       ON CS1.CreditCardId = CS2.CreditCardId
       /* and time between the two is between 0 and 5 seconds */
       AND DATEDIFF(second, CS1, CS2) BETWEEN 0 AND 5
       /* where the location is different */
   WHERE CS1.Location != CS2.Location
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Fare clic su **Salva**.

### <a name="test-the-query-for-the-stream-analytics-job"></a>Testare la query per il processo di Analisi di flusso 

1. Eseguire l'app di rilevamento delle anomalie per inviare dati all'hub eventi mentre si sta configurando ed eseguendo il test. 

2. Nel riquadro Query fare clic sui puntini di sospensione accanto all'input **contosoinputs** e selezionare **Carica dati di esempio da input**.

3. Specificare che si vuole ottenere i dati di un intervallo di tre minuti e fare clic su **OK**. Attendere fino a quando non si riceve la notifica che i dati sono stati campionati.

4. Fare clic su **Test** e assicurarsi di ottenere i risultati. I risultati vengono visualizzati nella sezione **Risultati** del riquadro in basso a destra sotto la query.

5. Chiudere il riquadro della query.

### <a name="run-the-stream-analytics-job"></a>Eseguire il processo di Analisi di flusso

Nel processo di Analisi di flusso fare clic su **Avvia**, poi su **Adesso** e su **Avvia**. Dopo aver avviato correttamente il processo, lo stato del processo passa da **Interrotto** a **In esecuzione**.

## <a name="set-up-the-power-bi-visualizations"></a>Configurare le visualizzazioni di Power BI

1. Eseguire l'app di rilevamento delle anomalie per inviare dati all'hub eventi mentre si sta configurando la visualizzazione in Power BI. Potrebbe essere necessario eseguirla più volte, in quanto genera solo 1000 transazioni ad ogni esecuzione.

2. Accedere all'account [Power BI](https://powerbi.microsoft.com/).

3. Passare a **Area di lavoro personale**.

4. Fare clic su **Set di dati**.

   Dovrebbero essere visualizzati i set di dati specificati durante la creazione dell'output per il processo di Analisi di flusso (**contosoehdataset**). La prima volta, potrebbero volerci da 5 a 10 minuti perché il set di dati venga visualizzato.

5. Fare clic su **Dashboard**, quindi fare clic su **Crea** e selezionare **Dashboard**.

   ![Screenshot dei pulsanti Dashboard e Crea.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-dashboard.png)

6. Specificare il nome del dashboard e fare clic su **Crea**. Usare **Credit Card Anomalies**.

   ![Screenshot della specifica del nome del dashboard.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-name.png)

7. Nella pagina Dashboard fare clic su **Aggiungi riquadro**, selezionare **Dati di streaming personalizzati** nella sezione **Dati in tempo reale** e fare clic su **Avanti**.

   ![Screenshot della specifica dell'origine del riquadro.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-real-time-data.png)

8. Selezionare il set di dati (**contosoehdataset**) e fare clic su **Avanti**.

   ![Screenshot della specifica del set di dati.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-select-dataset.png)

9. Selezionare **Scheda** per il tipo di visualizzazione. In **Campi** fare clic su **Aggiungi valore** e selezionare **fraudulentuses**.

   ![Screenshot che specifica il tipo di visualizzazione e i campi.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-tile.png)

   Fare clic su **Avanti**.

10. Impostare il titolo su **Fraudulent uses** e il sottotitolo su **Sum in last few minutes**. Fare clic su **Apply**. Il riquadro viene salvato nel dashboard.

    ![Screenshot di specifica di titolo e sottotitolo per il riquadro del dashboard.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-tile-details.png)

    > [!IMPORTANT]
    > Quando si esegue l'applicazione di esempio e si trasmettono i dati all'hub eventi, il numero riportato su questo riquadro cambia rapidamente (ogni secondo). Il motivo è che la query di Analisi di flusso aggiorna effettivamente il valore **ogni secondo**. Aggiornare la query in una finestra a cascata di 3 minuti per vedere la somma degli ultimi minuti. 
11. Aggiungere un'altra visualizzazione. Ripetere i primi passaggi:

    * Fare clic su **Aggiungi riquadro**.
    * Selezionare **Dati in streaming personalizzati**. 
    * Fare clic su **Avanti**.
    * Selezionare il set di dati e fare clic su **Avanti**. 

12. In **Tipo di visualizzazione** selezionare **Grafico a linee**.

13. In **Asse** fare clic su **Aggiungi valore**e selezionare **windowend**. 

14. In **Valori** fare clic su **Aggiungi valore**e selezionare **fraudulentuses**.

15. In **Intervallo di tempo da visualizzare** selezionare gli ultimi cinque minuti. Fare clic su **Avanti**.

16. Specificare **Show fraudulent uses over time** per il titolo, lasciare vuoto il sottotitolo per il riquadro e fare clic su **Applica**. Si torna al dashboard.

17. Eseguire nuovamente l'app di rilevamento delle anomalie per inviare dati all'hub eventi. Il riquadro **Fraudulent uses** cambia quando vengono analizzati i dati, illustrati dal grafico a linee. 

    ![Screenshot che illustra i risultati in Power BI](./media/event-hubs-tutorial-visualize-anomalies/power-bi-results.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vogliono rimuovere tutte le risorse create, eliminare i dati di visualizzazione in Power BI e quindi il gruppo di risorse. Se si elimina un gruppo di risorse, vengono eliminate tutte le risorse contenute in esso. In questo caso vengono rimossi l'hub eventi, lo spazio dei nomi di Hub eventi, il processo di Analisi di flusso e il gruppo di risorse stesso. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Pulire le risorse nella visualizzazione di Power BI

Accedere all'account Power BI. Passare a **Area di lavoro personale**. Nella riga con il nome del dashboard, fare clic sull'icona del cestino. In seguito passare a **Seti di dati** e fare clic sull'icona del cestino per eliminare il set di dati (**contosoehdataset**).

### <a name="clean-up-resources-using-azure-cli"></a>Pulire le risorse usando l'interfaccia della riga di comando di Azure

Per rimuovere il gruppo di risorse, usare il comando [eliminazione del gruppo az](/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="clean-up-resources-using-powershell"></a>Pulire le risorse usando PowerShell

Per rimuovere il gruppo di risorse, usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:
> [!div class="checklist"]
> * Creare uno spazio dei nomi di Hub eventi
> * Creare un hub eventi
> * Eseguire l'app che consente di simulare eventi e di inviarli all'hub eventi
> * Configurare un processo di Analisi di flusso per elaborare gli eventi inviati all'hub
> * Configurare una visualizzazione di Power BI per visualizzare i risultati

Passare all'articolo successivo per altre informazioni su Hub eventi di Azure.

> [!div class="nextstepaction"]
> [Introduzione all'invio di messaggi a Hub eventi di Azure in .NET Standard](event-hubs-dotnet-standard-getstarted-send.md)

[Creare un account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
