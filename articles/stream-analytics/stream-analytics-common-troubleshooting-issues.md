---
title: Risoluzione dei problemi comuni in Analisi di flusso
description: Questo articolo descrive diversi problemi comuni in Analisi di flusso di Azure e le procedure per la relativa risoluzione.
services: stream-analytics
author: jasonwhowell
manager: kfile
ms.author: jasonh
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: d7784792cd7e1475028dc0a5e9664916f1d45a07
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43699220"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Problemi comuni in Analisi di flusso e passaggi per risolvere tali problemi

## <a name="troubleshoot-malformed-input-events"></a>Risoluzione dei problemi relativi agli eventi di input in formato non valido

 I problemi di serializzazione sono causati dal fatto che il flusso di input del processo di Analisi di flusso contiene messaggi in formato non valido. Ad esempio, un messaggio in formato non valido potrebbe essere causato da una parentesi o una parentesi graffa mancante in un oggetto JSON o un formato di timestamp errato in un campo ora. 
 
 Quando un processo di Analisi di flusso riceve un messaggio in formato non valido da un input, elimina il messaggio e visualizza un messaggio di avviso per l'utente. Un simbolo di avviso viene visualizzato nel riquadro **Input** del processo di Analisi di flusso (il simbolo di avviso rimane finché il processo è in esecuzione):

![Riquadro Input](media/stream-analytics-malformed-events/inputs_tile.png)

Per ottenere altre informazioni, è possibile abilitare i log di diagnostica per visualizzare i dettagli dell'avviso. Per gli eventi di input con formato non corretto, i log di esecuzione contengono una voce con un messaggio simile al seguente: "Messaggio: Non è stato possibile deserializzare gli eventi di input dalla risorsa <blob URI> come json". 

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

1. Passare al riquadro Input e fare clic per visualizzare gli avvisi.

