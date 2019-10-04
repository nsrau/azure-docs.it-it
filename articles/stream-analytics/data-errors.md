---
title: Errori di dati di Azure Stream Analitica log di diagnostica
description: Questo articolo illustra i diversi input e gli errori di output dei dati che possono verificarsi quando si usa Azure Stream Analitica.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: ecc7077bf208adf1ac89adcce2f2e480ce34888e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329590"
---
# <a name="azure-stream-analytics-data-errors"></a>Errori di Azure Stream Analitica dei dati

Dati gli errori che si verificano durante l'elaborazione di dati.  In genere, questi errori si verificano durante la serializzazione deserializzazione dei dati, la serializzazione e le operazioni di scrittura.  Quando si verificano errori di dati, Stream Analitica scrive informazioni dettagliate e gli eventi di esempio per i log di diagnostica.  In alcuni casi, viene fornito anche riepilogo di queste informazioni tramite le notifiche del portale.

Questo articolo illustra i diversi tipi di errore, cause e i dettagli di log di diagnostica degli errori di dati di input e output.

## <a name="diagnostic-log-schema"></a>Schema di log di diagnostica

Visualizzare [risolvere i problemi di Analitica Stream di Azure usando i log di diagnostica](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) per visualizzare lo schema per i log di diagnostica. Il codice JSON seguente è un valore di esempio per la **proprietà** campo di un log di diagnostica per un errore nei dati.

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

## <a name="input-data-errors"></a>Errori di input dei dati

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Causa: Il tipo di compressione di input selezionato non corrisponde ai dati.
* Nelle notifiche del portale fornite: Yes
* Livello di log di diagnostica: Avviso
* Impatto: I messaggi con eventuali errori di deserializzazione incluso il tipo di compressione non valide vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. Per l'Hub eventi, l'identificatore è il PartitionId, Offset e numero di sequenza.

**Messaggio di errore**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Causa: L'intestazione di dati di input non è valido. Ad esempio, un file CSV include colonne con nomi duplicati.
* Nelle notifiche del portale fornite: Yes
* Livello di log di diagnostica: Avviso
* Impatto: I messaggi con eventuali errori di deserializzazione dell'inclusione dell'intestazione non validi vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Causa: Le colonne di input definite con CREATE TABLE o tramite TIMESTAMP BY non esiste.
* Nelle notifiche del portale fornite: Yes
* Livello di log di diagnostica: Avviso
* Impatto: Gli eventi con le colonne mancanti vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. 
   * Nomi delle colonne che non sono presenti. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggi di errore**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Causa: Impossibile convertire l'input nel tipo specificato nell'istruzione CREATE TABLE.
* Nelle notifiche del portale fornite: Yes
* Livello di log di diagnostica: Avviso
* Impatto: Gli eventi con errore di conversione del tipo vengono eliminati dall'input.
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

* Causa: I dati di input non sono nel formato corretto. Ad esempio, l'input non è un oggetto JSON valido.
* Nelle notifiche del portale fornite: Yes
* Livello di log di diagnostica: Avviso
* Impatto: Tutti gli eventi nel messaggio dopo che è stato rilevato un errore di dati non validi vengono eliminati dall'input.
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

* Causa: Il valore dell'espressione TIMESTAMP BY non può essere convertito in datetime.
* Nelle notifiche del portale fornite: Yes
* Livello di log di diagnostica: Avviso
* Impatto: Gli eventi con timestamp non valido di input vengono eliminati dall'input.
* Dettagli del log
   * Identificatore del messaggio di input. 
   * Messaggio di errore. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Causa: Il valore di TIMESTAMP BY OVER timestampColumn è NULL.
* Nelle notifiche del portale fornite: Yes
* Livello di log di diagnostica: Avviso
* Impatto: Gli eventi con timestamp non valido di input chiave vengono eliminati dall'input.
* Dettagli del log
   * Il payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Causa: La differenza tra ora di arrivo e tempo applicazione è maggiore di finestra di tolleranza per arrivo in ritardo.
