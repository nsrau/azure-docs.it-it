---
title: Risolvere i problemi degli output di Analisi di flusso di Azure
description: Questo articolo descrive alcune tecniche per la risoluzione dei problemi delle connessioni di output nei processi di Analisi di flusso di Azure.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 305632a0faa1eb7e217e86d36c5159e557df7aaf
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409260"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Risolvere i problemi degli output di Analisi di flusso di Azure

Questo articolo descrive i problemi comuni relativi alle connessioni di output di Analisi di flusso di Azure, come risolvere i problemi di output e come correggerli. Molti passaggi per la risoluzione dei problemi richiedono l'abilitazione dei log di diagnostica per il processo di Analisi di flusso. Se i log di diagnostica non sono abilitati, vedere Risolvere i problemi di Analisi di flusso di Azure usando i log di [diagnostica.](stream-analytics-job-diagnostic-logs.md)

## <a name="output-not-produced-by-job"></a>Output non prodotto dal processo

1.  Verificare la connettività agli output con il pulsante **Verifica connessione** per ogni output.

2.  Esaminare [**le metriche**](stream-analytics-monitoring.md) di monitoraggio nella scheda **Monitoraggio.Look** at Monitoring Metrics on the Monitor tab. Poiché i valori sono aggregati, le metriche vengono ritardate di alcuni minuti.
   * Se gli eventi di input sono maggiori di 0, il processo è in grado di leggere i dati di input. Se gli eventi di input non sono maggiori di 0, si verifica un problema con l'input del processo. Per informazioni su come risolvere i problemi di connessione di input, vedere Risolvere i problemi di connessione di [input.](stream-analytics-troubleshoot-input.md)
   * Se errori di conversione dei dati sono maggiori di 0 e si arrampicano, vedere Errori dei dati di [Azure Stream Analytics](data-errors.md) per informazioni dettagliate sugli errori di conversione dei dati.
   * Se gli errori di runtime sono maggiori di 0, il processo può ricevere dati ma genera errori durante l'elaborazione della query. Per trovare gli errori, consultare i [log di controllo](../azure-resource-manager/management/view-activity-logs.md) e filtrare lo stato *non riuscito*.
   * Se InputEvents è maggiore di 0 e OutputEvents è uguale a 0, si verifica una delle seguenti condizioni:
      * L'elaborazione della query non ha prodotto eventi di output.
      * Gli eventi o i campi potrebbero essere in formato non corretto, generando output pari a zero dopo l'elaborazione delle query.
      * Il processo non è riuscito a eseguire il push dei dati al sink di output per motivi di connettività o autenticazione.

   In tutti i casi di errore menzionati sopra, i messaggi di log delle operazioni specificano altri dettagli e la spiegazione della situazione, tranne nei casi in cui la logica della query ha filtrato tutti gli eventi. Se l'elaborazione di più eventi genera errori, gli errori vengono aggregati ogni 10 minuti.

## <a name="job-output-is-delayed"></a>L'output del processo è ritardato

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

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Avviso di violazione della chiave con output del database SQL di Azure

Quando si configura il database SQL di Azure come output di un processo di Analisi di flusso, vengono eseguiti inserimenti bulk dei record nella tabella di destinazione. In generale, Analisi di flusso di Azure garantisce [almeno un recapito](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) nel sink di output, ma è possibile [ottenere esattamente un recapito]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) nell'output SQL quando per la tabella SQL viene definito un vincolo univoco.

Quando sono impostati vincoli di chiave univoca sulla tabella SQL e sono presenti record duplicati da inserire nella tabella SQL, Analisi di flusso di Azure rimuove il record duplicato. Suddivide i dati in batch e inserisce in modo ricorsivo i batch finché non viene trovato un singolo record duplicato. Se il processo di streaming ha un numero considerevole di righe duplicate, questo processo di divisione e inserimento deve ignorare i duplicati uno alla volta, pertanto risulta meno efficiente e dispendioso in termini di tempo. Se vengono visualizzati più messaggi di avviso di violazione della chiave nel log attività nell'ultima ora, è probabile che l'output SQL stia rallentando l'intero processo.

Per risolvere questo problema, è necessario [configurare l'indice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) che causa la violazione della chiave, abilitando l'opzione IGNORE_DUP_KEY. L'abilitazione di questa opzione consente a SQL di ignorare i valori duplicati durante gli inserimenti bulk e SQL Azure genera semplicemente un messaggio di avviso anziché un errore. Analisi di flusso di Azure non genera più errori di violazione della chiave primaria.

Quando si configura IGNORE_DUP_KEY per diversi tipi di indici, tenere presente le osservazioni seguenti:

* Non è possibile impostare IGNORE_DUP_KEY in una chiave primaria o in un vincolo univoco che usa ALTER INDEX, ma è necessario eliminare e ricreare l'indice.  
* È possibile impostare l'opzione IGNORE_DUP_KEY con ALTER INDEX per un indice univoco, diverso dal vincolo PRIMARY KEY/UNIQUE e creato con la definizione CREATE INDEX o INDEX.  

* L'opzione IGNORE_DUP_KEY non è valida per gli indici columnstore perché non è possibile applicare l'univocità in tali indici.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>I nomi di colonna sono minuscoli da Analisi di flusso di AzureColumn names are lower-cased by Azure Stream Analytics
Quando si usa il livello di compatibilità originale (1.0), Analisi di flusso di Azure ha usato per modificare i nomi delle colonne in lettere minuscole. Questo comportamento è stato risolto nei livelli di compatibilità successivi. Al fine di preservare il caso, si consiglia ai clienti di passare al livello di compatibilità 1.1 e versioni successive. Per altre informazioni sul [livello di compatibilità per](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)i processi di Analisi di flusso di Azure, è possibile trovare altre informazioni sul livello di compatibilità per i processi di Analisi di flusso di Azure.You can find more information on

## <a name="get-help"></a>Ottenere aiuto

Per ulteriore assistenza, prova il forum di Analisi di Flusso di [Azure.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi di flusso di AzureGet started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Guida di riferimento al linguaggio di query di Analisi di flusso di AzureAzure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
