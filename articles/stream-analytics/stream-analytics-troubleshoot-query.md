---
title: Risolvere i problemi delle query di Analisi di flusso di Azure
description: Questo articolo descrive alcune tecniche per la risoluzione dei problemi delle query nei processi di Analisi di flusso di Azure.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 586ddb237144daddf0cbfd19785fcba7658469a0
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621482"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Risolvere i problemi delle query di Analisi di flusso di Azure

Questo articolo descrive problemi comuni di sviluppo delle query di Analisi di flusso di Azure e la risoluzione di questi.

## <a name="query-is-not-producing-expected-output"></a>La query non genera l'output previsto 
1.  Esaminare gli errori eseguendo un test locale:
    - Nella scheda **Query** selezionare **Test**. [Testare la query](stream-analytics-test-query.md) usando i dati di esempio scaricati. Esaminare eventuali errori e provare a risolverli.   
    - È anche possibile [testare la query direttamente con input in tempo reale](stream-analytics-live-data-local-testing.md) usando gli strumenti di Analisi di flusso di Azure per Visual Studio.

2.  Se si usa [**Timestamp By**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), assicurarsi che i timestamp degli eventi siano successivi all'[ora di inizio del processo](stream-analytics-out-of-order-and-late-events.md).

3.  Eliminare i problemi comuni, ad esempio:
    - Una clausola [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) della query ha filtrato tutti gli eventi impedendo la generazione dell'output.
    - Una funzione [**CAST**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) non riesce, causando l'esito negativo del processo. Per evitare errori di cast di tipo, usare [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics).
    - Quando si usano le funzioni finestra, attendere la durata dell'intera finestra per vedere l'output dalla query.
    - Il timestamp per gli eventi precede l'ora di inizio del processo, perciò gli eventi vengono eliminati.

4.  Assicurarsi che i criteri di ordinamento degli eventi siano configurati come previsto. Passare a **Impostazioni** e selezionare [ **Ordinamento eventi**](stream-analytics-out-of-order-and-late-events.md). Il criterio *non* viene applicato quando si usa il pulsante **Test** per testare la query. Questo aspetto rappresenta una differenza tra il test nel browser e l'esecuzione del processo in produzione. 

5. Eseguire il debug usando i log di controllo e diagnostica:
    - Usare i [log di controllo](../azure-resource-manager/resource-group-audit.md) e filtrare per identificare gli errori ed eseguirne il debug.
    - Usare i [log di diagnostica del processo](stream-analytics-job-diagnostic-logs.md) per identificare gli errori ed eseguirne il debug.

## <a name="job-is-consuming-too-many-streaming-units"></a>Il processo consuma troppe unità di streaming
Assicurarsi di sfruttare i vantaggi della parallelizzazione in Analisi di flusso di Azure. È possibile usare il [ridimensionamento con la parallelizzazione delle query](stream-analytics-parallelization.md) dei processi di Analisi di flusso configurando partizioni di input e ottimizzando la definizione delle query di analisi.

## <a name="debug-queries-progressively"></a>Eseguire il debug progressivo delle query

Nell'elaborazione dei dati in tempo reale può essere utile conoscere l'aspetto dei dati nel mezzo di una query. Poiché gli input o i passaggi di un processo di Analisi di flusso di Azure possono essere letti più volte, è possibile scrivere istruzioni SELECT INTO aggiuntive. In questo modo vengono generati dati intermedi nella risorsa di archiviazione che consentono di controllare la correttezza dei dati, esattamente come fanno le *variabili di controllo* quando si esegue il debug di un programma.

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

Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)