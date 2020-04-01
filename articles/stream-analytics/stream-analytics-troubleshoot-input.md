---
title: Risoluzione dei problemi delle connessioni di input in Analisi di flusso di Azure
description: Questo articolo descrive alcune tecniche per la risoluzione dei problemi delle connessioni di input nei processi di Analisi di flusso di Azure.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 3d88123b3dd79e5707c5c19cbbae13c30cbdeb84
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409407"
---
# <a name="troubleshoot-input-connections"></a>Risolvere i problemi delle connessioni di input

Questo articolo descrive i problemi comuni relativi alle connessioni di input di Analisi di flusso di Azure, come risolvere i problemi di input e come correggerli. Molti passaggi per la risoluzione dei problemi richiedono l'abilitazione dei log di diagnostica per il processo di Analisi di flusso. Se i log di diagnostica non sono abilitati, vedere Risolvere i problemi di Analisi di flusso di Azure usando i log di [diagnostica.](stream-analytics-job-diagnostic-logs.md)

## <a name="input-events-not-received-by-job"></a>Eventi di input non ricevuti dal processo 

1.  Testare la connettività di input e output. Verificare la connettività agli input e output con il pulsante **Verifica connessione** per ogni input e output.

2.  Esaminare i dati di input.

    1. Utilizzare il pulsante [**Dati di esempio**](stream-analytics-sample-data-input.md) per ogni input. Scaricare i dati di esempio di input.
        
    1. Esaminare i dati di esempio per comprendere lo schema e i [tipi di dati.](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
    
    1. Controllare [le metriche dell'Hub eventi](../event-hubs/event-hubs-metrics-azure-monitor.md) per verificare che gli eventi vengano inviati. Le metriche dei messaggi devono essere maggiori di zero se hub eventi riceve messaggi.

3.  Assicurarsi di aver selezionato un intervallo di tempo nell'anteprima di input. Scegliere **Seleziona intervallo**di tempo , quindi immettere una durata di esempio prima di testare la query.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Eventi di input in formato non valido determinano errori di deserializzazione 

Quando il flusso di input del processo di Analisi di flusso di Azure contiene messaggi in formato non valido, si verificano problemi di deserializzazione. Ad esempio, un messaggio in formato non corretto potrebbe essere causato da una parentesi o parentesi graffa mancante in un oggetto JSON o da un formato timestamp non corretto nel campo dell'ora. 
 
Quando un processo di Analisi di flusso di Azure riceve un messaggio in formato non valido da un input, elimina il messaggio e visualizza un messaggio di avviso. Nel riquadro **Input** del processo di Analisi di flusso viene visualizzato un simbolo di avviso. Il simbolo di avviso seguente esiste finché il processo è in esecuzione:

![Riquadro degli input di Analisi di flusso di Azure](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Abilitare i log di diagnostica per visualizzare i dettagli dell'errore e il messaggio (payload) che ha causato l'errore. Esistono diversi motivi per cui possono verificarsi errori di deserializzazione. Per ulteriori informazioni su errori di deserializzazione specifici, vedere [Errori relativi ai dati](data-errors.md#input-data-errors)di input . Se i log di diagnostica non sono abilitati, nel portale di Azure sarà disponibile una breve notifica.

![Notifica di avviso dettagli di input](media/stream-analytics-malformed-events/warning-message-with-offset.png)

Nei casi in cui il payload del messaggio è maggiore di 32 KB o è in formato binario, eseguire il codice CheckMalformedEvents.cs disponibile nel [repository degli esempi di GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Questo codice legge l'ID della partizione, l'offset e stampa i dati presenti nell'offset. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Il processo supera il numero massimo di ricevitori dell'hub eventi

Una procedura consigliata per l'utilizzo di Hub eventi consiste nell'utilizzare più gruppi di consumer per la scalabilità dei processi. Il numero di lettori del processo di Analisi di flusso di Azure per uno specifico input influisce sul numero dei lettori di un singolo gruppo di consumer. Il numero esatto di ricevitori si basa sui dettagli di implementazione interna per la logica della topologia con scale-out e non viene esposto esternamente. Il numero di lettori può cambiare al momento di avvio del processo o durante gli aggiornamenti del processo.

L'errore visualizzato quando la quantità di ricevitori supera il numero massimo è: 

`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Quando il numero di lettori cambia durante un aggiornamento del processo, vengono scritti avvisi temporanei nei log di controllo. I processi di Analisi di flusso vengono ripristinati automaticamente da questi problemi temporanei.

### <a name="add-a-consumer-group-in-event-hubs"></a>Aggiungere un gruppo di consumer negli hub eventi

Per aggiungere un nuovo gruppo di consumer all'istanza dell'hub eventi, seguire questa procedura:

1. Accedere al portale di Azure.

2. Individuare l'Hub eventi.

3. Selezionare **Hub eventi** nell'intestazione **Entità**.

4. Selezionare il nome dell'hub eventi.

5. Nella pagina **Istanza di Hub eventi** selezionare **Gruppi di consumer** nell'intestazione **Entità**. Viene elencato un gruppo di consumer denominato **$Default**.

6. Selezionare **+ Gruppo di consumer** per aggiungere un nuovo gruppo di consumer. 

   ![Aggiungere un gruppo di consumer negli hub eventi](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Quando l'utente ha creato l'input in Analisi di flusso affinché puntasse all'hub eventi, ha specificato il gruppo di consumer. **$Default** viene utilizzato quando non ne è specificato alcuno. Dopo aver creato un nuovo gruppo di consumer, modificare l'input dell'hub eventi nel processo Analisi di flusso e specificare il nome del nuovo gruppo di consumer.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Il numero di lettori per partizione supera il limite impostato in Hub eventi di Azure

Se la sintassi della query del flusso fa riferimento più volte alla stessa risorsa di input dell'hub di eventi, il motore del processo può usare più lettori per ogni query da quello stesso gruppo di consumer. Quando ci sono troppi riferimenti allo stesso gruppo di consumer, il processo può superare il limite di cinque e generare un errore. In questo caso è possibile creare altre suddivisioni usando più input in più gruppi di consumer servendosi della soluzione descritta nella sezione seguente. 

Gli scenari in cui il numero di lettori per ogni partizione supera il limite di cinque hub eventi comprendono i seguenti:

* Più istruzioni SELECT: se si usano più istruzioni SELECT che fanno riferimento allo **stesso** input dell'hub eventi, ogni istruzione SELECT fa sì che venga creato un nuovo ricevitore.

* UNION: quando si usa UNION, è possibile avere più input che si riferiscono allo **stesso** hub eventi e gruppo di consumer.

* SELF JOIN: quando si usa un'operazione SELF JOIN è possibile fare riferimento allo **stesso** hub eventi più volte.

Le procedure consigliate seguenti possono aiutare a mitigare gli scenari in cui il numero di lettori per ogni partizione supera il limite di cinque hub eventi.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Suddividere la query in più passaggi usando una clausola WITH

La clausola WITH specifica un set di risultati con nome temporaneo a cui è possibile fare riferimento mediante una clausola FROM nella query. La clausola WITH viene definita nell'ambito di esecuzione di una singola istruzione SELECT.

Ad esempio, invece di questa query:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Usare questa query:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Assicurarsi che gli input siano associati a gruppi di consumer diversi

Per le query in cui tre o più input sono connessi allo stesso gruppo di consumer di hub eventi, creare gruppi di consumer separati. Ciò richiede la creazione di input di Analisi di flusso aggiuntivi.

## <a name="get-help"></a>Ottenere aiuto

Per ulteriore assistenza, prova il forum di Analisi di Flusso di [Azure.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi di flusso di AzureGet started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Guida di riferimento al linguaggio di query di Analisi di flusso di AzureAzure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