* Nelle notifiche del portale fornite: No
* Livello di log di diagnostica: Informazioni
* Impatto:  Ultimi eventi di input vengono gestiti in base alla "Gestire gli altri eventi" impostazione dell'evento ordinamento sezione della configurazione del processo. Per altre informazioni, vedere [i criteri di gestione ora](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Dettagli del log
   * Tempo applicazione e l'ora di arrivo. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Causa: La differenza tra ora di arrivo e tempo applicazione è maggiore di 5 minuti.
* Nelle notifiche del portale fornite: No
* Livello di log di diagnostica: Informazioni
* Impatto:  Eventi di input anticipati vengono gestiti in base alla "Gestire gli altri eventi" impostazione dell'evento ordinamento sezione della configurazione del processo. Per altre informazioni, vedere [i criteri di gestione ora](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Dettagli del log
   * Tempo applicazione e l'ora di arrivo. 
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Causa: Evento è considerato ordinato in base alla finestra di tolleranza definita.
* Nelle notifiche del portale fornite: No
* Livello di log di diagnostica: Informazioni
* Impatto:  Non in ordine vengono gestiti gli eventi in base a "Gestire gli altri eventi" impostazione dell'evento sezione ordinazioni della configurazione del processo. Per altre informazioni, vedere [i criteri di gestione ora](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Dettagli del log
   * Payload effettivo fino a pochi kilobyte.

**Messaggio di errore**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Errori di output dei dati

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Causa: La colonna necessaria per l'output non esiste. Ad esempio, esiste una colonna definita come does't PartitionKey tabella di Azure.
* Nelle notifiche del portale fornite: Yes
* Livello di log di diagnostica: Avviso
* Impatto:  Tutti errori di conversione dati output tra cui Manca la colonna obbligatoria vengono gestiti in base al [criteri di dati di Output](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) impostazione.
* Dettagli del log
   * Nome della colonna e l'identificatore del record o parte del record.

**Messaggio di errore**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Causa: Il valore della colonna non sono conformi con l'output. Ad esempio, il nome della colonna non è una colonna della tabella di Azure valida.
* Nelle notifiche del portale fornite: Yes
* Livello di log di diagnostica: Avviso
* Impatto:  Tutti errori di conversione dati output incluso il nome di colonna non valido vengono gestiti in base al [criteri di dati di Output](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) impostazione.
* Dettagli del log
   * Nome della colonna e l'identificatore del record o parte del record.

**Messaggio di errore**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Causa: Una colonna non può essere convertita in un tipo valido nell'output. Ad esempio, il valore della colonna è incompatibile con i vincoli o tipo definito nella tabella SQL.
* Nelle notifiche del portale fornite: Yes
* Livello di log di diagnostica: Avviso
* Impatto:  Tutti errori di conversione dati output inclusi errore di conversione del tipo vengono gestiti in base al [criteri di dati di Output](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) impostazione.
* Dettagli del log
   * Nome della colonna.
   * Identificatore del record o parte del record.

**Messaggio di errore**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Causa: Il valore del messaggio è maggiore delle dimensioni di output supportati. Ad esempio, un record è superiore a 1 MB per un output di Hub eventi.
* Nelle notifiche del portale fornite: Yes
* Livello di log di diagnostica: Avviso
* Impatto:  Tutti errori di conversione dati output tra cui è stato superato il limite di record vengono gestiti in base al [criteri di dati di Output](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) impostazione.
* Dettagli del log
   * Identificatore del record o parte del record.

**Messaggio di errore**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Causa: Un record contiene già una colonna con lo stesso nome di una colonna di sistema. Output di COSMOS DB con una colonna, ad esempio, denominato ID quando colonna ID per una colonna diversa.
* Nelle notifiche del portale fornite: Yes
* Livello di log di diagnostica: Avviso
* Impatto:  Tutti errori di conversione dati output, tra cui chiave duplicati vengono gestiti in base al [criteri di dati di Output](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) impostazione.
* Dettagli del log
   * Nome della colonna.
   * Identificatore del record o parte del record.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di Analitica Stream di Azure usando i log di diagnostica](stream-analytics-job-diagnostic-logs.md)

* [Comprendere il monitoraggio di processo di Stream Analitica e su come monitorare le query](stream-analytics-monitoring.md)
