---
title: Risoluzione dei problemi delle connessioni di input in Analisi di flusso di Azure
description: Questo articolo descrive alcune tecniche per la risoluzione dei problemi delle connessioni di input nei processi di Analisi di flusso di Azure.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: b5ed614fdd378b36d8f95fc90ce7ff98d63ef31a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761869"
---
# <a name="troubleshoot-input-connections"></a>Risolvere i problemi delle connessioni di input

Questa pagina descrive i problemi comuni relativi alle connessioni di input e spiega come risolverli.

## <a name="input-events-not-received-by-job"></a>Eventi di input non ricevuti dal processo 
1.  Testare la connettività. Verificare la connettività agli input e output con il pulsante **Verifica connessione** per ogni input e output.

2.  Esaminare i dati di input.

    Per verificare che è attivo il flusso dei dati di input verso l'hub eventi, usare [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) per connettersi all'hub eventi di Azure, se in uso.
        
    Usare il pulsante [**Dati di esempio**](stream-analytics-sample-data-input.md) per ogni input e scaricare i dati di esempio dell'input.
        
    Esaminare i dati di esempio per comprendere la forma dei dati, ovvero lo schema e i [tipi di dati](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="malformed-input-events-causes-deserialization-errors"></a>Eventi di input in formato non valido determinano errori di deserializzazione 
Quando il flusso di input del processo di Analisi di flusso di Azure contiene messaggi in formato non valido, si verificano problemi di deserializzazione. Un messaggio in formato non valido può essere causato, ad esempio, da una parentesi mancante in un oggetto JSON o da un formato di timestamp errato nel campo dell'ora. 
 
Quando un processo di Analisi di flusso di Azure riceve un messaggio in formato non valido da un input, elimina il messaggio e visualizza un messaggio di avviso. Viene visualizzato un simbolo di avviso nel riquadro **Input** del processo di Analisi di flusso di Azure. Il simbolo di avviso rimane finché il processo è in esecuzione:

![Riquadro degli input di Analisi di flusso di Azure](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Abilitare i log di diagnostica per visualizzare i dettagli dell'avviso. Per gli eventi di input con formato non corretto, i log di esecuzione contengono una voce con un messaggio simile al seguente: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>Origine dell'errore di deserializzazione
Seguendo la procedura seguente è possibile analizzare nel dettaglio gli eventi di input e comprendere meglio la causa dell'errore di deserializzazione. È quindi possibile correggere l'origine dell'evento in modo da generare gli eventi futuri in formato corretto ed evitare che si verifichi nuovamente questo problema.

1. Passare al riquadro Input e fare clic sui simboli di avviso per visualizzare l'elenco dei problemi.

2. Nel riquadro dei dettagli di input viene visualizzato un elenco di avvisi con i dettagli di ogni problema. Il messaggio di avviso di esempio riportato di seguito illustra la partizione, l'offset e i numeri di sequenza dei dati JSON in formato non valido. 

   ![Messaggio di avviso di Analisi di flusso con offset](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Per trovare i dati JSON con formato non corretto, eseguire il codice CheckMalformedEvents.cs disponibile nel [repository degli esempi di GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Questo codice legge l'ID della partizione, l'offset e stampa i dati presenti nell'offset. 

4. Dopo aver eseguito la lettura dei dati, è possibile analizzare e correggere il formato di serializzazione.

5. È anche possibile [leggere gli eventi da un hub IoT con Service Bus Explorer](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Numero eccessivo di ricevitori dell'hub event per il processo
Una procedura consigliata per l'utilizzo di Hub eventi di Azure è usare più gruppi di consumer in modo da garantire la scalabilità del processo. Il numero di lettori del processo di Analisi di flusso di Azure per uno specifico input influisce sul numero dei lettori di un singolo gruppo di consumer. Il numero esatto di ricevitori si basa sui dettagli di implementazione interna per la logica della topologia con scale-out e non viene esposto esternamente. Il numero di lettori può cambiare al momento di avvio del processo o durante gli aggiornamenti del processo.

L'errore visualizzato quando la quantità di ricevitori supera il numero massimo è: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Quando il numero di lettori cambia durante un aggiornamento del processo, vengono scritti avvisi temporanei nei log di controllo. I processi di Analisi di flusso vengono ripristinati automaticamente da questi problemi temporanei.

### <a name="add-a-consumer-group-in-event-hubs"></a>Aggiungere un gruppo di consumer negli hub eventi
Per aggiungere un nuovo gruppo di consumer all'istanza dell'hub eventi, seguire questa procedura:

1. Accedere al portale di Azure.

2. Individuare gli hub eventi.

3. Selezionare **Hub eventi** nell'intestazione **Entità**.

4. Selezionare il nome dell'hub eventi.

5. Nella pagina **Istanza di Hub eventi** selezionare **Gruppi di consumer** nell'intestazione **Entità**. Viene elencato un gruppo di consumer denominato **$Default**.

6. Selezionare **+ Gruppo di consumer** per aggiungere un nuovo gruppo di consumer. 

   ![Aggiungere un gruppo di consumer negli hub eventi](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Quando l'utente ha creato l'input in Analisi di flusso affinché puntasse all'hub eventi, ha specificato il gruppo di consumer. $Default viene usato quando non è stato specificato alcun gruppo di consumer. Dopo aver creato un nuovo gruppo di consumer, modificare l'input dell'hub eventi nel processo Analisi di flusso e specificare il nome del nuovo gruppo di consumer.


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

Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
