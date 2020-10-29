---
title: Risolvere i problemi di analisi di flusso di Azure usando i log
description: Questo articolo descrive come analizzare i log delle risorse in analisi di flusso di Azure.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.custom: contperfq1
ms.date: 06/18/2020
ms.openlocfilehash: 18270a2f435428824714067749fc18ce2addc535
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913042"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>Risolvere i problemi di Analisi di flusso di Azure usando i log delle risorse

In alcuni casi un processo di Analisi di flusso di Azure arresta l'elaborazione in modo imprevisto. È importante essere in grado di risolvere i problemi di questo tipo di eventi. Gli errori potrebbero essere causati da un risultato imprevisto della query, dalla connettività ai dispositivi o da un'interruzione imprevista del servizio. I log delle risorse in analisi di flusso consentono di identificare la fonte di problemi quando si verificano e di ridurre il tempo di recupero.

Si consiglia vivamente di abilitare i log delle risorse per tutti i processi, in quanto ciò consentirà di semplificare il debug e il monitoraggio.

## <a name="log-types"></a>Tipi di log

Analisi di flusso offre due tipi di log:

* I [Log attività](../azure-monitor/platform/platform-logs-overview.md) (Always on) forniscono informazioni dettagliate sulle operazioni eseguite sui processi.

* [Log delle risorse](../azure-monitor/platform/platform-logs-overview.md) (configurabili), che forniscono informazioni dettagliate su tutto ciò che accade in un processo. I log delle risorse iniziano quando il processo viene creato e terminano quando il processo viene eliminato. Comprendono gli eventi di quando il processo viene aggiornato e di quando è in fase di esecuzione.

> [!NOTE]
> È possibile usare servizi come archiviazione di Azure, Hub eventi di Azure e log di monitoraggio di Azure per analizzare i dati non conformi. Gli addebiti avvengono in base al modello di determinazione dei prezzi per questi servizi.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Debug con l'uso dei log attività

I log attività sono attivati per impostazione predefinita e forniscono informazioni dettagliate sulle operazioni eseguite dal processo di Analisi di flusso. Le informazioni presenti nei log attività possono essere utili per individuare la causa radice dei problemi che si manifestano nel processo. Per usare i log attività in Analisi di flusso, eseguire i passaggi seguenti:

1. Accedere al portale di Azure e selezionare **Log attività** in **Panoramica** .

   ![Log attività di Analisi di flusso](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. È possibile visualizzare un elenco delle operazioni che sono state eseguite. Qualsiasi operazione che ha causato l'esito negativo del processo viene contrassegnata da una bolla rossa.

3. Fare clic su un'operazione per visualizzare il riepilogo. Generalmente, le informazioni qui sono limitate. Per informazioni dettagliate sull'operazione, fare clic su **JSON** .

   ![Riepilogo dell'operazione del Log attività di Analisi di flusso](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Scorrere verso il basso fino alla sezione **pannello Proprietà** di JSON, che fornisce i dettagli dell'errore che ha causato il fallimento dell'operazione. In questo esempio, l'errore era dovuto al superamento dei valori della latitudine associata di Common Language Runtime. La discrepanza nei dati elaborati da un processo di analisi di flusso causa un errore di dati. È possibile ottenere informazioni sui diversi [errori di dati di input e di output e sul motivo](https://docs.microsoft.com/azure/stream-analytics/data-errors)per cui si verificano.

   ![Dettagli dell'errore JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Si possono eseguire azioni correttive in base al messaggio di errore in JSON. In questo esempio, controlla che il valore di latitudine sia compreso tra -90 gradi e 90 gradi devono essere aggiunti alla query.

6. Se il messaggio di errore nei log attività non è utile per identificare la causa principale, abilitare i log delle risorse e usare i log di monitoraggio di Azure.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Inviare dati diagnostici ai log di monitoraggio di Azure

È consigliabile attivare i log delle risorse e inviarli ai log di monitoraggio di Azure. Sono **disattivate** per impostazione predefinita. Per attivarli, attenersi alla procedura seguente:

1.  Creare un'area di lavoro Log Analytics se non ne è già presente uno. Si consiglia di usare l'area di lavoro Log Analytics nella stessa area del processo di analisi di flusso.

2.  Accedere al portale di Azure e andare al processo di Analisi di flusso. In **Monitoraggio** selezionare **Log di diagnostica** . Selezionare quindi **Attiva diagnostica** .

    ![Spostamento dei pannelli nei log delle risorse](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Specificare un **nome** nel **nome delle impostazioni di diagnostica** e selezionare le caselle per l' **esecuzione** e la **creazione** in **log** e **AllMetrics** in **metrica** . Selezionare quindi **Invia a log Analytics** e scegliere l'area di lavoro. Fare clic su **Salva** .

    ![Impostazioni per i log delle risorse](./media/stream-analytics-job-diagnostic-logs/logs-setup.png)

3. All'avvio del processo di analisi di flusso, i log delle risorse vengono indirizzati all'area di lavoro Log Analytics. Per visualizzare i log delle risorse per il processo, selezionare **log** nella sezione **monitoraggio** .

   ![Screenshot Visualizza il menu generale con i log selezionati.](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Analisi di flusso offre query predefinite che consentono di cercare facilmente i log a cui si è interessati. È possibile selezionare le query predefinite nel riquadro a sinistra e quindi selezionare **Esegui** . I risultati della query vengono visualizzati nel riquadro inferiore. 

   ![Screenshot mostra i log per un processo di analisi di flusso.](./media/stream-analytics-job-diagnostic-logs/logs-example.png)

## <a name="resource-log-categories"></a>Categorie di log delle risorse

Analisi di flusso di Azure acquisisce due categorie di log di risorse:

* **Creazione e modifica** : acquisisce eventi di log relativi alle operazioni di creazione di processi, ad esempio la creazione di processi, l'aggiunta e l'eliminazione di input e output, l'aggiunta e l'aggiornamento della query e l'avvio o l'arresto del processo.

* **Esecuzione** : acquisisce gli eventi che si verificano durante l'esecuzione del processo.
    * Errori di connettività
    * Errori di elaborazione dei dati, fra cui:
        * Eventi non conformi alla definizione della query (valori e tipi di campo non corrispondenti, campi mancanti e così via)
        * Errori di valutazione delle espressioni
    * Altri eventi ed errori

## <a name="resource-logs-schema"></a>Schema dei log delle risorse

Tutti i log vengono archiviati in formato JSON. Ogni voce include i campi stringa comuni seguenti:

Nome | Descrizione
------- | -------
time | Timestamp del log (in UTC).
resourceId | ID della risorsa interessata dall'operazione, in lettere maiuscole. Include l'ID sottoscrizione, il gruppo di risorse e il nome del processo. Ad esempio, **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB** .
category | Categoria del log, ovvero **Execution** o **Authoring** .
operationName | Il nome dell'operazione registrata. Ad esempio, **Send Events: SQL output Write failure to mysqloutput** .
status | Stato dell'operazione. Ad esempio **Failed** o **Succeeded** .
livello | Il livello del log. Ad esempio **Error** , **Warning** o **Informational** .
properties | Dettagli specifici delle voci di log; serializzazione come stringa JSON. Per altre informazioni, vedere le sezioni seguenti in questo articolo.

### <a name="execution-log-properties-schema"></a>Schema delle proprietà dei log di esecuzione

I log di esecuzione hanno informazioni sugli eventi che si sono verificati durante l'esecuzione del processo di analisi di flusso. Lo schema delle proprietà varia a seconda che si tratti di un errore di dati o di un evento generico.

### <a name="data-errors"></a>Errori nei dati

Qualsiasi errore che si verifica durante il processo di elaborazione dei dati è in questa categoria di log. Questi log vengono creati più spesso durante le operazioni di lettura dei dati, serializzazione e scrittura. Questi log non includono errori di connettività. Gli errori di connettività vengono trattati come eventi generici. È possibile ottenere altre informazioni sulla cause di diversi [errori di dati di input e di output](https://docs.microsoft.com/azure/stream-analytics/data-errors).

Nome | Descrizione
------- | -------
Source (Sorgente) | Nome dell'input o dell'output del processo in cui si è verificato l'errore.
Message | Messaggio associato all'errore.
Type | Tipo di errore. Ad esempio **DataConversionError** , **CsvParserError** o **ServiceBusPropertyColumnMissingError** .
Data | Dati utili per individuare con precisione l'origine dell'errore. Sono soggetti a troncamento in base alle dimensioni.

In base al valore **operationName** , lo schema degli errori nei dati è il seguente:

* **Serializzare gli eventi** durante le operazioni di lettura dell'evento. Si verificano quando i dati in input non soddisfano lo schema di query per uno dei seguenti motivi:

   * *Mancata corrispondenza del tipo durante la (de)serializzazione dell'evento* : identifica il campo che causa l'errore.

   * *Impossibile leggere un evento, serializzazione non valida* : elenca le informazioni sulla posizione nei dati di input in cui si è verificato l'errore. Include il nome del BLOB per l'input del BLOB, l'offset e un campione dei dati.

* **Gli eventi Send** si verificano durante le operazioni di scrittura. Identificano l'evento di streaming che ha causato l'errore.

### <a name="generic-events"></a>Eventi generici

Gli eventi generici sono tutti gli altri.

Nome | Descrizione
-------- | --------
Errore | (facoltativo) Informazioni sugli errori. In genere, quando disponibili, si tratta di informazioni sulle eccezioni.
Message| Messaggio del log.
Type | Tipo di messaggio. Esegue il mapping alla categorizzazione interna degli errori. Ad esempio **JobValidationError** o **BlobOutputAdapterInitializationFailure** .
ID correlazione | GUID che identifica in modo univoco l'esecuzione del processo. Tutte le voci del log di esecuzione dal momento dell'avvio del processo fino a quando il processo viene interrotto hanno lo stesso valore **ID correlazione** .

## <a name="next-steps"></a>Passaggi successivi

* [Errori dei dati di analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/data-errors)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
