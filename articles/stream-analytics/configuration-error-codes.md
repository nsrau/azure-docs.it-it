---
title: Codici errore di configurazione - Analisi di flusso di Azure
description: Risolvere i problemi di Analisi di flusso di Azure con i codici errore di configurazione.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: de8eefd099f3691ae5e5eb5234ae8f76015dd68f
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86041116"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Codici errore di configurazione di Analisi di flusso di Azure

È possibile usare log attività e log risorse per facilitare il debug di comportamenti imprevisti del processo di Analisi di flusso di Azure. Questo articolo riporta la descrizione di ogni codice errore di configurazione. Gli errori di configurazione sono correlati alla configurazione del processo o alle configurazioni di input e output.

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **Causa**: L'hub eventi ha generato un errore di *accesso non autorizzato*.

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **Causa**: Sono presenti più destinatari di hub eventi con valori epoch diversi.
* **Raccomandazione**: Assicurarsi che *Service Bus Explorer* o un'applicazione *EventProcessorHost* non siano connessi durante l'esecuzione del processo di Analisi di flusso.

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **Causa**: Analisi di flusso non è in grado di connettersi a una partizione perché è stato raggiunto il numero massimo di destinatari consentiti per partizione in un gruppo di consumer.
* **Raccomandazione**: Assicurarsi che altri processi di Analisi di flusso o Service Bus Explorer non stiano usando lo stesso gruppo di consumer.

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **Causa**: Si è verificato un errore durante la scrittura dei dati nell'hub eventi a causa della limitazione.
* **Raccomandazione**: Se questa situazione si verifica costantemente, aggiornare la velocità effettiva.

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **Causa**: La configurazione della connessione specificata non è corretta.
* **Raccomandazione**: Correggere la configurazione e riavviare il processo.

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **Causa**: L'host dell'hub eventi non è raggiungibile.
* **Raccomandazione**: Verificare che il nome host specificato sia corretto.

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **Causa**: Il mittente EventHub ha rilevato un conteggio delle partizioni di EventHub imprevisto.
* **Raccomandazione**: Riavviare il processo di Analisi di flusso se il conteggio delle partizioni di EventHub è stato modificato.

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **Causa**: Analisi di flusso non è riuscito a trovare la chiave di partizione di una particolare raccolta di Cosmos DB nel database.
* **Raccomandazione**: Verificare che sia stata specificata una chiave di partizione valida per la raccolta in Cosmos DB.

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **Causa**: Questo errore viene generato quando una chiave di partizione non è né un nodo foglia né al livello principale.

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **Causa**: L'output della query non può contenere la colonna \[id] se viene scelta una colonna diversa come proprietà della chiave primaria.

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **Causa**: Analisi di flusso non riesce a trovare un database CosmosDB.

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **Causa**: Analisi di flusso non riesce a trovare una specifica raccolta di Cosmos DB in un database.

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **Causa**: Si è verificato un errore durante la scrittura dei dati a causa della limitazione da parte di Cosmos DB.
* **Raccomandazione**: Aggiornare il livello di prestazioni della raccolta e ottimizzare le prestazioni del database.

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **Causa**: Si è verificato un errore di autenticazione nel processo di Analisi di flusso.
* **Raccomandazione**: Verificare che la stringa di connessione del database SQL sia corretta.

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **Causa**: Si è verificato un errore di autenticazione nel processo di Analisi di flusso. 
* **Raccomandazione**: Verificare che il nome dell'account sia configurato correttamente e che l'identità gestita del processo abbia accesso al database SQL.

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **Causa**: Analisi di flusso non riesce a trovare le informazioni sullo schema per una tabella specifica.

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **Causa**: Il database SQL non è supportato.
* **Raccomandazione**: Usare il pool Synapse SQL.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi delle connessioni di input](stream-analytics-troubleshoot-input.md)
* [Risolvere i problemi degli output di Analisi di flusso di Azure](stream-analytics-troubleshoot-output.md)
* [Risolvere i problemi delle query di Analisi di flusso di Azure](stream-analytics-troubleshoot-query.md)
* [Risolvere i problemi di Analisi di flusso di Azure usando i log delle risorse](stream-analytics-job-diagnostic-logs.md)
* [Errori dei dati di Analisi di flusso di Azure](data-errors.md)
