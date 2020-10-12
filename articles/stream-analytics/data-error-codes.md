---
title: Codici errore dati - Analisi di flusso di Azure
description: Risolvere i problemi di Analisi di flusso di Azure con i codici errore dati.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 56d7527eebb91bd09895f6cd0238721574df1015
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86037325"
---
# <a name="azure-stream-analytics-data-error-codes"></a>Codici di errore dei dati di analisi di flusso di Azure

È possibile usare log attività e log risorse per facilitare il debug di comportamenti imprevisti del processo di Analisi di flusso di Azure. Questo articolo riporta la descrizione di ogni codice errore dati. Gli errori dati si verificano quando nel flusso sono presenti dati non validi, ad esempio uno schema di record imprevisto.

## <a name="inputdeserializationerror"></a>InputDeserializationError

* **Causa**: Si è verificato un errore durante la deserializzazione dei dati di input.

## <a name="inputeventtimestampnotfound"></a>InputEventTimestampNotFound

* **Causa**: Analisi di flusso di Azure non riesce a ottenere un timestamp per la risorsa. 

## <a name="inputeventtimestampbyovervaluenotfound"></a>InputEventTimestampByOverValueNotFound

* **Causa**: Analisi di flusso di Azure non è in grado di ottenere il valore di `TIMESTAMP BY OVER COLUMN`.

## <a name="inputeventlatebeyondthreshold"></a>InputEventLateBeyondThreshold

* **Causa**: Un evento di input è stato inviato successivamente alla tolleranza configurata.

## <a name="inputeventearlybeyondthreshold"></a>InputEventEarlyBeyondThreshold

* **Causa**: L'ora di arrivo di un evento di input è precedente alla soglia del timestamp relativo all'applicazione dell'evento di input.

## <a name="azurefunctionmessagesizeexceeded"></a>AzureFunctionMessageSizeExceeded

* **Causa**: L'output del messaggio inviato a Funzioni di Azure supera il limite relativo alle dimensioni.

## <a name="eventhuboutputrecordexceedssizelimit"></a>EventHubOutputRecordExceedsSizeLimit

* **Causa**: Un record di output supera il limite di dimensioni massime durante la scrittura in Hub eventi.

## <a name="cosmosdboutputinvalidid"></a>CosmosDBOutputInvalidId

* **Causa**: Il valore o il tipo di una determinata colonna non è valido.
* **Raccomandazione**: Fornire stringhe univoche non vuote che non superino il limite di 255 caratteri.

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **Causa**: L'ID Documento del record di output contiene un carattere non valido.

## <a name="cosmosdboutputmissingid"></a>CosmosDBOutputMissingId

* **Causa**: Il record di output non contiene la colonna \[id] da usare come proprietà della chiave primaria.

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **Causa**: Il record di output non contiene la proprietà ID Documento. 
* **Raccomandazione**: Verificare che nella colonna dell'output della query sia presente una stringa univoca non vuota inferiore a 255 caratteri.

## <a name="cosmosdboutputmissingpartitionkey"></a>CosmosDBOutputMissingPartitionKey

* **Causa**: Nel record di output manca la colonna da usare come proprietà della chiave di partizione.

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **Causa**: Un record scritto in Cosmos DB è troppo grande.

## <a name="sqldatabaseoutputdataerror"></a>SQLDatabaseOutputDataError

* **Causa**: Analisi di flusso di Azure non riesce a scrivere eventi nel database SQL a causa di problemi nei dati.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi delle connessioni di input](stream-analytics-troubleshoot-input.md)
* [Risolvere i problemi degli output di Analisi di flusso di Azure](stream-analytics-troubleshoot-output.md)
* [Risolvere i problemi delle query di Analisi di flusso di Azure](stream-analytics-troubleshoot-query.md)
* [Risolvere i problemi di Analisi di flusso di Azure usando i log delle risorse](stream-analytics-job-diagnostic-logs.md)
* [Errori dei dati di Analisi di flusso di Azure](data-errors.md)
