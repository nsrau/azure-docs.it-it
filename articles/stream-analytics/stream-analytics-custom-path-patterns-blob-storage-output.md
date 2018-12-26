---
title: Modelli di percorso datetime per l'output di BLOB di Analisi di flusso di Azure (anteprima)
description: Questo articolo descrive la funzione dei modelli di percorso datetime personalizzati per l'output di archiviazione BLOB dai processi di Analisi di flusso di Azure.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ba386539c3f3c6740b843575bbccd4b028b8a5a7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090788"
---
# <a name="custom-datetime-path-patterns-for-azure-stream-analytics-blob-storage-output-preview"></a>Modelli di percorso di data/ora personalizzati per l'output di archiviazione BLOB di Analisi di flusso di Azure (anteprima)

Analisi di flusso di Azure supporta identificatori personalizzati del formato di data e ora nel percorso del file per gli output di archiviazione BLOB. I modelli di percorso di data/ora personalizzati consentono di specificare un formato di output in linea con le convenzioni Streaming Hive, offrendo ad Analisi di flusso di Azure la possibilità di inviare i dati ad Azure HDInsight e Azure Databricks per l'elaborazione downstream. I modelli di percorso di data/ora personalizzati possono essere implementati facilmente usando la parola chiave `datetime` nel campo Prefisso percorso di output BLOB, con l'identificatore del formato. Ad esempio: `{datetime:yyyy}`.

Usare questo collegamento al [portale di Azure](https://portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true) per attivare o disattivare il flag della funzionalità che abilita i modelli di percorso di data/ora personalizzati per l'output dell'archiviazione BLOB (anteprima). Questa funzionalità verrà presto abilitata nel portale principale.

## <a name="supported-tokens"></a>Token supportati

I token di identificatore del formato seguenti possono essere usati da soli o in combinazione per ottenere formati di data/ora personalizzati:

|Identificatore di formato   |DESCRIZIONE   |Risultati per l'ora di esempio 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Anno come numero di quattro cifre|2018|
|{datetime:MM}|Mese da 01 a 12|01|
|{datetime:M}|Mese da 1 a 12|1|
|{datetime:dd}|Giorno da 01 a 31|02|
|{datetime:d}|Giorno da 1 a 12|2|
|{datetime:HH}|Ora in formato 24 ore, da 00 a 23|10|
|{datetime:mm}|Minuti da 00 a 24|06|
|{datetime:m}|Minuti da 0 a 24|6|
|{datetime:ss}|Secondi da 00 a 60|08|

Se si preferisce non usare modelli di data/ora personalizzati, è possibile aggiungere il token {date} e/o {time} al prefisso del percorso per generare un elenco a discesa con i formati di data/ora predefiniti.

![Formati di data/ora precedenti di Analisi di flusso](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

## <a name="extensibility-and-restrictions"></a>Estendibilità e restrizioni

È possibile usare un numero illimitato di token `{datetime:<specifier>}` nel modello del percorso, fino a raggiungere il limite di caratteri per il prefisso del percorso. Gli identificatori di formato non possono essere combinati all'interno di un singolo token oltre le combinazioni già presenti negli elenchi a discesa di data e ora. 

Per una partizione di percorso `logs/MM/dd`:

|Espressione valida   |Espressione non valida   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

È possibile usare più volte lo stesso identificatore di formato nel prefisso del percorso. Il token deve essere ripetuto ogni volta.

## <a name="hive-streaming-conventions"></a>Convenzioni Streaming Hive

I modelli di percorso personalizzati per l'archiviazione BLOB possono essere usati con la convenzione Streaming Hive, che prevede che le cartelle siano etichettate con `column=` nel nome della cartella.

Ad esempio: `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Grazie all'output personalizzato, non è più necessario modificare le tabelle e aggiungere manualmente le partizioni per trasferire i dati tra Analisi di flusso di Azure e Hive. È invece possibile aggiungere numerose cartelle automaticamente usando:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Esempio

Creare un account di archiviazione, un gruppo di risorse, un processo di Analisi di flusso e un'origine di input in base alla guida introduttiva [Creare un processo di Analisi di flusso di Azure tramite il portale di Azure](stream-analytics-quick-create-portal.md). Usare gli stessi dati di esempio usati nella guida introduttiva, disponibili anche in [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Creare un sink di output BLOB con la configurazione seguente:

![Creare un sink di output BLOB di Analisi di flusso](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Il modello di percorso completo è il seguente:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Quando si avvia il processo, una struttura di cartelle basata sul modello di percorso viene creata nel contenitore BLOB. È possibile eseguire il drill down a livello di giorno.

![Output BLOB di Analisi di flusso con un modello di percorso personalizzato](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sugli output di Analisi di flusso di Azure](stream-analytics-define-outputs.md)
