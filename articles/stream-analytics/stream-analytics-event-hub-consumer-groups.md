---
title: Risolvere i problemi dei ricevitori dell'hub eventi in Analisi di flusso di Azure
description: Questo articolo descrive come usare più gruppi di consumer per gli input degli hub eventi nei processi di Analisi di flusso.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: aaa8c4e8d273b44f453d3f63f0be1d4baf980649
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2018
---
# <a name="troubleshoot-event-hub-receivers-in-azure-stream-analytics"></a>Risolvere i problemi dei ricevitori dell'hub eventi in Analisi di flusso di Azure

È possibile usare gli hub eventi di Analisi di flusso di Azure per inserire o estrarre dati da un processo. Una procedura consigliata per l'utilizzo dell'hub eventi è usare più gruppi di consumer in modo da garantire la scalabilità del processo. Uno dei motivi è che il numero di lettori del processo di Analisi di flusso per uno specifico input influisce sul numero dei lettori di un singolo gruppo di consumer. Il numero preciso di ricevitori si basa sui dettagli di implementazione interna per la logica della topologia con scalabilità orizzontale. Il numero di ricevitori non viene esposto esternamente. Il numero di lettori può cambiare al momento di inizio del processo o durante gli aggiornamenti del processo.

L'errore mostrato quando il numero di ricevitori supera il massimo è: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Quando il numero di lettori cambia durante un aggiornamento del processo, vengono scritti avvisi temporanei nei log di controllo. I processi di Analisi di flusso vengono ripristinati automaticamente da questi problemi temporanei.

## <a name="add-a-consumer-group-in-event-hubs"></a>Aggiungere un gruppo di consumer negli hub eventi
Per aggiungere un nuovo gruppo di consumer all'istanza dell'hub eventi, seguire questa procedura:

1. Accedere al portale di Azure.

2. Individuare gli hub eventi.

3. Selezionare **Hub eventi** nell'intestazione **Entità**.

4. Selezionare il nome dell'hub eventi.

5. Nella pagina **Istanza di Hub eventi** selezionare **Gruppi di consumer** nell'intestazione **Entità**. Viene elencato un gruppo di consumer denominato **$Default**.

6. Selezionare **+ Gruppo di consumer** per aggiungere un nuovo gruppo di consumer. 

   ![Aggiungere un gruppo di consumer negli hub eventi](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Quando l'utente ha creato l'input in Analisi di flusso affinché puntasse all'hub eventi, ha specificato il gruppo di consumer. $Default viene usato quando non è stato specificato alcun gruppo di consumer. Dopo aver creato un nuovo gruppo di consumer, modificare l'input dell'hub eventi nel processo Analisi di flusso e specificare il nome del nuovo gruppo di consumer.


## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Il numero di lettori per partizione supera il limite di cinque hub eventi

Se la sintassi della query del flusso fa riferimento più volte alla stessa risorsa di input dell'hub di eventi, il motore del processo può usare più lettori per ogni query da quello stesso gruppo di consumer. Quando ci sono troppi riferimenti allo stesso gruppo di consumer, il processo può superare il limite di cinque e generare un errore. In questo caso è possibile creare altre suddivisioni usando più input in più gruppi di consumer servendosi della soluzione descritta nella sezione seguente. 

Gli scenari in cui il numero di lettori per ogni partizione supera il limite di cinque hub eventi comprendono i seguenti:

* Più istruzioni SELECT: se si usano più istruzioni SELECT che fanno riferimento allo **stesso** input dell'hub eventi, ogni istruzione SELECT fa sì che venga creato un nuovo ricevitore.
* UNION: quando si usa UNION, è possibile avere più input che si riferiscono allo **stesso** hub eventi e gruppo di consumer.
* SELF JOIN: quando si usa un'operazione SELF JOIN è possibile fare riferimento allo **stesso** hub eventi più volte.

## <a name="solution"></a>Soluzione

Le procedure consigliate seguenti possono aiutare a mitigare gli scenari in cui il numero di lettori per ogni partizione supera il limite di cinque hub eventi.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Suddividere la query in più passaggi usando una clausola WITH

La clausola WITH specifica un set di risultati con nome temporaneo a cui è possibile fare riferimento mediante una clausola FROM nella query. La clausola WITH viene definita nell'ambito di esecuzione di una singola istruzione SELECT.

Ad esempio, invece di questa query:

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Usare questa query:

```
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


## <a name="next-steps"></a>Passaggi successivi
* [Scalabilità dei processi di Analisi di flusso](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx)
