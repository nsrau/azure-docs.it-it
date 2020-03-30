---
title: Utilizzo di pacchetti di applicazione livello dati del database SQL e processi di analisi di flusso con L'edge del database SQL di Azure Documenti Microsoft
description: Informazioni sull'uso dei processi di Analisi di flusso in SQL Database Edge
keywords: bordo del database sql, analisi di flusso, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74384166"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Utilizzo di pacchetti di applicazione livello dati del database SQL e processi di Analisi di flusso con Edge del database SQLUsing SQL Database DAC packages and Stream Analytics jobs with SQL Database Edge

Azure SQL Database Edge Preview è un motore di database relazionale ottimizzato destinato alle distribuzioni IoT e Edge.Azure SQL Database Edge Preview is an optimized relational database engine is for IoT and edge deployments. Si basa sulle versioni più recenti del motore di database di Microsoft SQL ServerDatabase Engine, che offre funzionalità di elaborazione di query, sicurezza e prestazioni di progettazione leader del settore. Oltre alle funzionalità di gestione di database relazionali leader nel settore di SQL Server, Azure SQL Database Edge offre funzionalità di streaming integrate per l'analisi in tempo reale e l'elaborazione complessa degli eventi.

Azure SQL Database Edge fornisce anche un'implementazione nativa di SqlPackage.exe che consente di distribuire un pacchetto di applicazione livello dati [del database SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) durante la distribuzione di SQL Database Edge.

