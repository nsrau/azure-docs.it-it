---
title: Uso del pacchetto DAC di SQL database e dei processi di analisi di flusso con il database SQL di Azure Edge | Microsoft Docs
description: Informazioni sull'uso dei processi di analisi di flusso nel database SQL Edge
keywords: Edge database SQL, analisi di flusso, SqlPackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: c3ed84e06f693925ed8b484070616e223929e401
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108751"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>Uso del pacchetto di applicazione livello dati SQL database e del processo di analisi di flusso con Edge database SQL

L'anteprima Edge del database SQL di Azure è un motore di database relazionale ottimizzato per le distribuzioni di Azure e delle cose. Si basa sulle versioni più recenti del Microsoft SQL Server motore di database, che offre funzionalità leader del settore per le prestazioni, la sicurezza e l'elaborazione delle query. Insieme alle funzionalità leader del settore per la gestione dei database relazionali di SQL Server, Edge database SQL di Azure offre funzionalità di streaming integrate per l'analisi in tempo reale e l'elaborazione di eventi complessi.

Il database SQL di Azure Edge fornisce anche un'implementazione nativa di SqlPackage. exe che consente agli utenti di distribuire un pacchetto di [applicazione livello dati del database SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) durante la distribuzione del perimetro del database SQL.

Edge del database SQL di Azure espone due parametri facoltativi tramite l'opzione delle *proprietà desiderate del modulo gemello* del modulo IOT Edge.

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
| SQLPackage | URI dell'archiviazione BLOB di Azure per il file *. zip contenente il pacchetto di applicazione livello dati del database SQL.
| ASAJobInfo | URI dell'archiviazione BLOB di Azure per il processo di Edge ASA. Per altre informazioni sulla pubblicazione del processo Edge ASA, vedere [pubblicazione di un processo Edge ASA per il database SQL Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Utilizzo di pacchetti DAC del database SQL con Edge database SQL

Per usare un pacchetto di applicazione livello dati del database SQL (*. dacpac) con il database SQL Edge, attenersi alla procedura indicata di seguito.

1. Creare o estrarre un pacchetto di applicazione livello dati del database SQL. È possibile utilizzare i concetti indicati in [estrazione dell'applicazione livello dati da un database esistente](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) per generare un DacPac per un database SQL esistente.

2. Eseguire il file con*estensione *. dacpac* e caricarlo in un account di archiviazione BLOB di Azure. Per altre informazioni sul caricamento di file nell'archiviazione BLOB di Azure, vedere [caricare, scaricare ed elencare BLOB con il portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Generare una firma SAS per il file zip usando portale di Azure. Per altre informazioni, vedere [delega dell'accesso con le firme di accesso condiviso (SAS)](../storage/common/storage-sas-overview.md).

4. Aggiornare la configurazione del modulo Edge del database SQL per includere l'URI di firma di accesso condiviso per il pacchetto di applicazione livello dati. Per aggiornare il modulo perimetrale del database SQL

    1. Nella portale di Azure passare alla distribuzione dell'hub Internet.

    2. Nel riquadro sinistro fare clic su **IOT Edge**.

    3. Nella pagina **IOT Edge** trovare e fare clic sul IOT Edge in cui è distribuito il modulo perimetrale del database SQL.

    4. Nella pagina dispositivo *IOT Edge* dispositivo fare clic su **Imposta modulo**. 

    5. Nella pagina **imposta moduli** fare clic su *Configura* in base al modulo perimetrale del database SQL. 

    6. Nel riquadro **IOT Edge moduli personalizzati** selezionare le *proprietà desiderate del dispositivo gemello set Modules* e quindi aggiornare le proprietà desiderate in modo da includere l'URI per l'opzione SqlPackage, come illustrato nell'esempio riportato di seguito. 

        > [!NOTE]
        > L'URI di firma di accesso condiviso riportato di seguito è a solo illustrazione. Sostituire l'URI con l'URI effettivo dalla distribuzione.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Fare clic su **Save**.

    8. Nella pagina **imposta moduli** fare clic su *Avanti*.

    9. Nella pagina **set Modules** fare clic su *Next* e quindi su **Submit**.

5. Pubblicare l'aggiornamento del modulo, il file DACPAC viene scaricato, decompresso e distribuito nell'istanza perimetrale del database SQL.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Uso dei processi di streaming con il database SQL Edge

Il database SQL di Azure Edge ha un'implementazione nativa del runtime di analisi di flusso. Ciò consente agli utenti di creare un processo Edge di analisi di flusso di Azure e di distribuire il processo come processo di streaming perimetrale del database SQL. Per creare un processo Edge di analisi di flusso, seguire questa procedura.

