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
ms.openlocfilehash: bee40bc30a0ffbdacf8cc7bf88d1512c4fc43fa4
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147939"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Risolvere i problemi degli output di Analisi di flusso di Azure

Questo articolo descrive i problemi comuni relativi alle connessioni di output di analisi di flusso di Azure e come risolvere i problemi di output. Molti passaggi per la risoluzione dei problemi richiedono che la risorsa e altri log di diagnostica siano abilitati per il processo di analisi di flusso. Se non sono abilitati i log delle risorse, vedere [risolvere i problemi di analisi di flusso di Azure usando i log delle risorse](stream-analytics-job-diagnostic-logs.md).

## <a name="the-job-doesnt-produce-output"></a>Il processo non produce l'output

1. Verificare la connettività agli output con il pulsante **Verifica connessione** per ogni output.
1. Esaminare le [metriche di monitoraggio](stream-analytics-monitoring.md) nella scheda **monitoraggio** . Poiché i valori vengono aggregati, le metriche vengono posticipate di pochi minuti.

   * Se il valore **degli eventi di input** è maggiore di zero, il processo può leggere i dati di input. Se il valore **degli eventi di input** non è maggiore di zero, si verifica un problema con l'input del processo. Per ulteriori informazioni, vedere [risolvere i problemi relativi alle connessioni di input](stream-analytics-troubleshoot-input.md) .
   * Se il valore degli **errori di conversione dei dati** è maggiore di zero e scalabilità, vedere [errori dei dati di analisi di flusso di Azure](data-errors.md) per informazioni dettagliate sugli errori di conversione dei dati.
   * Se il valore di **errori di runtime** è maggiore di zero, il processo riceve i dati, ma genera errori durante l'elaborazione della query. Per individuare gli errori, accedere ai [log di controllo](../azure-resource-manager/management/view-activity-logs.md)e quindi filtrare lo stato di **errore** .
   * Se il valore degli **eventi di input** è maggiore di zero e il valore **degli eventi di output** è uguale a zero, viene soddisfatta una delle seguenti istruzioni:
      * L'elaborazione della query non ha prodotto alcun evento di output.
      * Gli eventi o i campi potrebbero non essere corretti, ottenendo un output pari a zero dopo l'elaborazione della query.
      * Il processo non è riuscito a eseguire il push dei dati al sink di output per motivi di connettività o autenticazione.

   I messaggi del log operazioni spiegano i dettagli aggiuntivi, incluse le operazioni eseguite, tranne nei casi in cui la logica di query Filtra tutti gli eventi. Se l'elaborazione di più eventi genera errori, gli errori vengono aggregati ogni 10 minuti.

## <a name="the-first-output-is-delayed"></a>Il primo output viene ritardato

Quando viene avviato un processo di analisi di flusso, vengono letti gli eventi di input. In alcune circostanze, tuttavia, è possibile che si verifichi un ritardo nell'output.

Valori di tempo di grandi dimensioni negli elementi di query temporali possono contribuire al ritardo dell'output. Per produrre l'output corretto nelle finestre temporali di grandi dimensioni, il processo di streaming legge i dati dal tempo più recente possibile per riempire l'intervallo di tempo. I dati possono essere passati fino a sette giorni prima. Nessun output viene prodotto fino a quando non vengono letti gli eventi di input in attesa. Questo problema può emergere quando il sistema aggiorna i processi di streaming. Quando si verifica un aggiornamento, il processo viene riavviato. Questi aggiornamenti in genere vengono eseguiti una volta ogni due mesi.

Quando si progetta la query di analisi di flusso, usare discrezione. Se si usa un intervallo di tempo di grandi dimensioni per gli elementi temporali nella sintassi di query del processo, può causare un ritardo nel primo output quando il processo viene avviato o riavviato. Più di diverse ore, fino a sette giorni, viene considerato un intervallo di tempo di grandi dimensioni.

Una mitigazione per questo tipo di ritardo di output consiste nell'utilizzare tecniche di parallelizzazione delle query, ad esempio il partizionamento dei dati. In alternativa, è possibile aggiungere altre unità di streaming per migliorare la velocità effettiva fino a quando il processo non viene ripreso.  Per altre informazioni, vedere [considerazioni sulla creazione di processi di analisi di flusso](stream-analytics-concepts-checkpoint-replay.md).

Questi fattori influiscono sulla tempestività del primo output:

* Utilizzo di funzioni di aggregazione finestra, ad esempio una clausola GROUP BY di finestre a cascata, di salto e di scorrimento:

  * Per le aggregazioni di finestre a cascata o di salto, i risultati vengono generati alla fine dell'intervallo di tempo della finestra.
  * Per una finestra temporale scorrevole, i risultati vengono generati quando un evento entra o esce dalla finestra temporale scorrevole.
  * Se si prevede di usare una finestra di grandi dimensioni, ad esempio più di un'ora, è preferibile scegliere una finestra di salto o scorrevole. Questi tipi di finestra consentono di visualizzare l'output con maggiore frequenza.

* Uso di join temporali, ad esempio JOIN con DATEDIFF:
  * Corrisponde a generate non appena vengono ricevuti entrambi i lati degli eventi corrispondenti.
  * Per ogni evento sul lato sinistro, i dati in cui manca una corrispondenza, ad esempio LEFT OUTER JOIN, vengono generati alla fine della finestra DATEDIFF.

* Uso di funzioni analitiche temporali, ad esempio, FIRST, LAST e LAG con la durata del limite:
  * Per le funzioni analitiche, l'output viene generato per ogni evento. Non si verifica alcun ritardo.

## <a name="the-output-falls-behind"></a>L'output si trova dietro

Durante il normale funzionamento di un processo, l'output potrebbe avere periodi di latenza e più lunghi. Se l'output è in ritardo, è possibile individuare le cause principali esaminando i fattori seguenti:

* Se viene applicata una limitazione al sink downstream
* Se viene applicata una limitazione al sink upstream
* Se la logica di elaborazione della query comporta un uso elevato delle risorse di calcolo

Per visualizzare i dettagli di output, selezionare il processo di streaming nella portale di Azure e quindi selezionare **Diagramma processi**. Per ogni input è presente una metrica dell'evento di backlog per partizione. Se la metrica continua ad aumentare, è un indicatore che le risorse di sistema sono vincolate. L'aumento è potenzialmente dovuto alla limitazione del sink di output o a un utilizzo elevato della CPU. Per ulteriori informazioni, vedere [debug basato sui dati tramite il diagramma dei processi](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Avviso di violazione della chiave con output del database SQL di Azure

Quando si configura un database SQL di Azure come output in un processo di analisi di flusso, bulk inserisce i record nella tabella di destinazione. In generale, analisi di flusso di Azure garantisce il [recapito at-least-once](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) al sink di output. È comunque possibile [ottenere il recapito di una sola volta]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) a un output SQL quando per una tabella SQL è stato definito un vincolo UNIQUE.

Quando si configurano vincoli di chiave univoca nella tabella SQL, analisi di flusso di Azure rimuove i record duplicati. Suddivide i dati in batch e inserisce in modo ricorsivo i batch fino a quando non viene trovato un singolo record duplicato. Il processo di suddivisione e inserimento ignora i duplicati uno alla volta. Per un processo di streaming con molte righe duplicate, il processo è inefficiente e richiede molto tempo. Se nel log attività vengono visualizzati più messaggi di avviso di violazione della chiave per l'ora precedente, è probabile che l'output SQL rallenti l'intero processo.

Per risolvere questo problema, [configurare l'indice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) che causa la violazione della chiave abilitando l'opzione IGNORE_DUP_KEY. Questa opzione consente a SQL di ignorare i valori duplicati durante gli inserimenti bulk. Il database SQL di Azure genera semplicemente un messaggio di avviso anziché un errore. Di conseguenza, analisi di flusso di Azure non produce più errori di violazione della chiave primaria.

Quando si configura IGNORE_DUP_KEY per diversi tipi di indici, tenere presente le osservazioni seguenti:

* Non è possibile impostare IGNORE_DUP_KEY in una chiave primaria o in un vincolo UNIQUE che utilizza ALTER INDEX. È necessario eliminare l'indice e ricrearlo.  
* È possibile impostare IGNORE_DUP_KEY utilizzando ALTER INDEX per un indice univoco. Questa istanza è diversa da un vincolo PRIMARY KEY/UNIQUE e viene creata usando una definizione CREATE INDEX o INDEX.  
* L'opzione IGNORE_DUP_KEY non si applica agli indici columnstore perché non è possibile imporre l'univocità su di essi.  

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>I nomi delle colonne sono minuscoli in analisi di flusso di Azure (1,0)

Quando si usa il livello di compatibilità originale (1,0), analisi di flusso di Azure modifica i nomi di colonna in lettere minuscole. Questo comportamento è stato corretto nei livelli di compatibilità successivi. Per mantenere il caso, passare al livello di compatibilità 1,1 o versione successiva. Per altre informazioni, vedere [livello di compatibilità per i processi di analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).

## <a name="get-help"></a>Ottenere aiuto

Per ulteriore assistenza, provare il [Forum di analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di analisi di flusso di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Riferimento al linguaggio di query di analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Riferimento all'API REST di gestione di analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
