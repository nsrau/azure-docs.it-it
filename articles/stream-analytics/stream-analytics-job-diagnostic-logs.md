---
title: Log di diagnostica dell&quot;analisi di flusso di Azure | Documentazione Microsoft
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
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: f0292efd50721ef58028df778052eb0ed6fcda84
ms.openlocfilehash: 724eba50b7428b0012e8f062e264ce057e2a5287


---
# <a name="job-diagnostic-logs"></a>Log di diagnostica dei processi

## <a name="introduction"></a>Introduzione
L'analisi di flusso restituisce due tipi di log: 
* [Log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), che sono sempre abilitati e offrono informazioni approfondite sulle operazioni eseguite sui processi.
* [Log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), che sono configurabili dall'utente e offrono informazioni più approfondite su ciò che accade durante il processo, a partire da quando viene creato e aggiornato, mentre viene eseguito, fino a quando viene eliminato.

## <a name="how-to-enable-diagnostic-logs"></a>Come abilitare i log di diagnostica
I log di diagnostica sono **disattivati** per impostazione predefinita. Per abilitarli, seguire questa procedura:

Accedere al portale di Azure, passare al pannello del processo di streaming e usare il pannello "Log di diagnostica" in "Monitoraggio".

![navigazione al pannello dei log di diagnostica](./media/stream-analytics-job-diagnostic-logs/image1.png)  

Fare quindi clic sul collegamento "Attiva diagnostica".

![attivazione dei log di diagnostica](./media/stream-analytics-job-diagnostic-logs/image2.png)

Nella diagnostica aperta modificare lo stato su "Attivo".

![modifica dello stato dei log di diagnostica](./media/stream-analytics-job-diagnostic-logs/image3.png)

Configurare la destinazione di archiviazione desiderata (account di archiviazione, hub eventi, Log Analytics) e selezionare le categorie di log che si desidera raccogliere (esecuzione, creazione). Salvare quindi la nuova configurazione di diagnostica.

Dopo essere stata salvata, la configurazione richiederà circa 10 minuti per risultare attiva, quindi i log inizieranno a essere visualizzati nella destinazione di archiviazione configurata, visualizzabile nel pannello "Registri di diagnostica":

![navigazione al pannello dei log di diagnostica](./media/stream-analytics-job-diagnostic-logs/image4.png)

Altre informazioni sulla configurazione sono disponibili nella pagina relativa ai [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostic-logs-categories"></a>Categorie dei log di diagnostica
Esistono due categorie di log di diagnostica attualmente acquisiti:

* **Authoring** (Creazione). Vengono acquisiti i log relativi alle operazioni di creazione dei processi: creazione, aggiunta ed eliminazione di input e output, aggiunta e aggiornamento della query, avvio e arresto del processo.
* **Execution** (Esecuzione). Vengono acquisiti i log relativi a ciò che accade durante l'esecuzione del processo.
    * Errori di connettività
    * Errori di elaborazione dei dati
        * Eventi non conformi alla definizione della query (valori e tipi di campo non corrispondenti, campi mancanti e così via)
        * Errori di valutazione delle espressioni
    * e così via.

## <a name="diagnostic-logs-schema"></a>Schema dei log di diagnostica
Tutti i log vengono archiviati in formato JSON e per ogni voce sono presenti i seguenti campi stringa comuni:

Nome | Descrizione
------- | -------
time | Il timestamp del log (fusorario UTC)
resourceId | L'ID della risorsa interessata dall'operazione, in lettere maiuscole. Include l'ID sottoscrizione, il gruppo di risorse e il nome del processo. Ad esempio: `/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB`
category | La categoria del log, ovvero `Execution` o `Authoring`
operationName | Il nome dell'operazione registrata. Ad esempio: `Send Events: SQL Output write failure to mysqloutput`
status | Lo stato dell'operazione. Ad esempio: `Failed, Succeeded`.
necessario | Il livello del log. Ad esempio: `Error, Warning, Informational`
properties | Dettagli specifici delle voci di log; serializzazione come stringa JSON. Per altri dettagli vedere di seguito

### <a name="execution-logs-properties-schema"></a>Schema delle proprietà dei log di esecuzione
I log di esecuzione contengono informazioni sugli eventi che si sono verificati durante l'esecuzione del processo di analisi di flusso.
In base al tipo di eventi, lo schema delle proprietà sarà diverso. Attualmente sono disponibili i seguenti tipi:

### <a name="data-errors"></a>Errori nei dati
Qualsiasi errore di elaborazione dei dati rientra in questa categoria di log. Si verificano principalmente durante le operazioni di lettura, serializzazione e scrittura dei dati. In questa categoria non rientrano gli errori di connettività, che vengono considerati come eventi generici.

Nome | Descrizione
------- | -------
Sorgente | Nome dell'input o dell'output del processo in cui si è verificato l'errore.
Message | Messaggio associato all'errore.
Tipo | Tipo di errore. Ad esempio, `DataConversionError, CsvParserError, ServiceBusPropertyColumnMissingError` e così via.
Dati | Dati utili per individuare con precisione l'origine dell'errore. Sono soggetti a troncamento in base alle dimensioni.

In base al valore **operationName**, lo schema degli errori nei dati sarà il seguente:
* **Eventi di serializzazione** - avvengono durante le operazioni di lettura degli eventi, se i dati nell'input non soddisfano lo schema di query:
    * Mancata corrispondenza del tipo durante la (de)serializzazione dell'evento: campo che causa l'errore.
    * Non è possibile leggere un evento e la serializzazione non è valida: informazioni sul punto in cui si è verificato l'errore nei dati di input: nome BLOB per l'input del BLOB, differenza ed esempio dei dati.
* **Eventi di invio** - operazioni di scrittura: flusso dell'evento che ha causato l'errore.

### <a name="generic-events"></a>Eventi generici
Tutti gli altri elementi

Nome | Descrizione
-------- | --------
Tipi di errore | (facoltativo) Informazioni sull'errore, in genere le informazioni sull'eccezione, se disponibili.
Message| Messaggio del log.
Tipo | Tipo di messaggio, associato alla categorizzazione interna degli errori: ad esempio, JobValidationError, BlobOutputAdapterInitializationFailure e così via.
ID correlazione | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) che identifica in modo univoco l'esecuzione del processo. Tutte le voci del log di esecuzione prodotte tra l'avvio e l'arresto del processo avranno lo stesso "ID correlazione".



## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


