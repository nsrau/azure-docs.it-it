---
title: Partizionamento dell'output dei BLOB personalizzato in Analisi di flusso di Azure
description: Questo articolo descrive le funzionalità dei modelli di percorso di data/ora personalizzati e dei campi o attributi personalizzati per l'output di archiviazione BLOB dai processi di Analisi di flusso di Azure.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e06313cf83768421bedc6c7baddd30c2ef2e4846
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789412"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Partizionamento dell'output dei BLOB personalizzato in Analisi di flusso di Azure

Analisi di flusso di Azure supporta il partizionamento dell'output dei BLOB personalizzato con campi o attributi personalizzati o e modelli di percorso di data/ora personalizzati. 

## <a name="custom-field-or-attributes"></a>Campi o attributi personalizzati

Gli attributi o i campi personalizzati migliorano i flussi di lavoro di elaborazione e reporting downstream, consentendo un maggior controllo sull'output.

### <a name="partition-key-options"></a>Opzioni per la chiave di partizione

La chiave di partizione, o nome di colonna, usata per partizionare i dati di input può contenere caratteri alfanumerici con spazi, caratteri di sottolineatura e trattini. Non è possibile usare campi annidati come chiave di partizione, se non in combinazione con alias. La chiave di partizione deve essere nvarchar (max).

### <a name="example"></a>Esempio

Si supponga che un processo accetti dati di input da sessioni utente in tempo reale connesse a un servizio di videogiochi esterno, in cui i dati inseriti contengono una colonna **client_id** per identificare le sessioni. Per partizionare i dati per **client_id**, impostare il campo Modello percorso del BLOB in modo includere un token di partizione **{client_id}** nelle proprietà di output del BLOB quando si crea un processo. Man mano che i dati con vari valori **client_id** transitano attraverso il processo di Analisi di flusso, i dati di output vengono salvati in cartelle separate in base a un singolo valore **client_id** per ogni cartella.

![Modello di percorso con ID client](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Analogamente, se l'input del processo fossero dati provenienti da milioni di sensori, in cui ogni sensore ha un **sensor_id**, il modello di percorso sarebbe **{sensor_id}** per partizionare i dati di ogni sensore in cartelle diverse.  


Usando l'API REST, la sezione di output di un file JSON file usato per questa richiesta potrebbe simile al seguente:  

![Output dell'API REST](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Dopo l'avvio del processo, il contenitore *clients* potrebbe avere un aspetto simile al seguente:  

![Contenitore dei client](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Ogni cartella può contenere più BLOB, ognuno dei quali può contenere uno o più record. Nell'esempio precedente, è presente un singolo BLOB in una cartella con etichetta "06000000" con il contenuto seguente:

![Contenuto BLOB](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Si noti che ogni record nel BLOB ha una colonna **client_id** corrispondente al nome della cartella, perché la colonna usata per partizionare l'output nel percorso di output era **client_id**.

### <a name="limitations"></a>Limitazioni

1. È consentita solo una chiave di partizione personalizzata nella proprietà di output del BLOB Modello percorso. Tutti i modelli di percorso seguenti sono validi:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. Le chiavi di partizione non fanno distinzione tra maiuscole e minuscole, quindi, ad esempio, "Gianni" e "gianni" sono equivalenti. Inoltre, le espressioni non possono essere usate come chiavi di partizione. Ad esempio, **{columnA + columnB}** non funziona.  

3. Quando un flusso di input è costituito da record con una cardinalità della chiave di partizione inferiore a 8000, i record verranno aggiunti ai BLOB esistenti verranno creati nuovi BLOB solo quando è necessario. Se è la cardinalità è superiore a 8000 non esiste alcuna garanzia che i record vengano scritti nei BLOB esistenti e che non verranno creati nuovi BLOB per un numero arbitrario di record con la stessa chiave di partizione.

## <a name="custom-datetime-path-patterns"></a>Modelli di percorso di data/ora personalizzati

I modelli di percorso di data/ora personalizzati consentono di specificare un formato di output in linea con le convenzioni Streaming Hive, offrendo ad Analisi di flusso di Azure la possibilità di inviare i dati ad Azure HDInsight e Azure Databricks per l'elaborazione downstream. I modelli di percorso di data/ora personalizzati possono essere implementati facilmente usando la parola chiave `datetime` nel campo Prefisso percorso di output BLOB, con l'identificatore del formato. Ad esempio: `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Token supportati

I token di identificatore del formato seguenti possono essere usati da soli o in combinazione per ottenere formati di data/ora personalizzati:

|Identificatore di formato   |Descrizione   |Risultati per l'ora di esempio 2018-01-02T10:06:08|
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

### <a name="extensibility-and-restrictions"></a>Estendibilità e restrizioni

È possibile usare un numero illimitato di token `{datetime:<specifier>}` nel modello del percorso, fino a raggiungere il limite di caratteri per il prefisso del percorso. Gli identificatori di formato non possono essere combinati all'interno di un singolo token oltre le combinazioni già presenti negli elenchi a discesa di data e ora. 

Per una partizione di percorso `logs/MM/dd`:

|Espressione valida   |Espressione non valida   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

È possibile usare più volte lo stesso identificatore di formato nel prefisso del percorso. Il token deve essere ripetuto ogni volta.

### <a name="hive-streaming-conventions"></a>Convenzioni Streaming Hive

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
