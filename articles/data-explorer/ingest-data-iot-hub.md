---
title: Inserire i dati dall'hub IoT in Azure Data Explorer
description: In this article, you learn how to ingest (load) data into Azure Data Explorer from IoT Hub.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 78455c90bab694b77a5e4a56d0b40518867d8d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188357"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>Inserire i dati dall'hub IoT in Azure Data Explorer 

> [!div class="op_single_selector"]
> * [Portale](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](data-connection-iot-hub-resource-manager.md)

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Azure Data Explorer offre l'inserimento (caricamento dei dati) dall'hub IoT, una piattaforma di streaming di Big Data e un servizio di inserimento IoT.Azure Data Explorer offers ingestion (data loading) from IoT Hub, a big data streaming platform and IoT ingestion service.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Creare [un cluster di test e un database](create-cluster-database-portal.md) con il nome del database *testdb*.
* [Un'app di esempio](https://github.com/Azure-Samples/azure-iot-samples-csharp) e documentazione per la simulazione di un dispositivo.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) per eseguire l'app di esempio.

## <a name="create-an-iot-hub"></a>Creare un hub IotCreate an Iot Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Registrare un dispositivo nell'hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Creare una tabella di destinazione in Esplora dati di Azure

A questo punto si crea una tabella in Azure Data Explorer a cui gli hub IoT invieranno i dati. Creare la tabella nel cluster e nel database di cui è stato eseguito il provisioning in [**Prerequisiti**](#prerequisites).

1. Nel portale di Azure passare al cluster e selezionare **Query**.

    ![Query ADX nel portale](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Copiare il comando seguente nella finestra e selezionare **Esegui** per creare la tabella (TestTable) che riceverà i dati inseriti.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Esecuzione della creazione di query](media/ingest-data-iot-hub/run-create-query.png)

1. Copiare il comando seguente nella finestra e selezionare **Esegui** per eseguire il mapping dei dati JSON in ingresso ai nomi di colonna e ai tipi di dati della tabella (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Connettere la tabella di Azure Data Explorer all'hub IoTConnect Azure Data Explorer table to IoT hub

A questo punto ci si connette all'hub IoT da Azure Data Explorer.Now you connect to the IoT Hub from Azure Data Explorer. Al termine della connessione, i dati che scorrono nell'hub iot vengono trasmessi alla [tabella di destinazione creata.](#create-a-target-table-in-azure-data-explorer)

1. Selezionare **Notifiche** sulla barra degli strumenti per verificare che la distribuzione dell'hub IoT sia stata eseguita correttamente.

1. Nel cluster creato selezionare **Database,** quindi selezionare il database creato **testdb**.
    
    ![Selezionare il database di test](media/ingest-data-iot-hub/select-database.png)

1. Selezionare **Inserimento dati** e **Aggiungi connessione dati**. Compilare quindi il modulo con le informazioni seguenti. Al termine, seleziona **Crea.**

    ![Connessione hub IoT](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Origine dati**:

    **Impostazione** | **Descrizione campo**
    |---|---|
    | Data connection name (Nome connessione dati) | Nome della connessione che si vuole creare in Azure Data Explorer
    | Hub IoT | Nome dell'hub IoT |
    | Criteri di accesso condiviso | Nome del criterio di accesso condiviso. Deve disporre delle autorizzazioni di lettura |
    | Gruppo di consumer |  Gruppo di consumer definito nell'endpoint incorporato dell'hub IoT |
    | Proprietà del sistema di eventi | Proprietà [del sistema di eventi dell'hub IoT](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages). Quando si aggiungono proprietà di sistema, [creare](/azure/kusto/management/create-table-command) o [aggiornare](/azure/kusto/management/alter-table-command) lo schema della tabella e il [mapping](/azure/kusto/management/mappings) per includere le proprietà selezionate. | | | 

    > [!NOTE]
    > In caso di [failover manuale,](/azure/iot-hub/iot-hub-ha-dr#manual-failover)è necessario ricreare la connessione dati.

    **Tabella di destinazione**:

    Sono disponibili due opzioni per il routing dei dati inseriti: *statico* e *dinamico*. 
    Per questo articolo viene usato il routing statico, in cui vengono specificati il nome della tabella, il formato dati e il mapping. Lasciare deselezionato **My data includes routing info** (I miei dati includono le informazioni di routing).

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Tabella | *TestTable* | Tabella creata in **testdb**. |
    | Formato dati | *Json* | I formati supportati sono Avro, CSV, JSON, MULTILINE JSON, PSV, SOHSV, SCSV, TSV, TSVE e TXT. |
    | Mapping di colonne | *TestMapping* | Il [mapping](/azure/kusto/management/mappings) creato in **testdb**, che esegue il mapping dei dati JSON in ingresso ai nomi di colonna e ai tipi di dati di **testdb**. Obbligatorio per JSON, MULTILINE JSON e AVRO e facoltativo per altri formati.|
    | | |

    > [!NOTE]
    > * Selezionare **My data includes routing info** (I miei dati includono le informazioni di routing) per usare il routing dinamico, in cui i dati includono le informazioni di routing necessarie come illustrato nei commenti dell'[app di esempio](https://github.com/Azure-Samples/event-hubs-dotnet-ingest). Se vengono impostate proprietà sia statiche che dinamiche, le proprietà dinamiche eseguono l'override di quelle statiche. 
    > * Vengono ingeriti solo gli eventi accodati dopo la creazione della connessione dati.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>Generare dati di esempio per il test

L'applicazione del dispositivo simulato si connette a un endpoint specifico del dispositivo nell'hub IoT e invia dati di telemetria simulati di temperatura e umidità.

1. Scaricare il progetto C# di esempio da https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip ed estrarre l'archivio ZIP.

1. In una finestra del terminale locale passare alla cartella radice del progetto C# di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\simulated-device**.

1. Aprire il file **SimulatedDevice.cs** in un editor di testo di propria scelta.

    Sostituire il valore `s_connectionString` della variabile con la stringa di connessione del dispositivo [da Registrare un dispositivo nell'hub IoT](#register-a-device-to-the-iot-hub). Salvare quindi le modifiche nel file **SimulatedDevice.cs**.

1. Nella finestra del terminale locale eseguire i comandi seguenti per installare i pacchetti necessari per l'applicazione del dispositivo simulato:

    ```cmd/sh
    dotnet restore
    ```

1. Nella finestra del terminale eseguire il comando seguente per compilare ed eseguire l'applicazione del dispositivo simulato:

    ```cmd/sh
    dotnet run
    ```

    La schermata seguente mostra l'output mentre l'applicazione del dispositivo simulato invia i dati di telemetria all'hub IoT:

    ![Eseguire il dispositivo simulato](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>Esaminare il flusso di dati

Con l'app che genera i dati, è ora possibile visualizzare il flusso di dati dall'hub IoT alla tabella del cluster.

1. Nel portale di Azure, nell'hub IoT, viene visualizzato il picco di attività mentre l'app è in esecuzione.

    ![Metriche di Hub IoT](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. Per verificare il numero di messaggi arrivati al database fino a questo momento, eseguire la query seguente nel database di test.

    ```Kusto
    TestTable
    | count
    ```

1. Per visualizzare il contenuto dei messaggi, eseguire la query seguente:

    ```Kusto
    TestTable
    ```

    Il set di risultati:
    
    ![Mostra i risultati dei dati ingeriti](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Esplora dati di Azure prevede un criterio di aggregazione (invio in batch) per l'inserimento di dati, progettato per ottimizzare il processo di inserimento. Il criterio è configurato su 5 minuti o 500 MB di dati, per impostazione predefinita, pertanto potrebbe verificarsi una latenza. Vedere [criteri di invio batch](/azure/kusto/concepts/batchingpolicy) per le opzioni di aggregazione. 
    > * Configurare la tabella per supportare lo streaming e rimuovere il ritardo nel tempo di risposta. Vedere [Politica di streaming](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di utilizzare nuovamente l'hub IoT, pulire **test-hub-rg**per evitare di incorrere in costi.

1. Nel portale di Azure selezionare **Gruppi di risorse** all'estrema sinistra e quindi selezionare il gruppo di risorse creato.  

    Se il menu a sinistra è compresso, selezionare ![pulsante Espandi](media/ingest-data-event-hub/expand.png) per espanderlo.

   ![Selezionare il gruppo di risorse da eliminare](media/ingest-data-event-hub/delete-resources-select.png)

1. In **test-resource-group** selezionare **Elimina gruppo di risorse**.

1. Nella nuova finestra digitare il nome del gruppo di risorse da eliminare (*test-hub-rg*) e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

* [Query data in Azure Data Explorer](web-query-data.md)
