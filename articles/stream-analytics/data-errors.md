---
title: Errori dati del log delle risorse di analisi di flusso di Azure
description: Questo articolo illustra i diversi errori di dati di input e output che possono verificarsi quando si usa analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 1c649499fd9eaedac0ca4ff9c182e13a9da223ef
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053151"
---
# <a name="azure-stream-analytics-data-errors"></a>Errori dei dati di Analisi di flusso di Azure

Gli errori relativi ai dati sono errori che si verificano durante l'elaborazione dei dati.  Questi errori si verificano più spesso durante le operazioni di deserializzazione, serializzazione e scrittura dei dati.  Quando si verificano errori di dati, analisi di flusso scrive informazioni dettagliate ed eventi di esempio nei log delle risorse. Per ottenere questi dettagli aggiuntivi, abilitare i log di diagnostica nel processo. In alcuni casi, un riepilogo di queste informazioni viene fornito anche tramite le notifiche del portale.

Questo articolo descrive i diversi tipi di errore, le cause e i dettagli del log delle risorse per gli errori relativi ai dati di input e di output.

## <a name="resource-logs-schema"></a>Schema dei log delle risorse

Vedere [risolvere i problemi di analisi di flusso di Azure usando i log di diagnostica](stream-analytics-job-diagnostic-logs.md#resource-logs-schema) per visualizzare lo schema per i log delle risorse. Il codice JSON seguente è un valore di esempio per il campo delle **Proprietà** di un log delle risorse per un errore di dati.

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

* Motivo: il tipo di compressione di input selezionato non corrisponde ai dati.
* Notifica del portale fornita: Sì
* Livello di log delle risorse: avviso
* Impact: i messaggi con eventuali errori di deserializzazione, incluso il tipo di compressione non valido, vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. Per l'hub eventi, l'identificatore è PartitionId, offset e numero di sequenza.

**Messaggio di errore**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Motivo: l'intestazione dei dati di input non è valida. Ad esempio, un file CSV contiene colonne con nomi duplicati.
* Notifica del portale fornita: Sì
* Livello di log delle risorse: avviso
* Impact: i messaggi con eventuali errori di deserializzazione, incluse le intestazioni non valide, vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Cause: le colonne di input definite con CREATE TABLE o tramite TIMESTAMP BY non esistono.
* Notifica del portale fornita: Sì
* Livello di log delle risorse: avviso
* Impact: gli eventi con colonne mancanti vengono eliminati dall'input.
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

* Cause: non è possibile convertire l'input nel tipo specificato nell'istruzione CREATE TABLE.
* Notifica del portale fornita: Sì
* Livello di log delle risorse: avviso
* Impact: gli eventi con errore di conversione del tipo vengono eliminati dall'input.
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

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError. InvalidData

* Motivo: i dati di input non sono nel formato corretto. Ad esempio, l'input non è un JSON valido.
* Notifica del portale fornita: Sì
* Livello di log delle risorse: avviso
* Impact: tutti gli eventi nel messaggio dopo un errore di dati non valido sono stati rilasciati dall'input.
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

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Motivo: il valore dell'espressione TIMESTAMP BY non può essere convertito in DateTime.
* Notifica del portale fornita: Sì
* Livello di log delle risorse: avviso
* Impact: gli eventi con timestamp di input non valido vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. 
   * Messaggio di errore. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Cause: il valore di TIMESTAMP BY OVER timestampColumn è NULL.
* Notifica del portale fornita: Sì
* Livello di log delle risorse: avviso
* Impact: gli eventi con chiave timestamp di input non valida vengono eliminati dall'input.
* Dettagli del log
   * Il payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Motivo: la differenza tra il tempo applicazione e l'ora di arrivo è maggiore della finestra di tolleranza per arrivo in ritardo.
* Notifica del portale fornita: No
* Livello di log delle risorse: informazioni
* Impact: gli eventi di input tardivi vengono gestiti in base all'impostazione "Gestisci altri eventi" nella sezione relativa all'ordinamento degli eventi della configurazione del processo. Per altre informazioni, vedere [criteri di gestione del tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Dettagli del log
   * Tempo applicazione e ora di arrivo. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Motivo: la differenza tra il tempo applicazione e l'ora di arrivo è maggiore di 5 minuti.
* Notifica del portale fornita: No
* Livello di log delle risorse: informazioni
* Impact: gli eventi di input iniziali vengono gestiti in base all'impostazione "Gestisci altri eventi" nella sezione relativa all'ordinamento degli eventi della configurazione del processo. Per altre informazioni, vedere [criteri di gestione del tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Dettagli del log
   * Tempo applicazione e ora di arrivo. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Motivo: l'evento viene considerato non in ordine in base alla finestra di tolleranza per elementi non in ordine definita.
* Notifica del portale fornita: No
* Livello di log delle risorse: informazioni
* Impact: gli eventi non in ordine vengono gestiti in base all'impostazione "Gestisci altri eventi" nella sezione relativa all'ordinamento degli eventi della configurazione del processo. Per altre informazioni, vedere [criteri di gestione del tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Dettagli del log
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Errori dati di output

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Motivo: la colonna necessaria per l'output non esiste. Ad esempio, è presente una colonna definita come tabella di Azure PartitionKey richiesto non.
* Notifica del portale fornita: Sì
* Livello di log delle risorse: avviso
* Impact: tutti gli errori di conversione dei dati di output, inclusa la colonna mancante obbligatoria, vengono gestiti in base all'impostazione dei [criteri di output](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Dettagli del log
   * Nome della colonna e identificatore del record o parte del record.

**Messaggio di errore**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Motivo: il valore della colonna non è conforme all'output. Ad esempio, il nome della colonna non è una colonna di tabella di Azure valida.
* Notifica del portale fornita: Sì
* Livello di log delle risorse: avviso
* Impact: tutti gli errori di conversione dei dati di output, incluso il nome di colonna non valido, vengono gestiti in base all'impostazione dei [criteri di output](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Dettagli del log
   * Nome della colonna e identificatore del record o parte del record.

**Messaggio di errore**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Motivo: una colonna non può essere convertita in un tipo valido nell'output. Il valore della colonna, ad esempio, non è compatibile con i vincoli o il tipo definito nella tabella SQL.
* Notifica del portale fornita: Sì
* Livello di log delle risorse: avviso
* Impact: tutti gli errori di conversione dei dati di output, incluso l'errore di conversione del tipo, vengono gestiti in base all'impostazione dei [criteri di output](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Dettagli del log
   * Nome della colonna.
   * Identificatore del record o parte del record.

**Messaggio di errore**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Motivo: il valore del messaggio è maggiore della dimensione di output supportata. Ad esempio, un record è più grande di 1 MB per un output di hub eventi.
* Notifica del portale fornita: Sì
* Livello di log delle risorse: avviso
* Impact: tutti gli errori di conversione dei dati di output, incluso il limite massimo delle dimensioni del record, vengono gestiti in base all'impostazione dei [criteri di output](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Dettagli del log
   * Identificatore del record o parte del record.

**Messaggio di errore**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Motivo: un record contiene già una colonna con lo stesso nome di una colonna di sistema. Ad esempio, CosmosDB output con una colonna denominata ID quando la colonna ID corrisponde a una colonna diversa.
* Notifica del portale fornita: Sì
* Livello di log delle risorse: avviso
* Impact: tutti gli errori di conversione dei dati di output, inclusa la chiave duplicata, vengono gestiti in base all'impostazione dei [criteri di output](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Dettagli del log
   * Nome della colonna.
   * Identificatore del record o parte del record.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di Analisi di flusso di Azure mediante i log di diagnostica](stream-analytics-job-diagnostic-logs.md)

* [Informazioni sul monitoraggio dei processi di Analisi di flusso e su come monitorare le query](stream-analytics-monitoring.md)