Azure SQL Database Edge espone due `module twin's desired properties` parametri facoltativi tramite l'opzione del modulo IoT Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Campo | Descrizione |
|------|-------------|
| SqlPackage | URI di archiviazione BLOB di Azure per il file con estensione zip che contiene il pacchetto di applicazione livello dati del database SQL.
| ASAJobInfo (Informazioni in base alle posizioni utente) | URI di archiviazione BLOB di Azure per il processo ASA Edge.Azure Blob storage URI for the ASA Edge job. Per ulteriori informazioni, vedere [Pubblicazione di un processo ASA Edge per SQL Database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Utilizzo di pacchetti di applicazione livello dati del database SQL con SQL Database Edge

Per utilizzare un pacchetto di applicazione livello dati del database SQL (con estensione dacpac) con SQL Database Edge, attenersi alla seguente procedura:

1. Creare o estrarre un pacchetto di applicazione livello dati del database SQL. Vedere [Estrazione di un'applicazione](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) livello dati da un database per informazioni su come generare un pacchetto di applicazione livello dati per un database di SQL Server esistente.

2. Comprimere il file con estensione dacpac e caricarlo in un account di archiviazione BLOB di Azure.zip the .dacpac and upload it to an Azure Blob storage account. Per altre informazioni sul caricamento di file nell'archivio BLOB di Azure, vedere Caricare, scaricare ed elencare BLOB con il portale di Azure.For more information on uploading files to Azure Blob storage, see [Upload, download, and list blobs with the Azure portal.](../storage/blobs/storage-quickstart-blobs-portal.md)

3. Generare una firma di accesso condiviso per il file zip tramite il portale di Azure.Generate a shared access signature for the zip file by using the Azure portal. Per ulteriori informazioni, vedere [Delegare l'accesso con firme](../storage/common/storage-sas-overview.md)di accesso condiviso.

4. Aggiornare la configurazione del modulo SQL Database Edge per includere l'URI di accesso condiviso per il pacchetto di applicazione livello dati. Per aggiornare il modulo SQL Database Edge, attenersi alla seguente procedura:

    1. Nel portale di Azure passare alla distribuzione dell'hub IoT.In the Azure portal, go to your IoT Hub deployment.

    2. Nel riquadro a sinistra selezionare **IoT Edge**.

    3. Nella pagina **Edge** Edge individuare e selezionare il bordo IoT in cui viene distribuito il modulo SQL Database Edge.

    4. Nella pagina **Dispositivo perimetrale IoT** selezionare **Imposta modulo**.

    5. Nella pagina **Imposta moduli** selezionare **Configura sul** modulo SQL Database Edge.

    6. Nel riquadro **Moduli personalizzati IoT Edge** selezionare Imposta proprietà **desiderate del modulo gemello.** Aggiornare le proprietà desiderate `SQLPackage` per includere l'URI per l'opzione, come illustrato nell'esempio seguente.

        > [!NOTE]
        > L'URI di chiamata nel codice JSON seguente è solo un esempio. Sostituire l'URI con l'URI effettivo della distribuzione.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Selezionare **Salva**.

    8. Nella pagina **Imposta moduli** selezionare **Avanti**.

    9. Nella pagina **Imposta moduli** selezionare **Avanti** e quindi **Invia**.

5. Dopo l'aggiornamento del modulo, il file del pacchetto di applicazione livello dati viene scaricato, decompresso e distribuito nell'istanza di SQL Database Edge.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Uso dei processi di streaming con SQL Database Edge

Azure SQL Database Edge ha un'implementazione nativa del runtime di analisi di flusso. Questa implementazione consente di creare un processo perimetrale di Analisi di flusso di Azure e di distribuirlo come processo di streaming di SQL Database Edge.This implementation enables you to create an Azure Stream Analytics edge job and deploy that job as a SQL Database Edge streaming job. Per creare un processo edge di Analisi di flusso, completare questi passaggi:To create a Stream Analytics edge job, complete these steps:

1. Passare al portale di Azure usando [l'URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)di anteprima. Questo URL di anteprima consente di configurare l'output del database SQL per un processo perimetrale di Analisi di flusso.

2. Creare un nuovo processo di Analisi di flusso di Azure in IoT Edge.Create a new **Azure Stream Analytics on IoT Edge** job. Scegliere l'ambiente di hosting destinato a **Edge**.

3. Definire un input e un output per il processo di Analisi di flusso di Azure.Define an input and output for the Azure Stream Analytics job. Ogni output SQL, che verrà impostato qui, è legato a una singola tabella nel database. Se è necessario eseguire il flusso dei dati in più tabelle, è necessario creare più output del database SQL. È possibile configurare gli output SQL in modo che puntino a database diversi.

    **Ingresso**. Scegliere EdgeHub come input per il processo perimetrale e fornire le informazioni sulla risorsa.

    **Uscita**. Selezionare Database SQL come output. Selezionare **Fornisci le impostazioni del database SQL manualmente**. Fornire i dettagli di configurazione per il database e la tabella.

    |Campo      | Descrizione |
    |---------------|-------------|
    |Alias di output | Nome dell'alias di output.|
    |Database | Nome del database SQL. Deve essere un nome valido di un database presente nell'istanza di SQL Database Edge.|
    |Nome server | Dettagli relativi al nome (o all'indirizzo IP) e al numero di porta per l'istanza SQL. Per una distribuzione di SQL Database Edge, è possibile usare **tcp:.,1433** come nome del server.|
    |Username | Account di accesso SQL con accesso del lettore dati e del writer dati al database specificato in precedenza.|
    |Password | Password per l'account di accesso SQL specificato in precedenza.|
    |Tabella | Nome della tabella che verrà restituita per il processo di streaming.|
    |Eredita partizionamento| Consente di ereditare lo schema di partizionamento del passaggio o dell'input della query precedente. Quando questa opzione è abilitata, è possibile prevedere una velocità effettiva migliore quando si scrive in una tabella basata su disco e si dispone di una topologia completamente parallela per il processo.|
    |Dimensioni batch| Numero massimo di record inviati con ogni transazione di inserimento bulk.|

    Ecco una configurazione di input/output di esempio:Here's a sample input/output configuration:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Per altre informazioni sull'adattatore di output SQL per Analisi di flusso di Azure, vedere Output di Analisi di flusso di Azure nel database SQL di Azure.For more information on the SQL output adapter for Azure Stream Analytics, see [Azure Stream Analytics output to Azure SQL Database.](../stream-analytics/stream-analytics-sql-output-perf.md)

4. Definire la query del processo ASA per il processo perimetrale. Questa query deve usare gli alias di input/output definiti come nomi di input e output nella query. Per ulteriori informazioni, vedere Informazioni di riferimento sul [linguaggio](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)di query di Analisi di flusso .

5. Configurare le impostazioni dell'account di archiviazione per il processo perimetrale. L'account di archiviazione viene usato come destinazione di pubblicazione per il processo perimetrale.

6. In **Configura**selezionare **Pubblica**e quindi fare clic sul pulsante **Pubblica.** Salvare l'URI di accesso condiviso da usare con il modulo SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Distribuire il processo Edge di Analisi di flusso in SQL Database Edge

Per distribuire il processo di streaming nel modulo SQL Database Edge, aggiornare la configurazione del modulo SQL Database Edge per includere l'URI di accesso condiviso per il processo di streaming nel passaggio precedente. Per aggiornare il modulo SQL Database Edge:

1. Nel portale di Azure passare alla distribuzione dell'hub IoT.In the Azure portal, go to your IoT Hub deployment.

2. Nel riquadro a sinistra selezionare **IoT Edge**.

3. Nella pagina **Edge** Edge individuare e selezionare il bordo IoT in cui viene distribuito il modulo SQL Database Edge.

4. Nella pagina **Dispositivo perimetrale IoT** selezionare **Imposta modulo**.

5. Nella pagina **Imposta moduli** selezionare **Configura sul** modulo SQL Database Edge.

6. Nel riquadro **Moduli personalizzati IoT Edge** selezionare Imposta proprietà **desiderate del modulo gemello.** Aggiornare le proprietà desiderate `ASAJobInfo` per includere l'URI per l'opzione, come illustrato nell'esempio seguente.

    > [!NOTE]
    > L'URI di chiamata nel codice JSON seguente è solo un esempio. Sostituire l'URI con l'URI effettivo della distribuzione.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Selezionare **Salva**.

8. Nella pagina **Imposta moduli** selezionare **Avanti**.

9. Nella pagina **Imposta moduli** selezionare **Avanti** e quindi **Invia**.

10. Dopo l'aggiornamento del modulo, il file del processo di analisi di flusso viene scaricato, decompresso e distribuito nell'istanza di SQL Database Edge.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate su prezzi e disponibilità, vedere [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Richiedere l'abilitazione di Azure SQL Database Edge per la sottoscrizione.
- Per iniziare, vedere [Distribuire SQL Database Edge tramite il portale](deploy-portal.md)di Azure.
