---
title: Usare i comandi di controllo Esplora dati di Azure in Azure Data Factory
description: In questo argomento usare i comandi di controllo Esplora dati di Azure in Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264482"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Usare Azure Data Factory attività Command per eseguire i comandi di controllo Esplora dati di Azure

[Azure Data Factory](/azure/data-factory/) (ADF) è un servizio di integrazione dei dati basato sul cloud che consente di eseguire una combinazione di attività sui dati. Utilizzare ADF per creare flussi di lavoro basati sui dati per l'orchestrazione e l'automazione dello spostamento e della trasformazione dei dati. L'attività **comando di azure Esplora dati** in Azure Data Factory consente di eseguire i [comandi di controllo di Azure Esplora dati](/azure/kusto/concepts/#control-commands) all'interno di un flusso di lavoro di ADF. Questo articolo illustra come creare una pipeline con un'attività di ricerca e un'attività ForEach contenente un'attività di comando di Azure Esplora dati.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster e un database di Azure Esplora dati](create-cluster-database-portal.md)
* Origine dati.
* [data factory](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Creare una nuova pipeline

1. Selezionare lo strumento matita **autore** . 
1. Creare una nuova pipeline selezionando **+** , quindi selezionare **pipeline** nell'elenco a discesa.

   ![Crea nuova pipeline](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Creare un'attività di ricerca

Un' [attività Lookup](/azure/data-factory/control-flow-lookup-activity) può recuperare un set di dati da qualsiasi origine dati supportata da Azure Data Factory. L'output dell'attività Lookup può essere utilizzato in un'attività ForEach o in un'altra attività.

1. Nel riquadro **attività** , in **generale**, selezionare l'attività di **ricerca** . Trascinarla e rilasciarla nell'area di disegno principale a destra.
 
    ![Selezionare l'attività di ricerca](media/data-factory-command-activity/select-activity.png)

1. L'area di disegno contiene ora l'attività di ricerca creata. Usare le schede sotto l'area di disegno per modificare i parametri pertinenti. In **generale**, rinominare l'attività. 

    ![modifica attività di ricerca](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Fare clic sull'area di disegno vuota per visualizzare le proprietà della pipeline. Utilizzare la scheda **generale** per rinominare la pipeline. La pipeline è denominata *pipeline-4-docs*.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Creare un set di dati di Azure Esplora dati nell'attività Lookup

1. In **Impostazioni**selezionare il **set di dati di origine**di Azure Esplora dati creato in precedenza oppure selezionare **+ nuovo** per creare un nuovo set di dati.
 
    ![Aggiungi set di dati nelle impostazioni di ricerca](media/data-factory-command-activity/lookup-settings.png)

1. Selezionare il set di dati di **Azure Esplora dati (kusto)** dalla finestra **nuovo set di dati** . Selezionare **continua** per aggiungere il nuovo set di dati.

   ![Seleziona nuovo set di dati](media/data-factory-command-activity/select-new-dataset.png) 

1. I nuovi parametri del set di dati di Azure Esplora dati sono visibili nelle **Impostazioni**. Per aggiornare i parametri, selezionare **modifica**.

    ![impostazioni di ricerca con il set di dati Esplora dati di Azure](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. Verrà visualizzata la nuova scheda **AzureDataExplorerTable** nell'area di disegno principale. 
    * Selezionare **generale** e modificare il nome del set di dati. 
    * Selezionare **connessione** per modificare le proprietà del set di dati. 
    * Selezionare il **servizio collegato** dall'elenco a discesa oppure selezionare **+ nuovo** per creare un nuovo servizio collegato.

    ![Modificare le proprietà del set di dati Esplora dati di Azure](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Quando si crea un nuovo servizio collegato, viene visualizzata la pagina **nuovo servizio collegato (Azure Esplora dati)** :

    ![ADX nuovo servizio collegato](media/data-factory-command-activity/adx-new-linked-service.png)

   * Selezionare il **nome** per il servizio collegato Azure Esplora dati. Se necessario, aggiungere una **Descrizione** .
   * In **Connetti tramite Integration Runtime**modificare le impostazioni correnti, se necessario. 
   * In **metodo di selezione account** selezionare il cluster usando uno dei due metodi seguenti: 
        * Selezionare il pulsante **di opzione dalla sottoscrizione di Azure** e selezionare l'account della **sottoscrizione di Azure** . Selezionare quindi il **cluster**. Si noti che nell'elenco a discesa vengono elencati solo i cluster che appartengono all'utente.
        * In alternativa, selezionare il pulsante di opzione **Immetti manualmente** e immettere l' **endpoint** (URL cluster).
    * Specificare il **tenant**.
    * Immettere l' **ID dell'entità servizio**. L'ID entità deve disporre delle autorizzazioni appropriate, in base al livello di autorizzazione richiesto dal comando in uso.
    * Selezionare il pulsante **chiave entità servizio** e immettere la **chiave dell'entità servizio**.
    * Selezionare il **database** dal menu a discesa. In alternativa, selezionare **modifica** casella di controllo e immettere il nome del database.
    * Selezionare **Test connessione** per testare la connessione al servizio collegato creata. Se è possibile connettersi alla configurazione, verrà visualizzata una connessione di segno di spunta verde con **esito positivo** .
    * Selezionare **fine** per completare la creazione del servizio collegato.

1. Dopo aver configurato un servizio collegato, in **AzureDataExplorerTable** > **Connection**aggiungere il nome della **tabella** . Selezionare **Anteprima dati**per assicurarsi che i dati vengano presentati correttamente.

   Il set di dati è ora pronto ed è possibile continuare a modificare la pipeline.

### <a name="add-a-query-to-your-lookup-activity"></a>Aggiungere una query all'attività di ricerca

1. In **pipeline-4-docs** > **Impostazioni** aggiungere una query nella casella di testo **query** , ad esempio:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Modificare le proprietà **timeout query** o **nessun troncamento** e **solo prima riga** , in base alle esigenze. In questo flusso, viene mantenuto il timeout predefinito per le **query** e vengono deselezionate le caselle di controllo. 

    ![Impostazioni finali dell'attività di ricerca](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Creare un'attività for-each 

L'attività [for-each](/azure/data-factory/control-flow-for-each-activity) viene utilizzata per scorrere una raccolta ed eseguire le attività specificate in un ciclo. 

1. A questo punto si aggiunge un'attività for-each alla pipeline. Questa attività elaborerà i dati restituiti dall'attività Lookup. 
    * Nel riquadro **attività** , in **iterazione & condizionali**, selezionare l'attività **foreach** e trascinarla e rilasciarla nell'area di disegno.
    * Tracciare una linea tra l'output dell'attività di ricerca e l'input dell'attività ForEach nell'area di disegno per connetterli.

        ![Attività ForEach](media/data-factory-command-activity/for-each-activity.png)

1.  Selezionare l'attività ForEach nell'area di disegno. Nella scheda **Impostazioni** seguente:
    * Controllare la casella di controllo **sequenziale** per un'elaborazione sequenziale dei risultati della ricerca o lasciarla deselezionata per creare l'elaborazione parallela.
    * Imposta il **numero di batch**.
    * In **elementi**specificare il riferimento seguente al valore di output: *@activity (' Lookup1'). output. valore*

       ![Impostazioni dell'attività ForEach](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Creare un'attività di comando di Azure Esplora dati nell'attività ForEach

1. Fare doppio clic sull'attività ForEach nell'area di disegno per aprirla in una nuova area di disegno per specificare le attività all'interno di ForEach.
1. Nel riquadro **attività** , in **Esplora dati di Azure**, selezionare l'attività del **comando Azure Esplora dati** e trascinarla e rilasciarla nell'area di disegno.

    ![Attività del comando di Esplora dati di Azure](media/data-factory-command-activity/adx-command-activity.png)

1.  Nella scheda **connessione** selezionare lo stesso servizio collegato creato in precedenza.

    ![scheda connessione attività del comando Esplora dati di Azure](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Nella scheda **comando** specificare il comando seguente:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    Il **comando** indica ad Azure Esplora dati di esportare i risultati di una query specificata in un archivio BLOB, in un formato compresso. Viene eseguito in modo asincrono (usando il modificatore Async).
    La query indirizza la colonna di database di ogni riga nel risultato dell'attività di ricerca. Il **timeout del comando** può essere lasciato invariato.

    ![attività Command](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > L'attività Command presenta i limiti seguenti:
    > * Limite dimensioni: dimensioni risposta 1 MB
    > * Limite di tempo: 20 minuti (impostazione predefinita), 1 ora (massimo).
    > * Se necessario, è possibile aggiungere una query al risultato usando [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands)per ridurre le dimensioni e il tempo risultanti.

1.  A questo punto la pipeline è pronta. È possibile tornare alla visualizzazione principale della pipeline facendo clic sul nome della pipeline.

    ![Pipeline di comandi di Azure Esplora dati](media/data-factory-command-activity/adx-command-pipeline.png)

1. Selezionare **debug** prima di pubblicare la pipeline. Lo stato della pipeline può essere monitorato nella scheda **output** .

    ![output dell'attività del comando Esplora dati di Azure](media/data-factory-command-activity/command-activity-output.png)

1. È possibile **pubblicare tutto** e quindi **aggiungere trigger** per eseguire la pipeline. 

## <a name="control-command-outputs"></a>Controllare gli output del comando

La struttura dell'output dell'attività del comando è descritta di seguito. Questo output può essere usato dall'attività successiva nella pipeline.

### <a name="returned-value-of-a-non-async-control-command"></a>Valore restituito di un comando di controllo non asincrono

In un comando di controllo non asincrono, la struttura del valore restituito è simile alla struttura del risultato dell'attività di ricerca. Il campo `count` indica il numero di record restituiti. Un campo di matrice fisso `value` contiene un elenco di record. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>Valore restituito di un comando di controllo asincrono

In un comando di controllo asincrono, l'attività esegue il polling della tabella delle operazioni dietro le quinte, fino a quando l'operazione asincrona non viene completata o si verifica il timeout. Il valore restituito conterrà pertanto il risultato di `.show operations OperationId` per la proprietà **operationId** specificata. Controllare i valori delle proprietà **stato** e **stato** per verificare il corretto completamento dell'operazione.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [copiare dati in Azure Esplora dati usando Azure Data Factory](data-factory-load-data.md).
* Informazioni sull'uso di [Azure Data Factory modello per la copia bulk dal database in Azure Esplora dati](data-factory-template.md).
