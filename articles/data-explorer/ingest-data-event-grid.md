---
title: 'Avvio rapido: Inserire BLOB di Azure in Esplora dati di Azure'
description: In questa guida introduttiva verrà illustrato come inviare dati dell'account di archiviazione a Esplora dati di Azure usando una sottoscrizione di Griglia di eventi.
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 1/30/2019
ms.openlocfilehash: 343baedb77eb5c8a250e2ab967332dc0905f5112
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743686"
---
# <a name="quickstart-ingest-azure-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Guida introduttiva: Inserire BLOB di Azure in Esplora dati di Azure tramite una sottoscrizione delle notifiche di Griglia di eventi

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Esplora dati di Azure offre l'inserimento continuo di dati (caricamento) dai BLOB scritti nei contenitori di BLOB. Questo risultato si ottiene impostando una sottoscrizione di [Griglia di eventi di Azure](/azure/event-grid/overview) per gli eventi di creazione di BLOB e instradando questi eventi a Kusto tramite Hub eventi. Per questa guida introduttiva, è necessario avere un account di archiviazione con una sottoscrizione di Griglia di eventi che invia le relative notifiche a Hub eventi. È quindi possibile creare una connessione dati di Griglia di eventi e verificare il flusso di dati attraverso l'intero sistema.

## <a name="prerequisites"></a>Prerequisiti