1. Passare al portale di Azure usando l' [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)di anteprima. Questo URL di anteprima consente agli utenti di configurare l'output del database SQL per un processo Edge di analisi di flusso.

2. Creare un nuovo processo di **analisi di flusso di Azure in IOT Edge** e scegliere l'ambiente host di destinazione **Edge**.

3. Definire l' *input* e l' *output* per il processo di analisi di flusso di Azure. Ogni output SQL (configurato di seguito) è associato a una singola tabella all'interno del database. Se è necessario trasmettere dati a più tabelle, è necessario creare più output del database SQL. Gli output SQL possono essere configurati in modo da puntare a database diversi.

    *Input: scegliere EdgeHub come input per il processo Edge e immettere le informazioni sulla risorsa.*

    *Output: selezionare database SQL come output, "specificare manualmente le impostazioni del database SQL" e specificare i dettagli di configurazione per il database e la tabella.*

    |Campo      | DESCRIZIONE |
    |---------------|-------------|
    |Alias di output | Nome dell'alias di output.|
    |Database | Nome del database SQL. Questo deve essere un nome di database valido, presente nell'istanza perimetrale del database SQL.|
    |Nome server | Nome (o indirizzo IP) e dettagli sul numero di porta per l'istanza di SQL. Per una distribuzione perimetrale del database SQL, è possibile utilizzare **TCP:., 1433** come nome del server.|
    |Nome utente | Account di accesso SQL con Reader e writer di dati che accedono al database menzionato in precedenza.|
    |Password | Password per l'account di accesso SQL menzionato in precedenza.|
    |tabella | Nome della tabella che verrà restituita per il processo di streaming.|
    |Eredita partizionamento| Questa opzione di configurazione dell'output SQL consente di ereditare lo schema di partizionamento del passaggio o dell'input della query precedente. Con questa funzionalità abilitata, la scrittura in una tabella basata su disco e la presenza di una topologia completamente parallela per il processo, prevede una migliore velocità effettiva.|
    |Dimensioni batch| Dimensioni batch è il numero massimo di record inviati con ogni transazione BULK INSERT.|

    Esempio di configurazione di input/output:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
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

6. In Configura selezionare pubblica, quindi fare clic sul pulsante pubblica. Salvare l'URL SAS per l'uso con il modulo perimetrale del database SQL.

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>Distribuire il processo Edge di analisi di flusso al perimetro del database SQL

Per distribuire il processo di streaming nel modulo perimetrale del database SQL, aggiornare la configurazione del modulo perimetrale del database SQL in modo da includere l'URI di firma di accesso condiviso per il processo di streaming dal passaggio precedente. Per aggiornare il modulo perimetrale del database SQL

1. Nella portale di Azure passare alla distribuzione dell'hub Internet.

2. Nel riquadro sinistro fare clic su **IOT Edge**.

3. Nella pagina **IOT Edge** trovare e fare clic sul IOT Edge in cui è distribuito il modulo perimetrale del database SQL.

4. Nella pagina dispositivo *IOT Edge* dispositivo fare clic su **Imposta modulo**. 

5. Nella pagina **imposta moduli** fare clic su *Configura* in base al modulo perimetrale del database SQL. 

6. Nel riquadro **IOT Edge moduli personalizzati** selezionare le *proprietà desiderate del dispositivo gemello set Modules* e quindi aggiornare le proprietà desiderate in modo da includere l'URI per l'opzione ASAJobInfo, come illustrato nell'esempio riportato di seguito. 

    > [!NOTE]
    > L'URI di firma di accesso condiviso riportato di seguito è a solo illustrazione. Sostituire l'URI con l'URI effettivo dalla distribuzione.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Fare clic su **Save**.

8. Nella pagina **imposta moduli** fare clic su *Avanti*.

9. Nella pagina **set Modules** fare clic su *Next* e quindi su **Submit**.

10. Pubblicare l'aggiornamento del modulo, il file del processo di analisi di flusso viene scaricato, decompresso e distribuito nell'istanza perimetrale del database SQL.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui prezzi e sulla disponibilità, vedere [Edge del database SQL di Azure](https://azure.microsoft.com/services/sql-database-edge/).
- Richiedere l'abilitazione del perimetro del database SQL di Azure per la sottoscrizione.
- Per iniziare, vedere gli argomenti seguenti:
  - [Distribuire Edge del database SQL tramite portale di Azure](deploy-portal.md)
