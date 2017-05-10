---
title: Risoluzione dei problemi di Analisi di flusso di Azure mediante i log di diagnostica | Documentazione Microsoft
description: Informazioni su come analizzare i log di diagnostica dei processi di analisi di flusso in Microsoft Azure.
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 51f35173f386463eac0d5131e80534511bccbcd8
ms.contentlocale: it-it
ms.lasthandoff: 05/01/2017


---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Risoluzione dei problemi di Analisi di flusso di Azure mediante i log di diagnostica

In alcuni casi un processo di Analisi di flusso di Azure arresta l'elaborazione in modo imprevisto. È importante essere in grado di risolvere i problemi di questo tipo di eventi. Questo evento potrebbe essere causato da un risultato imprevisto della query, dalla connettività ai dispositivi o da un'interruzione imprevista del servizio. I log di diagnostica di Analisi di flusso possono essere utili per identificare la causa dei problemi quando si verificano e per ridurre i tempi di ripristino.

## <a name="log-types"></a>Tipi di log

Analisi di flusso offre due tipi di log: 
* [Log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (sempre attivi). I log attività forniscono informazioni dettagliate sulle operazioni eseguite sui processi.
* [Log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configurabili). I log di diagnostica forniscono informazioni più complete su tutto ciò che accade con un processo. I log di diagnostica vengono avviati quando viene creato il processo e terminano quando il processo viene eliminato. Comprendono gli eventi di quando il processo viene aggiornato e di quando è in fase di esecuzione.

> [!NOTE]
> Per analizzare i dati non conformi è possibile usare servizi come Archiviazione di Azure, Hub eventi di Azure e Azure Log Analytics. Gli addebiti avvengono in base al modello di determinazione dei prezzi per questi 
servizi.
>

## <a name="turn-on-diagnostics-logs"></a>Attivare i log di diagnostica

I log di diagnostica sono **disattivati** per impostazione predefinita. Per attivare i log di diagnostica, completare questi passaggi:

1.  Accedere al portale di Azure e passare al pannello Processo di streaming. In **Monitoraggio**selezionare **Log di diagnostica**.

    ![Navigazione tra i pannelli per trovare i log di diagnostica](./media/stream-analytics-job-diagnostic-logs/image1.png)  

2.  Selezionare **Attiva diagnostica**.

    ![Attivare i log di diagnostica](./media/stream-analytics-job-diagnostic-logs/image2.png)

3.  Nella pagina **Impostazioni di diagnostica** per **Stato**selezionare **Attivato**.

    ![Cambiare lo stato per i log di diagnostica](./media/stream-analytics-job-diagnostic-logs/image3.png)

4.  Impostare la destinazione di archiviazione (account di archiviazione, hub di eventi, Log Analytics) desiderata. Quindi selezionare le categorie di log da raccogliere (Esecuzione, Creazione). 

5.  Salvare poi la nuova configurazione di diagnostica.

La configurazione di diagnostica impiega circa 10 minuti per diventare effettiva. I log vengono poi visualizzati nella destinazione di archiviazione configurata (è possibile vederli nella pagina **Log di diagnostica**):

![Navigazione tra i pannelli per trovare i log di diagnostica - destinazioni di archiviazione](./media/stream-analytics-job-diagnostic-logs/image4.png)

Per altre informazioni sulla configurazione di diagnostica, vedere [Raccogliere e usare i dati di diagnostica dalle risorse di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>Categorie del log di diagnostica

Attualmente vengono acquisite due categorie di log di diagnostica:

* **Creazione**. Acquisisce eventi di log relativi alle operazioni di creazione dei processi: creazione di processi, aggiunta ed eliminazione di input e output, aggiunta e aggiornamento della query, avvio e arresto del processo.
* **Esecuzione**. Acquisisce gli eventi che si verificano durante l'esecuzione del processo:
    * Errori di connettività
    * Errori di elaborazione dei dati, fra cui:
        * Eventi non conformi alla definizione della query (valori e tipi di campo non corrispondenti, campi mancanti e così via)
        * Errori di valutazione delle espressioni
    * Altri eventi ed errori

## <a name="diagnostics-logs-schema"></a>Schema dei log di diagnostica

Tutti i log vengono archiviati in formato JSON. Ogni voce include i campi stringa comuni seguenti:

Nome | Descrizione
------- | -------
time | Timestamp del log (in UTC).
resourceId | ID della risorsa interessata dall'operazione, in lettere maiuscole. Include l'ID sottoscrizione, il gruppo di risorse e il nome del processo. Ad esempio, **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Categoria del log, ovvero **Execution** o **Authoring**.
operationName | Il nome dell'operazione registrata. Ad esempio **Send Events: SQL Output write failure to mysqloutput**.
status | Stato dell'operazione. Ad esempio **Failed** o **Succeeded**.
necessario | Il livello del log. Ad esempio **Error**, **Warning** o **Informational**.
properties | Dettagli specifici delle voci di log; serializzazione come stringa JSON. Per altre informazioni, vedere le sezioni seguenti.

### <a name="execution-log-properties-schema"></a>Schema delle proprietà dei log di esecuzione

I log di esecuzione hanno informazioni sugli eventi che si sono verificati durante l'esecuzione del processo di analisi di flusso. Lo schema della proprietà varia a seconda del tipo di evento. Attualmente i tipi di log di esecuzione sono i seguenti:

### <a name="data-errors"></a>Errori nei dati

Qualsiasi errore che si verifica durante il processo di elaborazione dei dati è in questa categoria di log. Questi log vengono creati più spesso durante le operazioni di lettura dei dati, serializzazione e scrittura. Questi log non includono errori di connettività. Gli errori di connettività vengono trattati come eventi generici.

Nome | Descrizione
------- | -------
Sorgente | Nome dell'input o dell'output del processo in cui si è verificato l'errore.
Message | Messaggio associato all'errore.
Tipo | Tipo di errore. Ad esempio **DataConversionError**, **CsvParserError** o **ServiceBusPropertyColumnMissingError**.
Dati | Dati utili per individuare con precisione l'origine dell'errore. Sono soggetti a troncamento in base alle dimensioni.

In base al valore **operationName**, lo schema degli errori nei dati è il seguente:
* **Eventi di serializzazione**. Gli eventi di serializzazione si verificano durante le operazioni di lettura degli eventi. Si verificano quando i dati in input non soddisfano lo schema di query per uno dei seguenti motivi:
    * *Mancata corrispondenza del tipo durante la (de)serializzazione dell'evento*: identifica il campo che causa l'errore.
    * *Impossibile leggere un evento, serializzazione non valida*: elenca le informazioni sulla posizione nei dati di input in cui si è verificato l'errore. Include il nome del BLOB per l'input del BLOB, l'offset e un campione dei dati.
* **Eventi di invio**. Gli eventi di invio si verificano durante le operazioni di scrittura. Identificano l'evento di streaming che ha causato l'errore.

### <a name="generic-events"></a>Eventi generici

Gli eventi generici sono tutti gli altri.

Nome | Descrizione
-------- | --------
Tipi di errore | (facoltativo) Informazioni sugli errori. Si tratta in genere di informazioni sulle eccezioni, se disponibili.
Message| Messaggio del log.
Tipo | Tipo di messaggio. Esegue il mapping alla categorizzazione interna degli errori. Ad esempio **JobValidationError** o **BlobOutputAdapterInitializationFailure**.
ID correlazione | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) che identifica in modo univoco l'esecuzione del processo. Tutte le voci del log di esecuzione dal momento dell'avvio del processo fino a quando il processo viene interrotto hanno lo stesso valore **ID correlazione**.

## <a name="next-steps"></a>Passaggi successivi

* [Presentazione di Analisi di flusso](stream-analytics-introduction.md)
* [Introduzione ad Analisi di flusso](stream-analytics-get-started.md)
* [Scalabilità dei processi di Analisi di flusso](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sull'API REST di gestione di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx)

