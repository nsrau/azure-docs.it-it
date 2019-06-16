---
title: Risoluzione dei problemi di Analisi di flusso di Azure tramite i log di diagnostica
description: Questo articolo descrive come analizzare i log di diagnostica in Analisi di flusso di Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ff2930fbe0e53c4b3c1223f87919c0913296d07c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66515930"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Risoluzione dei problemi di Analisi di flusso di Azure mediante i log di diagnostica

In alcuni casi un processo di Analisi di flusso di Azure arresta l'elaborazione in modo imprevisto. È importante essere in grado di risolvere i problemi di questo tipo di eventi. Gli errori potrebbero essere causati da un risultato imprevisto della query, dalla connettività ai dispositivi o da un'interruzione imprevista del servizio. I log di diagnostica di Analisi di flusso possono essere utili per identificare la causa dei problemi quando si verificano e per ridurre i tempi di ripristino.

## <a name="log-types"></a>Tipi di log

Analisi di flusso offre due tipi di log:

* I [Log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (Always on) forniscono informazioni dettagliate sulle operazioni eseguite sui processi.

* I [Log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configurabili) forniscono informazioni più complete su tutto ciò che accade con un processo. I log di diagnostica vengono avviati quando viene creato il processo e terminati quando il processo viene eliminato. Comprendono gli eventi di quando il processo viene aggiornato e di quando è in fase di esecuzione.

> [!NOTE]
> È possibile usare servizi come archiviazione di Azure, hub eventi di Azure e log di monitoraggio di Azure per analizzare i dati non conformi. Gli addebiti avvengono in base al modello di determinazione dei prezzi per questi servizi.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Debug con l'uso dei log attività

I log attività sono attivati per impostazione predefinita e forniscono informazioni dettagliate sulle operazioni eseguite dal processo di Analisi di flusso. Le informazioni presenti nei log attività possono essere utili per individuare la causa radice dei problemi che si manifestano nel processo. Per usare i log attività in Analisi di flusso, eseguire i passaggi seguenti:

1. Accedere al portale di Azure e selezionare **Log attività** in **Panoramica**.

   ![Log attività di Analisi di flusso](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. È possibile visualizzare un elenco delle operazioni che sono state eseguite. Qualsiasi operazione che ha causato l'esito negativo del processo viene contrassegnata da una bolla rossa.

3. Fare clic su un'operazione per visualizzare il riepilogo. Generalmente, le informazioni qui sono limitate. Per informazioni dettagliate sull'operazione, fare clic su **JSON**.

   ![Riepilogo dell'operazione del Log attività di Analisi di flusso](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Scorrere verso il basso fino alla sezione **pannello Proprietà** di JSON, che fornisce i dettagli dell'errore che ha causato il fallimento dell'operazione. In questo esempio, l'errore era dovuto al superamento dei valori della latitudine associata di Common Language Runtime. Discrepanza nei dati che viene elaborati da un processo di Stream Analitica provoca un errore nei dati. È possibile ottenere informazioni diverse [dati di input e output degli errori e il motivo per cui si svolgono](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![Dettagli dell'errore JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Si possono eseguire azioni correttive in base al messaggio di errore in JSON. In questo esempio, controlla che il valore di latitudine sia compreso tra -90 gradi e 90 gradi devono essere aggiunti alla query.

6. Se il messaggio di errore nei log attività non è utile per identificare causa radice, abilitare i log di diagnostica e usare i log di monitoraggio di Azure.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Invio della diagnostica a log di monitoraggio di Azure

È altamente consigliabile attivando i log di diagnostica e li invia al log di monitoraggio di Azure. I log di diagnostica sono **disattivati** per impostazione predefinita. Per attivare i log di diagnostica, completare questi passaggi:

1.  Accedere al portale di Azure e andare al processo di Analisi di flusso. In **Monitoraggio**selezionare **Log di diagnostica**. Selezionare quindi **Attiva diagnostica**.

    ![Navigazione tra i pannelli per trovare i log di diagnostica](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Creare un **Nome** in **Impostazioni di diagnostica** e selezionare la casella accanto a **Invia a Log Analytics**. Quindi aggiungere o creare una nuova **area di lavoro Log Analytics**. Selezionare le caselle **Esecuzione** e **Creazione** in **LOG**, e **AllMetrics** in **METRICA**. Fare clic su **Save**.

    ![Impostare i log di diagnostica](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Quando viene avviato il processo di Analisi di flusso, i log di diagnostica vengono indirizzati all'area di lavoro Log Analytics. Passare all'area di lavoro Log Analytics e scegliere **Log** all'interno della sezione **Generale**.

   ![Log di monitoraggio di Azure nella sezione generale](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. È possibile [digitare la propria query](../azure-monitor/log-query/get-started-portal.md) per cercare termini, identificare le tendenze, analizzare i modelli e ottenere informazioni dettagliate basate sui dati. Ad esempio, è possibile scrivere una query per filtrare solo i log di diagnostica con il messaggio "Processo di streaming non riuscito". I log di diagnostica da Analisi di flusso di Azure vengono archiviati nella tabella **AzureDiagnostics**.

   ![Query di diagnostica e risultati](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. Quando si dispone di una query che ricerca i log corretti, salvarla selezionando **Save** e fornire un nome e una categoria. È quindi possibile creare un avviso selezionando **Nuova regola di avviso**. Successivamente, definire la condizione di avviso. Selezionare **Condizione** e immettere il valore di soglia e la frequenza con cui viene valutata la ricerca personalizzata dei log.  

   ![Query di ricerca dei log di diagnostica](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. Scegliere il gruppo di azioni e specificare i dettagli dell'avviso, ad esempio nome e descrizione, prima di poter creare la regola di avviso. È possibile indirizzare i log di diagnostica dei diversi processi per la stessa area di lavoro Log Analytics. In questo modo è possibile impostare gli avvisi una sola volta per tutti i processi.  

## <a name="diagnostics-log-categories"></a>Categorie del log di diagnostica

Azure Stream Analitica acquisisce due categorie di log di diagnostica:

* **Creazione**: Acquisisce eventi di log relativi alle operazioni di creazione dei processi, tra cui creazione di processi, aggiunta ed eliminazione di input e output, aggiunta e aggiornamento della query, e avvio o arresto del processo.

* **Execution** (Esecuzione): Acquisisce gli eventi che si verificano durante l'esecuzione del processo.
    * Errori di connettività
    * Errori di elaborazione dei dati, fra cui:
        * Eventi non conformi alla definizione della query (valori e tipi di campo non corrispondenti, campi mancanti e così via)
        * Errori di valutazione delle espressioni
    * Altri eventi ed errori

## <a name="diagnostics-logs-schema"></a>Schema dei log di diagnostica

Tutti i log vengono archiviati in formato JSON. Ogni voce include i campi stringa comuni seguenti:

NOME | DESCRIZIONE
------- | -------
time | Timestamp del log (in UTC).
resourceId | ID della risorsa interessata dall'operazione, in lettere maiuscole. Include l'ID sottoscrizione, il gruppo di risorse e il nome del processo. Ad esempio, **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Categoria del log, ovvero **Execution** o **Authoring**.
operationName | Il nome dell'operazione registrata. Per esempio, **Inviare eventi: SQL Output scrive un errore in mysqloutput**.
status | Stato dell'operazione. Ad esempio **Failed** o **Succeeded**.
level | Il livello del log. Ad esempio **Error**, **Warning** o **Informational**.
properties | Dettagli specifici delle voci di log; serializzazione come stringa JSON. Per altre informazioni, vedere le sezioni seguenti in questo articolo.

### <a name="execution-log-properties-schema"></a>Schema delle proprietà dei log di esecuzione

I log di esecuzione hanno informazioni sugli eventi che si sono verificati durante l'esecuzione del processo di analisi di flusso. Lo schema delle proprietà varia a seconda che l'evento sia un errore nei dati o un evento generico.

### <a name="data-errors"></a>Errori nei dati

Qualsiasi errore che si verifica durante il processo di elaborazione dei dati è in questa categoria di log. Questi log vengono creati più spesso durante le operazioni di lettura dei dati, serializzazione e scrittura. Questi log non includono errori di connettività. Gli errori di connettività vengono trattati come eventi generici.

NOME | DESCRIZIONE
------- | -------
Source (Sorgente) | Nome dell'input o dell'output del processo in cui si è verificato l'errore.
Message | Messaggio associato all'errore.
Type | Tipo di errore. Ad esempio **DataConversionError**, **CsvParserError** o **ServiceBusPropertyColumnMissingError**.
Dati | Dati utili per individuare con precisione l'origine dell'errore. Sono soggetti a troncamento in base alle dimensioni.

In base al valore **operationName**, lo schema degli errori nei dati è il seguente:

* **Eventi di serializzazione** si verificano durante le operazioni di lettura eventi. Si verificano quando i dati in input non soddisfano lo schema di query per uno dei seguenti motivi:

   * *Tipo non corrispondente durante la (de)serializzazione dell'evento*: identifica il campo che causa l'errore.

   * *Impossibile leggere un evento, serializzazione non valida*: elenca le informazioni sulla posizione nei dati di input in cui si è verificato l'errore. Include il nome del BLOB per l'input del BLOB, l'offset e un campione dei dati.

* **Inviare eventi** si verificano durante operazioni di scrittura. Identificano l'evento di streaming che ha causato l'errore.

### <a name="generic-events"></a>Eventi generici

Gli eventi generici sono tutti gli altri.

NOME | DESCRIZIONE
-------- | --------
Tipi di errore | (facoltativo) Informazioni sugli errori. In genere, quando disponibili, si tratta di informazioni sulle eccezioni.
Message| Messaggio del log.
Type | Tipo di messaggio. Esegue il mapping alla categorizzazione interna degli errori. Ad esempio **JobValidationError** o **BlobOutputAdapterInitializationFailure**.
ID correlazione | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) che identifica in modo univoco l'esecuzione del processo. Tutte le voci del log di esecuzione dal momento dell'avvio del processo fino a quando il processo viene interrotto hanno lo stesso valore **ID correlazione**.

## <a name="next-steps"></a>Passaggi successivi

* [Presentazione di Analisi di flusso](stream-analytics-introduction.md)
* [Introduzione ad Analisi di flusso](stream-analytics-real-time-fraud-detection.md)
* [Scalabilità dei processi di Analisi di flusso](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sull'API REST di gestione di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx)
