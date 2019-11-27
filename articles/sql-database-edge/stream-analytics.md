---
title: Uso dei pacchetti DAC del database SQL e dei processi di analisi di flusso con il database SQL di Azure Edge | Microsoft Docs
description: Informazioni sull'uso dei processi di analisi di flusso nel database SQL Edge
keywords: Edge database SQL, analisi di flusso, SqlPackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384166"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Uso dei pacchetti DAC del database SQL e dei processi di analisi di flusso con il database SQL Edge

L'anteprima Edge del database SQL di Azure è un motore di database relazionale ottimizzato per le distribuzioni di Azure e delle cose. Si basa sulle versioni più recenti del Microsoft SQL Server motore di database, che offre funzionalità leader del settore per le prestazioni, la sicurezza e l'elaborazione delle query. Insieme alle funzionalità leader del settore per la gestione dei database relazionali di SQL Server, Edge database SQL di Azure offre funzionalità di streaming integrate per l'analisi in tempo reale e l'elaborazione di eventi complessi.

Il database SQL di Azure Edge fornisce anche un'implementazione nativa di SqlPackage. exe che consente di distribuire un pacchetto di [applicazione livello dati del database SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) durante la distribuzione del perimetro del database SQL.

Edge database SQL di Azure espone due parametri facoltativi tramite l'opzione `module twin's desired properties` del modulo IoT Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Campo | DESCRIZIONE |
|------|-------------|
| SqlPackage | URI dell'archiviazione BLOB di Azure per il file *. zip che contiene il pacchetto di applicazione livello dati del database SQL.
| ASAJobInfo | URI dell'archiviazione BLOB di Azure per il processo di Edge ASA. Per altre informazioni, vedere [pubblicazione di un processo Edge ASA per il database SQL Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Utilizzo di pacchetti DAC del database SQL con Edge database SQL

Per usare un pacchetto di applicazione livello dati del database SQL (*. dacpac) con il database SQL Edge, seguire questa procedura:

1. Creare o estrarre un pacchetto di applicazione livello dati del database SQL. Per informazioni su come generare un pacchetto DAC per un database di SQL Server esistente, vedere [estrazione di un'applicazione livello dati da un database](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) .

2. Comprimere il file *. dacpac e caricarlo in un account di archiviazione BLOB di Azure. Per altre informazioni sul caricamento di file nell'archiviazione BLOB di Azure, vedere [caricare, scaricare ed elencare BLOB con il portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Generare una firma di accesso condiviso per il file zip usando il portale di Azure. Per altre informazioni, vedere [delega dell'accesso con le firme di accesso condiviso (SAS)](../storage/common/storage-sas-overview.md).

4. Aggiornare la configurazione del modulo perimetrale del database SQL per includere l'URI di accesso condiviso per il pacchetto di applicazione livello dati. Per aggiornare il modulo perimetrale del database SQL, seguire questa procedura:

    1. Nella portale di Azure passare alla distribuzione dell'hub Internet.

    2. Nel riquadro a sinistra selezionare **IoT Edge**.

    3. Nella pagina **IOT Edge** individuare e selezionare il bordo del dispositivo in cui è distribuito il modulo perimetrale del database SQL.

    4. Nella pagina dispositivo **IOT Edge** dispositivo selezionare **Imposta modulo**.

    5. Nella pagina **set Modules** selezionare **Configure** to the SQL database Edge Module.

    6. Nel riquadro **IOT Edge moduli personalizzati** selezionare Imposta le **proprietà desiderate del modulo gemello**. Aggiornare le proprietà desiderate per includere l'URI per l'opzione `SQLPackage`, come illustrato nell'esempio seguente.

        > [!NOTE]
        > L'URI SAS nel codice JSON seguente è solo un esempio. Sostituire l'URI con l'URI effettivo dalla distribuzione.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Selezionare **Salva**.

    8. Nella pagina **imposta moduli** selezionare **Avanti**.

    9. Nella pagina **set Modules** selezionare **Next** e quindi **Submit**.

5. Dopo l'aggiornamento del modulo, il file del pacchetto di applicazione livello dati viene scaricato, decompresso e distribuito nell'istanza perimetrale del database SQL.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Uso dei processi di streaming con il database SQL Edge

Il database SQL di Azure Edge ha un'implementazione nativa del runtime di analisi di flusso. Questa implementazione consente di creare un processo Edge di analisi di flusso di Azure e di distribuire il processo come processo di streaming perimetrale del database SQL. Per creare un processo Edge di analisi di flusso, completare i passaggi seguenti:

1. Passare alla portale di Azure usando l' [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)di anteprima. Questo URL di anteprima consente di configurare l'output del database SQL per un processo Edge di analisi di flusso.

2. Creare un nuovo processo di **analisi di flusso di Azure in IOT Edge** . Scegliere l'ambiente host che ha come destinazione **Edge**.

3. Definire un input e un output per il processo di analisi di flusso di Azure. Ogni output SQL, che verrà configurato qui, è associato a una singola tabella del database. Se è necessario trasmettere dati a più tabelle, è necessario creare più output del database SQL. È possibile configurare gli output SQL in modo che puntino a database diversi.

    **Input**. Scegliere EdgeHub come input per il processo Edge e specificare le informazioni sulla risorsa.

    **Output**. Selezionare database SQL come output. Selezionare **specificare manualmente le impostazioni del database SQL**. Specificare i dettagli di configurazione per il database e la tabella.

    |Campo      | DESCRIZIONE |
    |---------------|-------------|
    |Alias di output | Nome dell'alias di output.|
    |Database | Nome del database SQL. Deve essere un nome valido di un database esistente nell'istanza perimetrale del database SQL.|
    |Nome server | Nome (o indirizzo IP) e dettagli sul numero di porta per l'istanza di SQL. Per una distribuzione perimetrale del database SQL, è possibile utilizzare **TCP:., 1433** per il nome del server.|
    |Nome utente | Account di accesso SQL con Reader e writer di dati che accedono al database specificato in precedenza.|
    |Password | Password per l'account di accesso SQL specificato in precedenza.|
    |tabella | Nome della tabella che verrà restituita per il processo di streaming.|
    |Eredita partizionamento| Consente di ereditare lo schema di partizionamento del passaggio o dell'input della query precedente. Quando questa opzione è abilitata, è possibile prevedere una migliore velocità effettiva quando si scrive in una tabella basata su disco e si dispone di una topologia completamente parallela per il processo.|
    |Dimensioni batch| Numero massimo di record inviati con ogni transazione BULK INSERT.|

    Ecco una configurazione di esempio di input/output:

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
    > Per altre informazioni sull'adattatore di output SQL per analisi di flusso di Azure, vedere [output di analisi di flusso di Azure nel database SQL di Azure](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Definire la query del processo ASA per il processo Edge. Questa query deve usare gli alias di input/output definiti come nomi di input e output nella query. Per altre informazioni, vedere [riferimento al linguaggio di query di analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Impostare le impostazioni dell'account di archiviazione per il processo Edge. L'account di archiviazione viene usato come destinazione di pubblicazione per il processo Edge.

6. In **Configura**selezionare **pubblica**, quindi selezionare il pulsante **pubblica** . Salvare l'URI SAS per l'uso con il modulo perimetrale del database SQL.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Distribuire il processo Edge di analisi di flusso al perimetro del database SQL

Per distribuire il processo di streaming nel modulo perimetrale del database SQL, aggiornare la configurazione del modulo perimetrale del database SQL in modo da includere l'URI di firma di accesso condiviso per il processo di streaming dal passaggio precedente. Per aggiornare il modulo perimetrale del database SQL:

1. Nella portale di Azure passare alla distribuzione dell'hub Internet.

2. Nel riquadro a sinistra selezionare **IoT Edge**.

3. Nella pagina **IOT Edge** individuare e selezionare il bordo del dispositivo in cui è distribuito il modulo perimetrale del database SQL.

4. Nella pagina dispositivo **IOT Edge** dispositivo selezionare **Imposta modulo**.

5. Nella pagina **set Modules** selezionare **Configure** to the SQL database Edge Module.

6. Nel riquadro **IOT Edge moduli personalizzati** selezionare Imposta le **proprietà desiderate del modulo gemello**. Aggiornare le proprietà desiderate per includere l'URI per l'opzione `ASAJobInfo`, come illustrato nell'esempio seguente.

    > [!NOTE]
    > L'URI SAS nel codice JSON seguente è solo un esempio. Sostituire l'URI con l'URI effettivo dalla distribuzione.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Selezionare **Salva**.

8. Nella pagina **imposta moduli** selezionare **Avanti**.

9. Nella pagina **set Modules** selezionare **Next** e quindi **Submit**.

10. Dopo l'aggiornamento del modulo, il file del processo di analisi di flusso viene scaricato, decompresso e distribuito nell'istanza perimetrale del database SQL.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui prezzi e sulla disponibilità, vedere [Edge del database SQL di Azure](https://azure.microsoft.com/services/sql-database-edge/).
- Richiedere l'abilitazione del perimetro del database SQL di Azure per la sottoscrizione.
- Per iniziare, vedere la pagina relativa alla [distribuzione di Edge per database SQL tramite portale di Azure](deploy-portal.md).
