---
title: Errori dei dati del log di diagnostica di Analisi di flusso di AzureAzure Stream Analytics diagnostic log data errors
description: Questo articolo illustra i diversi errori dei dati di input e output che possono verificarsi quando si usa Analisi di flusso di Azure.This article explains the different input and output data errors that can occur when using Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 5457308d577b95201fa31bfad0a6634a7a79eda3
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398122"
---
# <a name="azure-stream-analytics-data-errors"></a>Errori dei dati di Analisi di flusso di AzureAzure Stream Analytics data errors

Gli errori di dati sono errori che si verificano durante l'elaborazione dei dati.  Questi errori si verificano più spesso durante la deserializzazione dei dati, la serializzazione e le operazioni di scrittura.  Quando si verificano errori di dati, Analisi di flusso scrive informazioni dettagliate ed eventi di esempio nei log di diagnostica.  In alcuni casi, il riepilogo di queste informazioni viene fornito anche tramite le notifiche del portale.

In questo articolo vengono descritti i diversi tipi di errore, le cause e i dettagli del log di diagnostica per gli errori dei dati di input e output.

## <a name="diagnostic-log-schema"></a>Schema dei log di diagnostica

Vedere [Risolvere i problemi di Analisi di flusso di Azure usando i log](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) di diagnostica per visualizzare lo schema per i log di diagnostica. Il codice JSON seguente è un valore di esempio per il campo **Proprietà** di un log di diagnostica per un errore di dati.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Errori dei dati di input

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Causa: il tipo di compressione di input selezionato non corrisponde ai dati.
* Notifica del portale fornita: Sì
* Livello del registro di diagnostica: Avviso
* Impatto: i messaggi con eventuali errori di deserializzazione, incluso il tipo di compressione non valido, vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. Per l'hub eventi, l'identificatore è PartitionId, Offset e Numero sequenza.

**Messaggio di errore**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Causa: l'intestazione dei dati di input non è valida. Ad esempio, un file CSV ha colonne con nomi duplicati.
* Notifica del portale fornita: Sì
* Livello del registro di diagnostica: Avviso
* Impatto: i messaggi con eventuali errori di deserializzazione, inclusa l'intestazione non valida, vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Causa: le colonne di input definite con CREATE TABLE o tramite TIMESTAMP BY non esistono.
* Notifica del portale fornita: Sì
* Livello del registro di diagnostica: Avviso
* Impatto: gli eventi con colonne mancanti vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. 
   * Nomi delle colonne mancanti. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggi di errore**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Causa: impossibile convertire l'input nel tipo specificato nell'istruzione CREATE TABLE.
* Notifica del portale fornita: Sì
* Livello del registro di diagnostica: Avviso
* Impatto: gli eventi con errore di conversione del tipo vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. 
   * Nome della colonna e tipo previsto.

**Messaggi di errore**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Causa: i dati di input non sono nel formato corretto. Ad esempio, l'input non è JSON valido.
* Notifica del portale fornita: Sì
* Livello del registro di diagnostica: Avviso
* Impatto: tutti gli eventi nel messaggio dopo che si è verificato un errore di dati non validi vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggi di errore**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp (Schema NonInputTimeStamp)

* Causa: il valore dell'espressione TIMESTAMP BY non può essere convertito in datetime.
* Notifica del portale fornita: Sì
* Livello del registro di diagnostica: Avviso
* Impatto: gli eventi con timestamp di input non valido vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. 
   * Messaggio di errore. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey (Chiave NonInputTimeStampKey)

* Causa: il valore di TIMESTAMP BY OVER timestampColumn è NULL.
* Notifica del portale fornita: Sì
* Livello del registro di diagnostica: Avviso
* Impatto: gli eventi con chiave timestamp di input non valida vengono eliminati dall'input.
* Dettagli del log
   * Il payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>Evento LateInput

