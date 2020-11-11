---
title: Risolvere i problemi delle query di Analisi di flusso di Azure
description: Questo articolo descrive alcune tecniche per la risoluzione dei problemi delle query nei processi di Analisi di flusso di Azure.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: ef03560cff704255d2779a747d124e0b39a1c657
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491308"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Risolvere i problemi delle query di Analisi di flusso di Azure

Questo articolo descrive problemi comuni di sviluppo delle query di Analisi di flusso di Azure e la risoluzione di questi.

Questo articolo descrive i problemi comuni relativi allo sviluppo di query di Analisi di flusso di Azure, come risolvere i problemi di query e come correggere i problemi. Molti passaggi per la risoluzione dei problemi richiedono l'abilitazione dei log delle risorse per il processo di Analisi di flusso. Se i log delle risorse non sono abilitati, vedere [Risolvere i problemi di Analisi di flusso di Azure usando i log delle risorse](stream-analytics-job-diagnostic-logs.md).

## <a name="query-is-not-producing-expected-output"></a>La query non genera l'output previsto

1.  Esaminare gli errori eseguendo un test locale:

    - Nel portale di Azure selezionare **Test** nella scheda **Query**. [Testare la query](stream-analytics-test-query.md) usando i dati di esempio scaricati. Esaminare eventuali errori e provare a risolverli.   
    - È anche possibile [testare la query localmente](stream-analytics-live-data-local-testing.md) usando gli strumenti di Analisi di flusso di Azure per Visual Studio o [Visual Studio Code](visual-studio-code-local-run-live-input.md). 

