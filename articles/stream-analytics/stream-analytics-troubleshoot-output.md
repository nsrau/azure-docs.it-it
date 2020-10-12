---
title: Risolvere i problemi degli output di Analisi di flusso di Azure
description: Questo articolo descrive alcune tecniche per la risoluzione dei problemi delle connessioni di output nei processi di Analisi di flusso di Azure.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 1fa9a8aa24cf6a8c8c2223836ae80b8b47807c81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87903188"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Risolvere i problemi degli output di Analisi di flusso di Azure

Questo articolo descrive i problemi comuni relativi alle connessioni di output di Analisi di flusso di Azure e come correggere i problemi di output. Molti passaggi per la risoluzione dei problemi richiedono l'abilitazione dei log delle risorse e di altri log di diagnostica per il processo di Analisi di flusso. Se i log delle risorse non sono abilitati, vedere [Risolvere i problemi di Analisi di flusso di Azure usando i log delle risorse](stream-analytics-job-diagnostic-logs.md).

## <a name="the-job-doesnt-produce-output"></a>Il processo non produce alcun output

1. Verificare la connettività agli output con il pulsante **Verifica connessione** per ogni output.
1. Esaminare le [metriche di monitoraggio](stream-analytics-monitoring.md) nella scheda **Monitoraggio**. Poiché i valori sono aggregati, le metriche sono posticipate di alcuni minuti.

   * Se il valore di **Eventi di input** è maggiore di zero, il processo può leggere i dati di input. Se il valore di **Eventi di input** è minore di zero, l'input del processo presenta un problema. Per altre informazioni, vedere [Risolvere i problemi delle connessioni di input](stream-analytics-troubleshoot-input.md). Se il processo ha input di dati di riferimento, applicare la suddivisione in base al nome logico quando si esamina la metrica **degli eventi di input** . Se non sono presenti eventi di input solo dai dati di riferimento, probabilmente significa che questa origine di input non è configurata correttamente per recuperare il set di dati di riferimento a destra.
   * Se il valore di **Errori di conversione dati** è maggiore di zero e in aumento, vedere [Errori dei dati di Analisi di flusso di Azure](data-errors.md) per informazioni dettagliate sugli errori di conversione dei dati.
   * Se il valore di **Errori di runtime** è maggiore di zero, il processo riceve i dati ma genera errori durante l'elaborazione della query. Per trovare gli errori, andare ai [log di controllo](../azure-resource-manager/management/view-activity-logs.md) e applicare il filtro in base allo stato **Non riuscito**.
   * Se il valore di **Eventi di input** è maggiore di zero e il valore di **Eventi di output** è uguale a zero, una delle condizioni seguenti è vera:
      * L'elaborazione della query non ha prodotto eventi di output.
      * Il formato degli eventi o dei campi potrebbe non essere corretto e di conseguenza l'elaborazione della query non ha prodotto output.
      * Il processo non è riuscito a eseguire il push dei dati al sink di output per motivi di connettività o autenticazione.

   I messaggi del log operazioni specificano altri dettagli e la spiegazione della situazione, tranne nei casi in cui la logica della query abbia escluso tramite filtro tutti gli eventi. Se l'elaborazione di più eventi genera errori, gli errori vengono aggregati ogni 10 minuti.

## <a name="the-first-output-is-delayed"></a>Il primo output è ritardato

Quando viene avviato un processo di Analisi di flusso, vengono letti gli eventi di input. In alcune circostanze potrebbe tuttavia verificarsi un ritardo nell'output.

Valori di tempo di grandi dimensioni negli elementi di query temporali possono contribuire al ritardo dell'output. Per produrre l'output corretto su intervalli di tempo di grandi dimensioni, il processo di streaming esegue la lettura dei dati dal momento più recente possibile per riempire l'intervallo di tempo. I dati possono essere al massimo degli ultimi sette giorni. Non viene prodotto alcun output finché non vengono letti gli eventi di input in attesa. Questo problema può manifestarsi quando il sistema aggiorna i processi di streaming. Quando viene eseguito un aggiornamento, il processo viene riavviato. Questi aggiornamenti in genere vengono eseguiti una volta ogni due mesi.

Prestare attenzione durante la progettazione della query di Analisi di flusso. Se si usa un intervallo di tempo di grandi dimensioni per gli elementi temporali nella sintassi delle query del processo, ciò può causare un ritardo nel primo output quando il processo viene avviato o riavviato. Un intervallo superiore a qualche ora, fino a sette giorni, viene considerato un intervallo di tempo di grandi dimensioni.

Una possibile soluzione per questo tipo di ritardo di output consiste nell'uso di tecniche di parallelizzazione delle query come il partizionamento dei dati. In alternativa, è possibile aggiungere altre unità di streaming per migliorare la velocità effettiva fino all'aggiornamento del processo.  Per altre informazioni, vedere [Considerazioni per la creazione dei processi di Analisi di flusso](stream-analytics-concepts-checkpoint-replay.md).

I fattori seguenti influiscono sulla tempestività del primo output:

* Uso di funzioni di aggregazione finestra, ad esempio la clausola GROUP BY di finestre temporali a cascata, di salto e scorrevoli:

  * Per le aggregazioni con finestra a cascata o di salto, i risultati vengono generati alla fine dell'intervallo di tempo della finestra.
  * Per una finestra temporale scorrevole, i risultati vengono generati quando un evento entra o esce dalla finestra temporale scorrevole.
  * Se si prevede di usare una finestra di grandi dimensioni, ad esempio più di un'ora, è consigliabile scegliere una finestra di salto o scorrevole. Questi tipi di finestra consentono di visualizzare l'output con maggiore frequenza.

* Uso di join temporali, ad esempio JOIN con DATEDIFF:
  * Le corrispondenze vengono generate non appena arrivano entrambi i lati degli eventi corrispondenti.
  * I dati che non presentano una corrispondenza, ad esempio LEFT OUTER JOIN, vengono generati alla fine della finestra DATEDIFF per ogni evento sul lato sinistro.

* Uso di funzioni di analisi temporale, ad esempio ISFIRST, LAST e LAG con LIMIT DURATION:
  * Per le funzioni analitiche, l'output viene generato per ogni evento. Non vi è alcun ritardo.

## <a name="the-output-falls-behind"></a>Ritardo dell'output

Durante il normale funzionamento di un processo, l'output potrebbe avere periodi di latenza sempre più lunghi. Se l'output è in ritardo, è possibile individuare le cause principali esaminando i fattori indicati di seguito:

* Se viene applicata una limitazione al sink downstream
* Se viene applicata una limitazione al sink upstream
* Se la logica di elaborazione della query comporta un uso elevato delle risorse di calcolo

Per visualizzare i dettagli dell'output, nel portale di Azure selezionare il processo di streaming e scegliere **Diagramma del processo**. Per ogni input è presente una metrica dell'evento di backlog per partizione. Se la metrica continua ad aumentare, è un indicatore del fatto che le risorse del sistema sono vincolate. L'aumento potrebbe essere causato dalla limitazione del sink di output o dall'utilizzo elevato della CPU. Per altre informazioni, vedere [Debug guidato dai dati mediante il diagramma di processo](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Avviso di violazione della chiave con output del database SQL di Azure

Quando si configura un database SQL di Azure come output di un processo di Analisi di flusso, vengono eseguiti inserimenti bulk dei record nella tabella di destinazione. Analisi di flusso di Azure garantisce in genere [almeno un recapito](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) nel sink di output. È tuttavia possibile [ottenere esattamente un recapito]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) nell'output SQL quando per la tabella SQL viene definito un vincolo univoco.

Quando si configurano vincoli di chiave univoca nella tabella SQL, Analisi di flusso di Azure rimuove i record duplicati. Suddivide i dati in batch e inserisce in modo ricorsivo i batch finché non viene trovato un singolo record duplicato. Il processo di suddivisione e inserimento ignora i duplicati uno alla volta. Per un processo di streaming con molte righe duplicate, il processo è inefficiente e richiede molto tempo. Se vengono visualizzati più messaggi di avviso di violazione della chiave nel log attività dell'ora precedente, è probabile che l'output SQL stia rallentando l'intero processo.

Per risolvere questo problema, [configurare l'indice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) che causa la violazione della chiave, abilitando l'opzione IGNORE_DUP_KEY. Questa opzione consente a SQL di ignorare i valori duplicati durante gli inserimenti bulk. Il database SQL di Azure genera semplicemente un messaggio di avviso invece di un errore. Di conseguenza, Analisi di flusso di Azure non genera più errori di violazione della chiave primaria.

Quando si configura IGNORE_DUP_KEY per diversi tipi di indici, tenere presente le osservazioni seguenti:

* Non è possibile impostare IGNORE_DUP_KEY in una chiave primaria o in un vincolo univoco che usa ALTER INDEX. È necessario eliminare e ricreare l'indice.  
* È possibile impostare IGNORE_DUP_KEY usando ALTER INDEX per un indice univoco. Questa istanza è diversa rispetto a un vincolo PRIMARY KEY/UNIQUE e viene creata usando una definizione CREATE INDEX o INDEX.  
* L'opzione IGNORE_DUP_KEY non è valida per gli indici columnstore perché in essi non è possibile applicare l'univocità.  

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>I nomi delle colonne sono minuscoli in Analisi di flusso di Azure (1.0)

Quando si usa il livello di compatibilità originale (1.0), Analisi di flusso di Azure modifica i nomi di colonna in lettere minuscole. Questo comportamento è stato corretto nei livelli di compatibilità successivi. Per mantenere la distinzione tra maiuscole e minuscole, passare al livello di compatibilità 1.1 o versione successiva. Per altre informazioni, vedere [Livello di compatibilità per i processi di Analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).

## <a name="get-help"></a>Ottenere aiuto

Per maggiore supporto, provare la [Pagina delle domande di Domande e risposte Microsoft per Analisi di flusso di Azure](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