2. Nel riquadro dei dettagli di input viene visualizzato un set di avvisi con i dettagli sul problema. Di seguito è riportato un messaggio di avviso di esempio, che mostra la partizione, l'offset e i numeri di sequenza dei dati JSON in formato non valido. 

   ![Messaggio di avviso con offset](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Per ottenere i dati JSON in formato non valido, eseguire il codice CheckMalformedEvents.cs. Questo esempio è disponibile nel [repository degli esempi di GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Questo codice legge l'ID della partizione, l'offset e stampa i dati presenti nell'offset. 

4. Dopo aver eseguito la lettura dei dati, è possibile analizzare e correggere il formato di serializzazione.

5. È anche possibile [leggere gli eventi da un hub IoT con Service Bus Explorer](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="delayed-output"></a>Output ritardato

### <a name="first-output-is-delayed"></a>Il primo output è ritardato
Quando viene avviato un processo di Analisi di flusso di Azure, gli eventi di input vengono letti, ma in determinate circostanze l'output prodotto può presentare un ritardo.

Valori di tempo di grandi dimensioni negli elementi di query temporali possono contribuire al ritardo dell'output. Per produrre l'output corretto su intervalli di tempo di grandi dimensioni, il processo di streaming viene avviato eseguendo la lettura dei dati dal momento più recente possibile (fino a sette giorni prima) per riempire l'intervallo di tempo. Durante questo periodo, non viene prodotto alcun output finché la lettura di aggiornamento degli eventi di input in attesa non è stata completata. Questo problema può manifestarsi quando il sistema aggiorna i processi di streaming, riavviando il processo. Questi aggiornamenti in genere vengono eseguiti una volta ogni due mesi. 

È pertanto necessario prestare attenzione durante la progettazione della query di Analisi di flusso. Se si usa un intervallo di tempo di grandi dimensioni (più di qualche ora, fino a sette giorni) per gli elementi temporali nella sintassi delle query del processo, questo può causare un ritardo nel primo output quando il processo viene avviato o riavviato.  

Un modo per attenuare questo ritardo del primo output consiste nell'usare tecniche di parallelizzazione delle query (partizionamento dei dati) o aggiungere altre unità di streaming per aumentare la velocità effettiva finché l'aggiornamento del processo non viene completato.  Per altre informazioni, vedere [Considerations when creating Stream Analytics jobs](stream-analytics-concepts-checkpoint-replay.md) (Considerazioni relative alla creazione dei processi di Analisi di flusso di Azure)

Questi fattori influiscono sulla tempestività con cui viene generato il primo output:

1. Uso di funzioni di aggregazione finestra (GROUP BY di finestre temporali scorrevoli, di salto e a cascata)
   - Per le aggregazioni con finestra a cascata o di salto, i risultati vengono generati alla fine dell'intervallo di tempo della finestra. 
   - Per una finestra temporale scorrevole, i risultati vengono generati quando un evento entra o esce dalla finestra temporale scorrevole. 
   - Se si prevede di usare una finestra di grandi dimensioni (più di un'ora), è consigliabile scegliere una finestra di salto o una finestra temporale scorrevole, in modo da visualizzare l'output con maggiore frequenza.

2. Uso di join temporali (JOIN con DATEDIFF)
   - Vengono generate corrispondenze non appena arrivano entrambi i lati degli eventi corrispondenti.
   - Per i dati che non presentano una corrispondenza viene generato (LEFT OUTER JOIN) alla fine della finestra DATEDIFF rispetto a ogni evento sul lato sinistro.

3. Uso di funzioni di analisi temporale (ISFIRST, LAST e LAG con LIMIT DURATION)
   - Per le funzioni di analisi, l'output viene generato per ogni evento, senza alcun ritardo.

### <a name="output-falls-behind"></a>Ritardo dell'output
Durante il normale funzionamento del processo, se si rileva un ritardo dell'output del processo (latenza sempre più lunga), è possibile identificare le cause radice esaminando i fattori seguenti:
- Se viene applicata una limitazione al sink downstream
- Se viene applicata una limitazione al sink upstream
- Se la logica di elaborazione della query comporta un uso elevato delle risorse di calcolo

Per visualizzare i dettagli, nel portale di Azure selezionare il processo di streaming, quindi selezionare **Diagramma del processo**. Per ogni input è presente una metrica dell'evento per backlog di partizione. Se la metrica dell'evento di backlog continua ad aumentare, è un indicatore del fatto che le risorse del sistema sono vincolate. Il problema può essere causato dalla limitazione del sink di output o dall'uso elevato della CPU. Per altre informazioni sull'uso del diagramma di processo, vedere [Debug guidato dai dati mediante il diagramma di processo](stream-analytics-job-diagram-with-metrics.md).

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Gestire i record duplicati nell'output del database SQL di Azure

Quando si configura il database SQL di Azure come output di un processo di Analisi di flusso, vengono eseguiti inserimenti bulk dei record nella tabella di destinazione. In generale, Analisi di flusso di Azure garantisce [almeno un recapito]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) nel sink di output, ma è possibile [ottenere esattamente un recapito]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) nell'output SQL quando per la tabella SQL viene definito un vincolo univoco. 

Quando sono impostati vincoli di chiave univoca sulla tabella SQL e sono presenti record duplicati da inserire nella tabella SQL, Analisi di flusso di Azure rimuove il record duplicato. Suddivide i dati in batch e inserisce in modo ricorsivo i batch finché non viene trovato un singolo record duplicato. Se il processo di streaming ha un numero considerevole di righe duplicate, questo processo di divisione e inserimento deve ignorare i duplicati uno alla volta, pertanto risulta meno efficiente e dispendioso in termini di tempo. Se vengono visualizzati più messaggi di avviso di violazione della chiave nel log attività nell'ultima ora, è probabile che l'output SQL stia rallentando l'intero processo. 

Per risolvere questo problema, è necessario [configurare l'indice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) che causa la violazione della chiave, abilitando l'opzione IGNORE_DUP_KEY. L'abilitazione di questa opzione consente a SQL di ignorare i valori duplicati durante gli inserimenti bulk e SQL Azure genera semplicemente un messaggio di avviso anziché un errore. Analisi di flusso di Azure non genera più errori di violazione della chiave primaria.

Quando si configura IGNORE_DUP_KEY per diversi tipi di indici, tenere presente le osservazioni seguenti:

* Non è possibile impostare IGNORE_DUP_KEY in una chiave primaria o in un vincolo univoco che usa ALTER INDEX, ma è necessario eliminare e ricreare l'indice.  
* È possibile impostare l'opzione IGNORE_DUP_KEY con ALTER INDEX per un indice univoco, diverso dal vincolo PRIMARY KEY/UNIQUE e creato con la definizione CREATE INDEX o INDEX.  
* L'opzione IGNORE_DUP_KEY non è valida per gli indici columnstore perché non è possibile applicare l'univocità in tali indici.  

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