2.  [Eseguire il debug delle query in locale usando il diagramma dei processi](debug-locally-using-job-diagram-vs-code.md) negli strumenti di analisi di flusso di Azure per Visual Studio Code. Il diagramma del processo mostra i dati che fluiscono dalle origini di input (hub eventi, hub IoT e così via) attraversano più passaggi della query e infine giungono ai sink di output. Ogni passaggio della query è mappato a un set di risultati temporaneo definito nello script usando l'istruzione WITH. Per individuare l'origine del problema, è possibile visualizzare i dati e le metriche in ogni set di risultati intermedio.

    ![Anteprima dei risultati nel diagramma di processo](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

3.  Se si usa [**Timestamp By**](/stream-analytics-query/timestamp-by-azure-stream-analytics), assicurarsi che i timestamp degli eventi siano successivi all' [ora di inizio del processo](./stream-analytics-time-handling.md).

4.  Eliminare i problemi comuni, ad esempio:
    - Una clausola [**WHERE**](/stream-analytics-query/where-azure-stream-analytics) della query ha filtrato tutti gli eventi impedendo la generazione dell'output.
    - Una funzione [**CAST**](/stream-analytics-query/cast-azure-stream-analytics) non riesce, causando l'esito negativo del processo. Per evitare errori di cast di tipo, usare [**TRY_CAST**](/stream-analytics-query/try-cast-azure-stream-analytics).
    - Quando si usano le funzioni finestra, attendere la durata dell'intera finestra per vedere l'output dalla query.
    - Il timestamp per gli eventi precede l'ora di inizio del processo e gli eventi vengono eliminati.
    - Le condizioni di [**JOIN**](/stream-analytics-query/join-azure-stream-analytics) non corrispondono. Se non sono presenti corrispondenze, non verrà restituito output.

5.  Assicurarsi che i criteri di ordinamento degli eventi siano configurati come previsto. Passare a **Impostazioni** e selezionare [**Ordinamento eventi**](./stream-analytics-time-handling.md). Il criterio *non* viene applicato quando si usa il pulsante **Test** per testare la query. Questo aspetto rappresenta una differenza tra il test nel browser e l'esecuzione del processo in produzione. 

6. Eseguire il debug usando i log di attività e risorse:
    - Usare i [log attività](../azure-resource-manager/management/view-activity-logs.md) e filtrare per identificare gli errori ed eseguirne il debug.
    - Usare i [log delle risorse del processo](stream-analytics-job-diagnostic-logs.md) per identificare gli errori ed eseguirne il debug.

## <a name="resource-utilization-is-high"></a>L'utilizzo delle risorse è elevato

Assicurarsi di sfruttare i vantaggi della parallelizzazione in Analisi di flusso di Azure. È possibile usare il [ridimensionamento con la parallelizzazione delle query](stream-analytics-parallelization.md) dei processi di Analisi di flusso configurando partizioni di input e ottimizzando la definizione delle query di analisi.

Se l'utilizzo delle risorse è costantemente superiore al 80%, il ritardo della filigrana è in aumento e il numero di eventi backlog è in aumento, è consigliabile aumentare le unità di streaming. Un utilizzo elevato indica che il processo sta usando vicino al numero massimo di risorse allocate.

## <a name="debug-queries-progressively"></a>Eseguire il debug progressivo delle query

Nell'elaborazione dei dati in tempo reale può essere utile conoscere l'aspetto dei dati nel mezzo di una query. È possibile visualizzarlo usando il diagramma del processo in Visual Studio. Se non si ha Visual Studio, è possibile eseguire altri passaggi per l'output dei dati intermedi.

Poiché gli input o i passaggi di un processo di Analisi di flusso di Azure possono essere letti più volte, è possibile scrivere istruzioni SELECT INTO aggiuntive. In questo modo vengono generati dati intermedi nella risorsa di archiviazione che consentono di controllare la correttezza dei dati, esattamente come fanno le *variabili di controllo* quando si esegue il debug di un programma.

La seguente query di esempio in un processo di Analisi di flusso di Azure ha un input di flusso, due input di dati di riferimento e un output in Archiviazione tabelle di Azure. La query unisce i dati dell'hub eventi e di due BLOB di riferimento per ottenere le informazioni di nome e categoria:

![Esempio di Analisi di flusso SELECT nella query](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Si noti che il processo è in esecuzione ma non vengono generati eventi nell'output. Nel riquadro **Monitoraggio** , illustrato di seguito, è possibile vedere che l'input produce dati, ma non sa quale passaggio del **JOIN** ha causato l'eliminazione di tutti gli eventi.

![Riquadro Monitoraggio di Analisi di flusso](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

In questa situazione è possibile aggiungere alcune istruzioni SELECT INTO aggiuntive per "registrare" i risultati intermedi di JOIN e i dati che vengono letti dall'input.

In questo esempio sono stati aggiunti due nuovi "output temporanei". Può trattarsi di qualsiasi sink desiderato. Qui si usa Archiviazione di Azure come esempio:

![Aggiunta di istruzioni SELECT INTO aggiuntive alla query analisi di flusso](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

È quindi possibile riscrivere la query come segue:

![Query di Analisi di flusso SELECT INTO riscritta](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Ora avviare nuovamente il processo e lasciarlo in esecuzione per alcuni minuti. Quindi eseguire una query su temp1 e temp2 con Visual Studio Cloud Explorer per generare le tabelle seguenti:

**tabella temp1**
![query di analisi di flusso della tabella temp1 SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**tabella temp2**
![query di analisi di flusso della tabella temp2 SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Come si può vedere, temp1 e temp2 hanno entrambe dati e la colonna del nome viene popolata in modo corretto in temp2. Tuttavia, poiché non sono ancora presenti dati nell'output, c'è qualcosa di sbagliato:

![Tabella SELECT INTO output1 senza dati di query di analisi di flusso](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Tramite il campionamento dei dati, è possibile essere quasi certi che il problema riguardi il secondo JOIN. È possibile scaricare i dati di riferimento dal BLOB e dare un'occhiata:

![Query di analisi di flusso della tabella di riferimento SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Come si può notare, il formato del GUID in questi dati di riferimento è diverso dal formato della colonna [da] di temp2. Ecco perché i dati non arrivavano in output1 come previsto.

È possibile correggere il formato dei dati, caricarlo nel BLOB di riferimento e riprovare:

![Query di analisi di flusso della tabella temporanea SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Questa volta i dati nell'output vengono formattati e popolati come previsto.

![Query di analisi di flusso della tabella finale SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Ottenere aiuto

Per maggiore supporto, provare la [Pagina delle domande di Domande e risposte Microsoft per Analisi di flusso di Azure](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](/rest/api/streamanalytics/)
