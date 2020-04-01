---
title: Risolvere i problemi delle query di Analisi di flusso di Azure
description: Questo articolo descrive alcune tecniche per la risoluzione dei problemi delle query nei processi di Analisi di flusso di Azure.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: f049dc6d1261a8201cf79d1779e522b30d13c4b0
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409448"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Risolvere i problemi delle query di Analisi di flusso di Azure

Questo articolo descrive problemi comuni di sviluppo delle query di Analisi di flusso di Azure e la risoluzione di questi.

Questo articolo descrive i problemi comuni relativi allo sviluppo di query di Analisi di flusso di Azure, come risolvere i problemi di query e come correggerli. Molti passaggi per la risoluzione dei problemi richiedono l'abilitazione dei log di diagnostica per il processo di Analisi di flusso. Se i log di diagnostica non sono abilitati, vedere Risolvere i problemi di Analisi di flusso di Azure usando i log di [diagnostica.](stream-analytics-job-diagnostic-logs.md)

## <a name="query-is-not-producing-expected-output"></a>La query non genera l'output previsto

1.  Esaminare gli errori eseguendo un test locale:

    - Nella scheda **Query** del portale di Azure selezionare **Test**. [Testare la query](stream-analytics-test-query.md) usando i dati di esempio scaricati. Esaminare eventuali errori e provare a risolverli.   
    - È anche possibile [testare la query in locale](stream-analytics-live-data-local-testing.md) usando gli strumenti di Analisi di flusso di Azure per Visual Studio o Visual Studio [Code.](visual-studio-code-local-run-live-input.md) 

2.  [Eseguire il debug delle query passo dopo passo in locale usando](debug-locally-using-job-diagram.md) il diagramma dei processi negli strumenti di Analisi di flusso di Azure per Visual Studio.Debug queries step by step locally using job diagram in Azure Stream Analytics tools for Visual Studio. Il diagramma dei processi mostra come i dati passano dalle origini di input (Hub eventi, hub IoT e così via) tramite più passaggi di query e infine nei sink di output. Ogni passaggio di query viene mappato a un set di risultati temporaneo definito nello script utilizzando l'istruzione WITH. È possibile visualizzare i dati, nonché le metriche, in ogni set di risultati intermedio per trovare l'origine del problema.

    ![Risultato dell'anteprima del diagramma di processo](./media/debug-locally-using-job-diagram/preview-result.png)

3.  Se si usa [**Timestamp By**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), assicurarsi che i timestamp degli eventi siano successivi all'[ora di inizio del processo](stream-analytics-out-of-order-and-late-events.md).

4.  Eliminare i problemi comuni, ad esempio:
    - Una clausola [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) della query ha filtrato tutti gli eventi impedendo la generazione dell'output.
    - Una funzione [**CAST**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) non riesce, causando l'esito negativo del processo. Per evitare errori di cast di tipo, usare [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics).
    - Quando si usano le funzioni finestra, attendere la durata dell'intera finestra per vedere l'output dalla query.
    - Il timestamp per gli eventi precede l'ora di inizio del processo e gli eventi vengono eliminati.
    - [**Le**](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) condizioni JOIN non corrispondono. Se non ci sono corrispondenze, non ci sarà alcun output.

5.  Assicurarsi che i criteri di ordinamento degli eventi siano configurati come previsto. Vai a **Impostazioni** e seleziona [**Ordine eventi**](stream-analytics-out-of-order-and-late-events.md). Il criterio *non* viene applicato quando si usa il pulsante **Test** per testare la query. Questo aspetto rappresenta una differenza tra il test nel browser e l'esecuzione del processo in produzione. 

6. Eseguire il debug usando i log di controllo e diagnostica:
    - Usare i [log di controllo](../azure-resource-manager/resource-group-audit.md) e filtrare per identificare gli errori ed eseguirne il debug.
    - Usare i [log di diagnostica del processo](stream-analytics-job-diagnostic-logs.md) per identificare gli errori ed eseguirne il debug.

## <a name="resource-utilization-is-high"></a>L'utilizzo delle risorse è elevato

Assicurarsi di sfruttare i vantaggi della parallelizzazione in Analisi di flusso di Azure. È possibile usare il [ridimensionamento con la parallelizzazione delle query](stream-analytics-parallelization.md) dei processi di Analisi di flusso configurando partizioni di input e ottimizzando la definizione delle query di analisi.

## <a name="debug-queries-progressively"></a>Eseguire il debug progressivo delle query

Nell'elaborazione dei dati in tempo reale può essere utile conoscere l'aspetto dei dati nel mezzo di una query. È possibile vedere questo utilizzando il diagramma dei processi in Visual Studio.You can see this using the job diagram in Visual Studio. Se non si dispone di Visual Studio, è possibile eseguire passaggi aggiuntivi per l'output di dati intermedi.

Poiché gli input o i passaggi di un processo di Analisi di flusso di Azure possono essere letti più volte, è possibile scrivere istruzioni SELECT INTO aggiuntive. In questo modo vengono generati dati intermedi nella risorsa di archiviazione che consentono di controllare la correttezza dei dati, esattamente come fanno le *variabili di controllo* quando si esegue il debug di un programma.

La seguente query di esempio in un processo di Analisi di flusso di Azure ha un input di flusso, due input di dati di riferimento e un output in Archiviazione tabelle di Azure. La query unisce i dati dell'hub eventi e di due BLOB di riferimento per ottenere le informazioni di nome e categoria:

![Esempio di Analisi di flusso SELECT nella query](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Si noti che il processo è in esecuzione ma non vengono generati eventi nell'output. Nel riquadro **Monitoraggio**, illustrato di seguito, è possibile vedere che l'input produce dati, ma non sa quale passaggio del **JOIN** ha causato l'eliminazione di tutti gli eventi.

![Riquadro Monitoraggio di Analisi di flusso](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

In questa situazione è possibile aggiungere alcune istruzioni SELECT INTO aggiuntive per "registrare" i risultati intermedi di JOIN e i dati che vengono letti dall'input.

In questo esempio sono stati aggiunti due nuovi "output temporanei". Può trattarsi di qualsiasi sink desiderato. Qui si usa Archiviazione di Azure come esempio:

![Aggiunta di istruzioni SELECT INTO aggiuntive alla query analisi di flusso](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

È quindi possibile riscrivere la query come segue:

![Query di Analisi di flusso SELECT INTO riscritta](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Ora avviare nuovamente il processo e lasciarlo in esecuzione per alcuni minuti. Quindi eseguire una query su temp1 e temp2 con Visual Studio Cloud Explorer per generare le tabelle seguenti:

**tabella temp1**
SELECT INTO temp1 tabella Query di analisi di![flusso](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**tabella temp2**
SELECT INTO temp2 tabella Query di analisi di![flusso](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

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

Per ulteriore assistenza, prova il forum di Analisi di Flusso di [Azure.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi di flusso di AzureGet started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Guida di riferimento al linguaggio di query di Analisi di flusso di AzureAzure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
