---
title: Usare i comandi del controllo di Azure Data Explorer in Azure Data FactoryUse Azure Data Explorer control commands in Azure Data Factory
description: In this topic, use Azure Data Explorer control commands in Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264482"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Usare l'attività dei comandi di Azure Data Factory per eseguire i comandi di controllo di Azure Data ExplorerUse Azure Data Factory command activity to run Azure Data Explorer control commands

[Azure Data Factory](/azure/data-factory/) (ADF) è un servizio di integrazione dati basato su cloud che consente di eseguire una combinazione di attività sui dati. Utilizzare ADF per creare flussi di lavoro basati sui dati per l'orchestrazione e l'automazione degli spostamento dei dati e la trasformazione dei dati. L'attività Comando di **Esplora dati** di Azure in Azure Data Factory consente di eseguire comandi di controllo di Azure Data [Explorer](/azure/kusto/concepts/#control-commands) all'interno di un flusso di lavoro ADF. Questo articolo illustra come creare una pipeline con un'attività di ricerca e un'attività ForEach contenente un'attività di comando di Azure Data Explorer.This article teaches you how to create a pipeline with a lookup activity and ForEach activity containing an Azure Data Explorer command activity.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster e un database di Azure Data Explorer](create-cluster-database-portal.md)
* Un'origine dati.
* [Una data factory](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Creare una nuova pipeline

1. Selezionare lo strumento matita **Autore.** 
1. Creare una nuova **+** pipeline selezionando e quindi selezionare **Pipeline** dall'elenco a discesa.

   ![creare una nuova pipeline](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Creare un'attività di ricerca

Un'attività di ricerca può recuperare un set di dati da qualsiasi origine dati supportata da Azure Data Factory.A [lookup activity](/azure/data-factory/control-flow-lookup-activity) can retrieve a dataset from any Azure Data Factory-supported data sources. L'output dell'attività Lookup può essere utilizzato in un'attività ForEach o in un'altra attività.

1. Nel riquadro **Attività,** in **Generale,** selezionare l'attività **Ricerca.** Trascinalo e rilascialo nell'area di disegno principale a destra.
 
    ![selezionare l'attività di ricerca](media/data-factory-command-activity/select-activity.png)

1. L'area di disegno contiene ora l'attività Di ricerca creata. Utilizzare le schede sotto l'area di disegno per modificare i parametri pertinenti. In **Generale**rinominare l'attività. 

    ![modificare l'attività di ricerca](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Fare clic sull'area di disegno vuota per visualizzare le proprietà della pipeline. Utilizzare la scheda **Generale** per rinominare la pipeline. La nostra pipeline è denominata *pipeline-4-docs*.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Creare un set di dati di Azure Data Explorer nell'attività di ricercaCreate an Azure Data Explorer dataset in lookup activity

1. In **Impostazioni**selezionare il set di dati **dell'origine**di Azure Data Explorer creato in precedenzina oppure selezionare **Nuovo** per creare un nuovo set di dati.
 
    ![aggiungere dataset nelle impostazioni di ricercaAdd dataset in lookup settings](media/data-factory-command-activity/lookup-settings.png)

1. Selezionare il set di dati di **Azure Data Explorer (Kusto)** dalla finestra **Nuovo set di dati.** Selezionare **Continua** per aggiungere il nuovo set di dati.

   ![selezionare un nuovo set di dati](media/data-factory-command-activity/select-new-dataset.png) 

1. I nuovi parametri del set di dati di Azure Data Explorer sono visibili in **Impostazioni**. Per aggiornare i parametri, selezionare **Modifica**.

    ![impostazioni di ricerca con il set di dati di Azure Data ExplorerLookup settings with Azure Data Explorer dataset](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. La nuova scheda **AzureDataExplorerTable** viene aperta nell'area di disegno principale. 
    * Selezionare **Generale** e modificare il nome del set di dati. 
    * Selezionare **Connessione** per modificare le proprietà del set di dati. 
    * Selezionare il **servizio collegato** dall'elenco a discesa oppure selezionare **Nuovo** per creare un nuovo servizio collegato.

    ![Modificare le proprietà del set di dati di Azure Data Explorer](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Quando si crea un nuovo servizio collegato, viene visualizzata la pagina **Nuovo servizio collegato (Azure Data Explorer):When** creating a new linked service, the New Linked Service (Azure Data Explorer) page opens:

    ![ADX nuovo servizio collegato](media/data-factory-command-activity/adx-new-linked-service.png)

   * Selezionare Nome per il servizio collegato Azure Data **Explorer.Select Name** for Azure Data Explorer linked service. Se necessario, aggiungere **Descrizione.**
   * In **Connetti tramite runtime di integrazione**modificare le impostazioni correnti, se necessario. 
   * In Metodo di **selezione account** selezionare il cluster utilizzando uno dei due metodi seguenti: 
        * Selezionare il pulsante di opzione **Da sottoscrizione di Azure** e selezionare l'account della sottoscrizione di **Azure.Select** the From Azure subscription radio button and select your Azure subscription account. Selezionare quindi il **cluster**. Si noti che l'elenco a discesa elencherà solo i cluster che appartengono all'utente.
        * Selezionare invece **immettere manualmente** il pulsante di opzione e immettere l'endpoint (URL cluster). **Endpoint**
    * Specificare il **tenant**.
    * Immettere **l'ID entità servizio**. L'ID entità deve disporre delle autorizzazioni appropriate, in base al livello di autorizzazione richiesto dal comando in uso.
    * Selezionare il pulsante **Chiave entità servizio** e immettere La chiave **dell'entità servizio**.
    * Selezionare il **Database** dal menu a discesa. In alternativa, selezionare la casella di controllo **Modifica** e immettere il nome del database.
    * Selezionare **Test connessione** per testare la connessione al servizio collegato creata. Se è possibile connettersi alla configurazione, verrà visualizzato un segno di spunta verde **Connessione riuscita.**
    * Selezionare **Fine** per completare la creazione del servizio collegato.

1. Dopo aver configurato un servizio collegato, in **AzureDataExplorerTable** > **Connection**aggiungere il nome **della tabella.** Selezionare **Anteprima dati**per assicurarsi che i dati vengano presentati correttamente.

   Il set di dati è ora pronto ed è possibile continuare a modificare la pipeline.

### <a name="add-a-query-to-your-lookup-activity"></a>Aggiungere una query all'attività di ricerca

1. Nella pipeline-4-docs**Le impostazioni** aggiungono una query nella casella di testo **Query,** ad esempio:In **pipeline-4-docs** > Settings add a query in Query text box, for example:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Modificare le proprietà **Timeout query** o **Nessun troncamento** e **Primo solo riga,** in base alle esigenze. In questo flusso, manteniamo il **timeout di Query** predefinito e deseleziona le caselle di controllo. 

    ![Impostazioni finali dell'attività di ricerca](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Creare un'attività For-EachCreate a For-Each activity 

L'attività [For-Each](/azure/data-factory/control-flow-for-each-activity) viene utilizzata per scorrere una raccolta ed eseguire le attività specificate in un ciclo. 

1. A questo punto si aggiunge un'attività For-Each alla pipeline. Questa attività elaborerà i dati restituiti dall'attività Lookup. 
    * Nel riquadro **Attività,** in **Iterazione & Condizionali**, selezionare l'attività **ForEach** e trascinarla nell'area di disegno.
    * Disegnare una linea tra l'output dell'attività Lookup e l'input dell'attività ForEach nell'area di disegno per connetterli.

        ![Attività ForEach](media/data-factory-command-activity/for-each-activity.png)

1.  Selezionare l'attività ForEach nell'area di disegno. Nella scheda **Impostazioni** qui sotto:
    * Selezionare la casella di controllo **Sequenziale** per un'elaborazione sequenziale dei risultati della ricerca oppure lasciarla deselezionata per creare l'elaborazione parallela.
    * Impostare **Conteggio batch**.
    * In **Elementi**specificare il seguente riferimento al valore di output: * @activity('Lookup1').output.value*

       ![Impostazioni dell'attività ForEach](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Creare un'attività di comando di Esplora dati di Azure all'interno dell'attività ForEachCreate an Azure Data Explorer Command activity within the ForEach activity

1. Fare doppio clic sull'attività ForEach nell'area di disegno per aprirla in una nuova area di disegno per specificare le attività all'interno di ForEach.
1. Nel riquadro **Attività,** in Esplora dati di **Azure,** selezionare l'attività Comando di **Esplora dati** di Azure e trascinarla nell'area di disegno.

    ![Attività del comando di Esplora dati di AzureAzure Data Explorer command activity](media/data-factory-command-activity/adx-command-activity.png)

1.  Nella scheda **Connessione** selezionare lo stesso servizio collegato creato in precedenza.

    ![Azure Data Explorer comando scheda di connessione](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Nella scheda **Comando,** fornire il comando seguente:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    Il **comando** indica ad Esplora dati di Azure di esportare i risultati di una determinata query in un archivio BLOB, in un formato compresso. Viene eseguito in modo asincrono (utilizzando il modificatore async).
    La query indirizza la colonna di database di ogni riga nel risultato dell'attività di ricerca. Il **timeout del comando** può essere lasciato invariato.

    ![attività di comando](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > L'attività di comando ha i seguenti limiti:
    > * Limite di dimensione: 1 MB di risposta
    > * Limite di tempo: 20 minuti (predefinito), 1 ora (massimo).
    > * Se necessario, è possibile aggiungere una query al risultato utilizzando [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands), per ridurre le dimensioni/tempo risultanti.

1.  Ora la pipeline è pronta. È possibile tornare alla visualizzazione della pipeline principale facendo clic sul nome della pipeline.

    ![Pipeline dei comandi di Azure Data ExplorerAzure Data Explorer command pipeline](media/data-factory-command-activity/adx-command-pipeline.png)

1. Selezionare **Debug** prima di pubblicare la pipeline. Lo stato di avanzamento della pipeline può essere monitorato nella scheda **Output.The** pipeline progress can be monitored in the Output tab.

    ![Output dell'attività del comando Esplora dati azureAzure data explorer command activity output](media/data-factory-command-activity/command-activity-output.png)

1. È possibile **pubblicare tutto** e quindi **aggiungere trigger** per eseguire la pipeline. 

## <a name="control-command-outputs"></a>Controllare le uscite dei comandi

La struttura dell'output dell'attività di comando è descritta in dettaglio di seguito. Questo output può essere utilizzato dall'attività successiva nella pipeline.

### <a name="returned-value-of-a-non-async-control-command"></a>Valore restituito di un comando di controllo non asincronoReturned value of a non-async control command

In un comando di controllo non asincrono, la struttura del valore restituito è simile alla struttura del risultato dell'attività Lookup. Il `count` campo indica il numero di record restituiti. Un campo `value` di matrice fissa contiene un elenco di record. 

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
 
### <a name="returned-value-of-an-async-control-command"></a>Valore restituito di un comando di controllo asincronoReturned value of an async control command

In un comando di controllo asincrono, l'attività esegue il polling della tabella delle operazioni dietro le quinte, fino al completamento o al timeout dell'operazione asincrona. Pertanto, il valore restituito conterrà il risultato di `.show operations OperationId` per la data **OperationId** proprietà. Controllare i valori delle proprietà **State** e **Status** per verificare il corretto completamento dell'operazione.

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

* Informazioni su come copiare i [dati in Azure Data Explorer usando Azure Data Factory.](data-factory-load-data.md)
* Informazioni sull'uso del modello di Azure Data Factory per la [copia bulk dal database ad Azure Data Explorer.](data-factory-template.md)
