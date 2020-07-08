---
title: Codici errore esterni - Analisi di flusso di Azure
description: Risolvere i problemi di Analisi di flusso di Azure con i codici errore esterni.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: f963f4c3538146b7f9929960092a18294659be5d
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045247"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Codici errore esterni di Analisi di flusso di Azure

È possibile usare log attività e log risorse per facilitare il debug di comportamenti imprevisti del processo di Analisi di flusso di Azure. Questo articolo riporta la descrizione di ogni codice errore esterno. Gli errori esterni sono errori generici generati da un servizio upstream o downstream che Analisi di flusso di Azure non è in grado di identificare come un errore di dati, un errore di configurazione o un errore di disponibilità esterna.

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **Causa**: Si è verificato un errore durante l'inizializzazione di un adattatore.

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **Causa**: Si è verificato un errore durante la scrittura di dati in un adattatore.

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **Causa**: Un errore HTTP è stato restituito da Funzioni di Azure.

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **Causa**: Analisi di flusso non è riuscito a scrivere gli eventi in Funzioni di Azure.

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **Causa**: Si verifica un errore di reindirizzamento quando si esegue l'output in Funzioni di Azure.

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **Causa**: Si verifica un errore client quando si esegue l'output in Funzioni di Azure.

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **Causa**: Si verifica un errore server quando si esegue l'output in Funzioni di Azure.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **Causa**: La scrittura in funzioni di Azure non è riuscita perché la richiesta HTTP ha superato il timeout. 
* **Raccomandazione**: Controllare i log di Funzioni di Azure per rilevare potenziali ritardi.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **Causa**: Gli offset di input non sono validi. Questo problema può essere dovuto a un failover.
* **Raccomandazione**: Riavviare il processo di Analisi di flusso di Azure dall'ora dell'ultimo output.

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **Causa**: Si è verificato un errore durante l'invio di dati all'hub eventi.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **Causa**: Analisi di flusso non è riuscito a connettersi a un account Cosmos DB dopo aver raggiunto il numero massimo di tentativi.

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **Causa**: Analisi di flusso non è riuscito a eseguire una query sul database e sulla raccolta Cosmos DB dopo aver raggiunto il numero massimo di tentativi.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **Causa**: CosmosDB non è riuscito a creare una stored procedure dopo diversi tentativi.

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **Causa**: La stored procedure upsert ha restituito un errore. 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **Causa**: Analisi di flusso non è riuscito a inizializzare l'output del database SQL.

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **Causa**: Analisi di flusso non è riuscito a scrivere eventi nell'output del database SQL.

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **Causa**: Si è verificato un errore durante l'inizializzazione di un output del pool Synapse SQL.

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **Causa**: Si è verificato un errore durante la scrittura di un output nel pool Synapse SQL.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi delle connessioni di input](stream-analytics-troubleshoot-input.md)
* [Risolvere i problemi degli output di Analisi di flusso di Azure](stream-analytics-troubleshoot-output.md)
* [Risolvere i problemi delle query di Analisi di flusso di Azure](stream-analytics-troubleshoot-query.md)
* [Risolvere i problemi di Analisi di flusso di Azure usando i log delle risorse](stream-analytics-job-diagnostic-logs.md)
* [Errori dei dati di Analisi di flusso di Azure](data-errors.md)
