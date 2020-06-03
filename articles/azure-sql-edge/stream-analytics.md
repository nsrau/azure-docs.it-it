---
title: Uso di processi Edge di Analisi di flusso di Azure con SQL Edge di Azure (anteprima)
description: Informazioni sull'uso di processi di Analisi di flusso in SQL Edge di Azure (anteprima)
keywords: SQL Edge, analisi di flusso,
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7c15312b48e7118517894d8ffd4807e4892e03a3
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233151"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>Uso dei processi di Analisi di flusso di Azure con SQL Edge

SQL Edge di Azure (anteprima) è un motore di database relazionale ottimizzato progettato per distribuzioni di IoT ed Edge. È basato sulle versioni più recenti del motore di database di Microsoft SQL Server, che offre funzionalità leader del settore per prestazioni, sicurezza ed elaborazione delle query. Oltre alle funzionalità leader del settore per la gestione dei database relazionali di SQL Server, SQL Edge di Azure offre funzionalità di streaming predefinite per l'analisi in tempo reale e l'elaborazione di eventi complessa.

SQL Edge di Azure include un'implementazione nativa del runtime di analisi di flusso. Questa implementazione consente di creare un processo Edge di Analisi di flusso di Azure e di distribuire il processo come processo di flusso di SQL Edge. Per distribuire i processi di Analisi di flusso di Azure in SQL Edge, è possibile usare il parametro ASAJobInfo esposto tramite l'opzione `module twin's desired properties` del modulo SQL Edge:

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
| SqlPackage | URI dell'archiviazione BLOB di Azure per il file *.zip che contiene il pacchetto di applicazione livello dati del database SQL.
| ASAJobInfo | URI dell'archiviazione BLOB di Azure per il processo Edge di Analisi di flusso di Azure.

## <a name="create-an-azure-stream-analytics-edge-job"></a>Creare un processo Edge di Analisi di flusso di Azure

1. Accedere al portale di Azure.

2. Creare un nuovo processo **Analisi di flusso di Azure in IoT Edge**. Scegliere l'ambiente host con destinazione **Edge**.

3. Definire un input e un output per il processo di Analisi di flusso di Azure. Ogni output SQL che verrà configurato qui è associato a una singola tabella del database. Se è necessario trasmettere dati a più tabelle, è necessario creare più output del database SQL. È possibile configurare gli output SQL in modo che puntino a database diversi.

    **Input**. Scegliere EdgeHub come input del processo Edge e specificare le informazioni sulla risorsa.

    **Output**. Selezionare Database SQL come output. Selezionare **Specificare le impostazioni del database SQL manualmente**. Specificare i dettagli di configurazione per il database e la tabella.

    |Campo      | Descrizione |
    |---------------|-------------|
    |Alias di output | Nome dell'alias di output.|
    |Database | Nome del database SQL. Deve essere un nome valido di un database esistente nell'istanza di SQL Edge.|
    |Nome server | Dettagli relativi al nome (o indirizzo IP) e numero di porta dell'istanza di SQL. Come nome del server di una distribuzione di SQL Edge è possibile usare **tcp:.,1433**.|
    |Nome utente | Account di accesso SQL con accesso in lettura e scrittura dati al database specificato in precedenza.|
    |Password | Password dell'account di accesso SQL specificato in precedenza.|
    |Tabella | Nome della tabella che verrà restituita per il processo di streaming.|
    |Eredita schema di partizione| Consente di ereditare lo schema di partizione del passaggio o dell'input della query precedente. Quando questa opzione è abilitata, la velocità effettiva delle operazioni di scrittura su una tabella basata su disco risulta migliorata e il processo prevede una topologia completamente parallela.|
    |Dimensioni batch| Numero massimo di record inviati con ogni transazione di inserimento di massa.|

    Ecco una configurazione di input/output di esempio:

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
    > Per altre informazioni sull'adattatore di output SQL per Analisi di flusso di Azure, vedere [Output di Analisi di flusso di Azure in Database SQL di Azure](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Definire la query del processo di Analisi di flusso di Azure per il processo Edge. Questa query deve usare gli alias di input/output definiti come nomi di input e output nella query. Per altre informazioni, vedere [Informazioni di riferimento sul linguaggio di query per Analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Configurare le impostazioni dell'account di archiviazione per il processo Edge. L'account di archiviazione viene usato come destinazione di pubblicazione per il processo Edge.

6. In **Configura** selezionare **Pubblica** e quindi selezionare il pulsante **Pubblica**. Salvare l'URI della firma di accesso condiviso da usare con il modulo SQL Edge.

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>Distribuire il processo Edge di Analisi di flusso di Azure in SQL Edge

Per distribuire il processo di streaming nel modulo SQL Edge, aggiornare la configurazione del modulo SQL Edge in modo che includa l'URI della firma di accesso condiviso per il processo di streaming del passaggio precedente. Per aggiornare il modulo SQL Edge:

1. Nel portale di Azure passare alla distribuzione dell'hub IoT.

2. Nel riquadro a sinistra selezionare **IoT Edge**.

3. Nella pagina **IoT Edge** trovare e selezionare l'istanza di IoT Edge in cui è distribuito il modulo SQL Edge.

4. Nella pagina **Dispositivo IoT Edge** selezionare **Imposta moduli**.

5. Nella pagina **Imposta moduli** selezionare **Configura** per il modulo SQL Edge.

6. Nel riquadro **Moduli personalizzati IoT Edge** selezionare **Impostare le proprietà desiderate del modulo gemello**. Aggiornare le proprietà desiderate in modo da includere l'URI per l'opzione `ASAJobInfo`, come illustrato nell'esempio seguente.

    > [!NOTE]
    > L'URI della firma di accesso condiviso nel codice JSON seguente è solo un esempio. Sostituire l'URI con l'URI effettivo della distribuzione.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. Selezionare **Salva**.

8. Nella pagina **Imposta moduli** selezionare **Avanti**.

9. Nella pagina **Imposta moduli** selezionare **Avanti** e quindi **Invia**.

10. Dopo l'aggiornamento del modulo, il file del processo di Analisi di flusso viene scaricato, decompresso e distribuito nell'istanza di SQL Edge.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire SQL Edge tramite il portale di Azure](deploy-portal.md).

- [Trasmettere i dati](stream-data.md)

- [Machine Learning e intelligenza artificiale con ONNX in SQL Edge (anteprima)](onnx-overview.md)