1. Se non si ha una sottoscrizione di Azure, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/)
1. [Un cluster e un database](create-cluster-database-portal.md)
1. [Un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)
1. [Hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Creare una sottoscrizione di Griglia di eventi nell'account di archiviazione

1. Nel portale di Azure passare all'account di archiviazione
1. Fare clic sulla scheda **Eventi** e quindi su **Sottoscrizione di eventi**

    ![Collegamento all'applicazione di query](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. Nella scheda **Di base** della finestra **Crea sottoscrizione di eventi** specificare i valori seguenti:

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | NOME | *test-grid-connection* | Il nome della griglia di eventi da creare.|
    | Schema di eventi | *Schema griglia di eventi* | Lo schema da usare per la griglia di eventi. |
    | Tipo di argomento | *Account di archiviazione* | Il tipo di argomento della griglia di eventi. |
    | Risorsa argomento | *gridteststorage* | nome dell'account di archiviazione. |
    | Esegui la sottoscrizione di tutti i tipi di eventi | *Deselezionare* | Non si ricevono notifiche per tutti gli eventi. |
    | Tipi di evento definiti | *Blob created* (BLOB creato) | Gli eventi specifici per cui ricevere notifiche. |
    | Tipo di endpoint | *Hub eventi* | Il tipo di endpoint a cui vengono inviati gli eventi. |
    | Endpoint | *test-hub* | Hub eventi creato. |
    | | |

1. Se si vuole tenere traccia dei file di uno specifico contenitore, selezionare la scheda **Funzionalità aggiuntive**. Impostare i filtri per le notifiche come segue:
    * Il campo **L'oggetto inizia con** è il prefisso di tipo *letterale* del contenitore di BLOB (poiché il criterio applicato è *startswith*, può includere più contenitori). I caratteri jolly non sono consentiti.
     È *necessario* impostarlo come segue: *`/blobServices/default/containers/`*[prefisso contenitore]
    * Il campo **L'oggetto termina con** è il suffisso di tipo *letterale* del BLOB. I caratteri jolly non sono consentiti.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Creare una tabella di destinazione in Esplora dati di Azure

Creare una tabella in Esplora dati di Azure a cui verranno inviati i dati da Hub eventi. La tabella viene creata nel cluster e nel database preparati in **Prerequisiti**.

1. Nel portale di Azure, in corrispondenza del cluster selezionare **Query**.

    ![Collegamento all'applicazione di query](media/ingest-data-event-hub/query-explorer-link.png)

1. Copiare il comando seguente nella finestra e selezionare **Esegui** per creare la tabella (TestTable) che riceverà i dati inseriti.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Esecuzione della creazione di query](media/ingest-data-event-grid/run-create-table.png)

1. Copiare il comando seguente nella finestra e selezionare **Esegui** per eseguire il mapping dei dati JSON in ingresso ai nomi di colonna e ai tipi di dati della tabella (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Creare una connessione dati di Griglia di eventi in Esplora dati di Azure

A questo punto è possibile connettersi a Griglia di eventi da Esplora dati di Azure per consentire lo streaming del flusso di dati dal contenitore di BLOB alla tabella di test.

1. Selezionare **Notifiche** sulla barra degli strumenti per verificare che la distribuzione dell'hub eventi abbia avuto esito positivo.

1. Nel cluster creato selezionare **Database** e quindi **TestDatabase**.

    ![Selezionare il database di test](media/ingest-data-event-hub/select-test-database.png)

1. Selezionare **Inserimento dati** e quindi **Aggiungi connessione dati**.

    ![Inserimento di dati](media/ingest-data-event-hub/data-ingestion-create.png)

1. Selezionare il tipo di connessione: **Archiviazione BLOB**.

1. Compilare il modulo con le informazioni seguenti e quindi fare clic su **Crea**.

    ![Connessione all'hub eventi](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Origine dati:

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Data connection name (Nome connessione dati) | *test-hub-connection* | Nome della connessione da creare in Esplora dati di Azure.|
    | Sottoscrizione dell'account di archiviazione | ID sottoscrizione | L'ID sottoscrizione in cui risiede l'account di archiviazione.|
    | Account di archiviazione | *gridteststorage* | Il nome dell'account di archiviazione creato in precedenza.|
    | Griglia di eventi | *test-grid-connection* | Il nome della griglia di eventi creata. |
    | Nome dell'hub eventi | *test-hub* | Hub eventi creato. Questo campo viene compilato automaticamente quando si seleziona una griglia di eventi. |
    | Gruppo di consumer | *test-group* | Gruppo di consumer definito nell'hub eventi creato. |
    | | |

    Tabella di destinazione:

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Tabella | *TestTable* | Tabella creata in **TestDatabase**. |
    | Formato dati | *JSON* | I formati supportati sono Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV e TXT. |
    | Mapping di colonne | *TestMapping* | Mapping creato in **TestDatabase** che esegue il mapping dei dati JSON in ingresso ai nomi di colonna e ai tipi di dati di **TestTable**.|
    | | |

## <a name="generate-sample-data"></a>Generare i dati di esempio

Dopo aver connesso Esplora dati di Azure e l'account di archiviazione, è possibile creare i dati di esempio e caricarli nell'archiviazione BLOB.

Verrà usato un piccolo script della shell che invia alcuni semplici comandi dell'interfaccia della riga di comando di Azure per interagire con le risorse di Archiviazione di Azure. Innanzitutto lo script crea un nuovo contenitore nell'account di archiviazione e poi carica un file esistente (come un BLOB) in tale contenitore. Crea quindi un elenco di tutti i BLOB inclusi nel contenitore. È possibile usare [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) per eseguire lo script direttamente nel portale.

Salvare i dati seguenti in un file e usarli con lo script seguente:

```Json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```bash
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

## <a name="review-the-data-flow"></a>Esaminare il flusso di dati

> [!NOTE]
> ADX prevede un criterio di aggregazione (invio in batch) per l'inserimento di dati, progettato per ottimizzare questo processo.
Per impostazione predefinita, il criterio viene impostato su 5 minuti.
Sarà possibile modificarlo in un secondo momento, se necessario. In questa guida introduttiva si può prevedere una latenza di alcuni minuti.

1. Nella griglia di eventi del portale di Azure si noterà un picco di attività durante l'esecuzione dell'app.

    ![Grafico della griglia di eventi](media/ingest-data-event-grid/event-grid-graph.png)

1. Per verificare il numero di messaggi arrivati al database fino a questo momento, eseguire la query seguente nel database di test.

    ```Kusto
    TestTable
    | count
    ```

1. Per visualizzare il contenuto dei messaggi, eseguire la query seguente nel database di test.

    ```Kusto
    TestTable
    ```

    Il set di risultati dovrebbe essere simile al seguente.

    ![Set di risultati dei messaggi](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di usare nuovamente la griglia di eventi, eliminare **test-hub-rg**, per evitare l'addebito di costi.

1. Nel portale di Azure selezionare **Gruppi di risorse** all'estrema sinistra e quindi selezionare il gruppo di risorse creato.  

    Se il menu a sinistra è compresso, selezionare ![pulsante Espandi](media/ingest-data-event-hub/expand.png) per espanderlo.

   ![Selezionare il gruppo di risorse da eliminare](media/ingest-data-event-hub/delete-resources-select.png)

1. In **test-resource-group** selezionare **Elimina gruppo di risorse**.

1. Nella nuova finestra digitare il nome del gruppo di risorse da eliminare (*test-hub-rg*) e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida introduttiva: Eseguire query sui dati in Esplora dati di Azure](web-query-data.md)