* Causa: la differenza tra l'ora di applicazione e l'ora di arrivo è maggiore della finestra di tolleranza all'arrivo in ritardo.
* Notifica del portale fornita: No
* Livello di log di diagnostica: Informazioni
* Impatto: gli eventi di input in ritardo vengono gestiti in base all'impostazione "Gestisci altri eventi" nella sezione Ordinamento eventi della configurazione del processo. Per ulteriori informazioni, vedere [Criteri di gestione del tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Dettagli del log
   * Ora di applicazione e ora di arrivo. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent (Evento Input anticipato)

* Causa: la differenza tra l'ora di applicazione e l'ora di arrivo è maggiore di 5 minuti.
* Notifica del portale fornita: No
* Livello di log di diagnostica: Informazioni
* Impatto: gli eventi di input anticipati vengono gestiti in base all'impostazione "Gestisci altri eventi" nella sezione Ordinamento eventi della configurazione del processo. Per ulteriori informazioni, vedere [Criteri di gestione del tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Dettagli del log
   * Ora di applicazione e ora di arrivo. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>Evento OutOfOrder

* Causa: l'evento viene considerato non in ordine in base alla finestra di tolleranza fuori ordine definita.
* Notifica del portale fornita: No
* Livello di log di diagnostica: Informazioni
* Impatto: gli eventi fuori ordine vengono gestiti in base all'impostazione "Gestisci altri eventi" nella sezione Ordine eventi della configurazione del processo. Per ulteriori informazioni, vedere [Criteri di gestione del tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Dettagli del log
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Errori dei dati di output

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Causa: la colonna richiesta per l'output non esiste. Ad esempio, una colonna definita come Azure Table PartitionKey non esiste.
* Notifica del portale fornita: Sì
* Livello del registro di diagnostica: Avviso
* Impatto: tutti gli errori di conversione dei dati di output, inclusa la colonna obbligatoria mancante, vengono gestiti in base all'impostazione Criterio dati di [output.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Dettagli del log
   * Nome della colonna e identificatore del record o parte del record.

**Messaggio di errore**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Causa: il valore della colonna non è conforme all'output. Ad esempio, il nome della colonna non è una colonna di tabella di Azure valida.
* Notifica del portale fornita: Sì
* Livello del registro di diagnostica: Avviso
* Impatto: tutti gli errori di conversione dei dati di output, incluso il nome di colonna non valido, vengono gestiti in base all'impostazione Criterio dati di [output.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Dettagli del log
   * Nome della colonna e identificatore di record o parte del record.

**Messaggio di errore**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Causa: una colonna non può essere convertita in un tipo valido nell'output. Ad esempio, il valore di column non è compatibile con i vincoli o il tipo definito nella tabella SQL.
* Notifica del portale fornita: Sì
* Livello del registro di diagnostica: Avviso
* Impatto: tutti gli errori di conversione dei dati di output, incluso l'errore di conversione dei tipi, vengono gestiti in base all'impostazione Criterio dati di [output.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Dettagli del log
   * Nome della colonna.
   * Identificatore di record o parte del record.

**Messaggio di errore**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Causa: il valore del messaggio è maggiore della dimensione di output supportata. Ad esempio, un record è maggiore di 1 MB per un output dell'hub eventi.
* Notifica del portale fornita: Sì
* Livello del registro di diagnostica: Avviso
* Impatto: tutti gli errori di conversione dei dati di output, incluso il limite di dimensioni superato dai record, vengono gestiti in base all'impostazione Criterio dati di [output.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Dettagli del log
   * Identificatore di record o parte del record.

**Messaggio di errore**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Causa: un record contiene già una colonna con lo stesso nome di una colonna di sistema. Ad esempio, l'output CosmosDB con una colonna denominata ID quando la colonna ID è a una colonna diversa.
* Notifica del portale fornita: Sì
* Livello del registro di diagnostica: Avviso
* Impatto: tutti gli errori di conversione dei dati di output, inclusa la chiave duplicata, vengono gestiti in base all'impostazione Criterio dati di [output.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Dettagli del log
   * Nome della colonna.
   * Identificatore di record o parte del record.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di Analisi di flusso di Azure mediante i log di diagnostica](stream-analytics-job-diagnostic-logs.md)

* [Informazioni sul monitoraggio dei processi di Analisi di flusso e su come monitorare le query](stream-analytics-monitoring.md)
