---
title: Output di analisi di flusso di Azure
description: Questo articolo descrive le opzioni di output dei dati disponibili per analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/2/2020
ms.openlocfilehash: 022e2e25c96473f49468f2bd48e5ee997933baea
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348713"
---
# <a name="outputs-from-azure-stream-analytics"></a>Output di analisi di flusso di Azure

Un processo di Analisi di flusso di Azure è costituito da un input, una query e un output. Sono disponibili diversi tipi di output a cui è possibile inviare dati trasformati. Questo articolo elenca gli output di analisi di flusso supportati. Quando si progetta la query di Analisi di flusso, fare riferimento al nome dell'output usando la [clausola INTO](/stream-analytics-query/into-azure-stream-analytics). È possibile usare un singolo output per processo o più output per ogni processo di streaming (se necessario) aggiungendo più clausole INTO alla query.

Per creare, modificare e testare gli output dei processi di Analisi di flusso, è possibile usare il [portale di Azure](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), l'[API .NET](/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations), l'[API REST](/rest/api/streamanalytics/) e [Visual Studio](stream-analytics-quick-create-vs.md).

Alcuni tipi di output supportano il [partizionamento](#partitioning) e le [dimensioni batch dell'output](#output-batch-size) variano per ottimizzare la velocità effettiva. La tabella seguente illustra le funzionalità supportate per ogni tipo di output:

| Tipo di output | Partizionamento | Sicurezza | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|Sì|Utente Azure Active Directory </br> MSI|
|[Database SQL di Azure](sql-database-output.md)|Sì, facoltativo.|Autenticazione utente SQL </br> MSI (anteprima)|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|Sì|Autenticazione utente SQL|
|[Archiviazione BLOB e Azure Data Lake generazione 2](blob-storage-azure-data-lake-gen2-output.md)|Sì|MSI </br> Chiave di accesso|
|[Hub eventi di Azure](event-hubs-output.md)|Sì, è necessario impostare la colonna chiave di partizione nella configurazione di output.|Chiave di accesso|
|[Power BI](power-bi-output.md)|No|Utente Azure Active Directory </br> MSI|
|[Archivio tabelle di Azure](table-storage-output.md)|Sì|Chiave account|
|[Code del bus di servizio di Azure](service-bus-queues-output.md)|Sì|Chiave di accesso|
|[Argomenti del bus di servizio di Azure](service-bus-topics-output.md)|Sì|Chiave di accesso|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|Sì|Chiave di accesso|
|[Funzioni di Azure](azure-functions-output.md)|Sì|Chiave di accesso|

## <a name="partitioning"></a>Partizionamento

Analisi di flusso supporta le partizioni per tutti gli output tranne Power BI. Per ulteriori informazioni sulle chiavi di partizione e sul numero di writer di output, vedere l'articolo relativo al tipo di output specifico a cui si è interessati. Tutti gli articoli di output sono collegati nella sezione precedente.  

Inoltre, per un'ottimizzazione più avanzata delle partizioni, il numero di writer di output può essere controllato utilizzando una `INTO <partition count>` clausola (see [into](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) nella query, che può essere utile per ottenere una topologia di processo desiderata. Se l'adattatore di output non è partizionato, la mancanza di dati in una partizione di input comporta un ritardo fino alla quantità di tempo di arrivo in ritardo. In questi casi, l'output viene unito a un unico writer che può causare colli di bottiglia nella pipeline. Per altre informazioni sui criteri di arrivo in ritardo, vedere [Considerazioni sull'ordine degli eventi con Analisi di flusso di Azure](./stream-analytics-time-handling.md).

## <a name="output-batch-size"></a>Dimensione del batch di output

Tutti gli output supportano la suddivisione in batch, ma solo alcuni supportano in modo esplicito le dimensioni del batch. Analisi di flusso di Azure usa batch di dimensioni variabili per l'elaborazione degli eventi e la scrittura negli output. Il motore di Analisi di flusso non scrive generalmente un messaggio alla volta e usa i batch per migliorare l'efficienza. Quando la frequenza degli eventi in ingresso e in uscita è elevata, Analisi di flusso usa batch più grandi. Quando la frequenza in uscita è bassa, usa batch di dimensioni minori per mantenere bassa la latenza.

## <a name="parquet-output-batching-window-properties"></a>Proprietà della finestra batch di output parquet

Quando si usa la distribuzione di Azure Resource Manager modello o l'API REST, le due proprietà della finestra batch sono:

1. *timeWindow*

   Tempo massimo di attesa per batch. Il valore deve essere una stringa di TimeSpan. Ad esempio, "00:02:00" per due minuti. Al termine di questo periodo, il batch viene scritto nell'output anche se non è stato soddisfatto il requisito di righe minime. Il valore predefinito è 1 minuto e il valore massimo consentito è 2 ore. Se l'output del BLOB ha la frequenza del modello di percorso, il tempo di attesa non può essere superiore all'intervallo di tempo della partizione.

2. *sizeWindow*

   Numero minimo di righe per batch. Per parquet, ogni batch crea un nuovo file. Il valore predefinito corrente è 2000 righe e il valore massimo consentito è 10.000 righe.

Queste proprietà della finestra batch sono supportate solo dalla versione API **2017-04-01-Preview**. Di seguito è riportato un esempio di payload JSON per una chiamata API REST:

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>
> [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/