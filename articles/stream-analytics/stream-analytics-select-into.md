---
title: Eseguire il debug delle query di Analisi di flusso di Azure mediante SELECT INTO | Documentazione Microsoft
description: Campionare i dati intermedi di una query mediante istruzioni SELECT INTO in Analisi di flusso
keywords: 
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 6ffa756eef0cfa44d7dd397e43afbf054ac2df7a
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---
# <a name="debug-queries-by-using-select-into-statements"></a>Eseguire il debug delle query mediante istruzioni SELECT INTO

Nell'elaborazione dei dati in tempo reale può essere utile conoscere l'aspetto dei dati nel mezzo di una query. Poiché gli input o i passaggi di un processo di Analisi di flusso di Azure possono essere letti più volte, è possibile scrivere istruzioni SELECT INTO aggiuntive. In questo modo vengono generati dati intermedi nella risorsa di archiviazione che consentono di controllare la correttezza dei dati, esattamente come fanno le *variabili di controllo* quando si esegue il debug di un programma.

## <a name="use-select-into-to-check-the-data-stream"></a>Usare SELECT INTO per controllare il flusso dei dati

La seguente query di esempio in un processo di Analisi di flusso di Azure ha un input di flusso, due input di dati di riferimento e un output in Archiviazione tabelle di Azure. La query unisce i dati dell'hub eventi e di due BLOB di riferimento per ottenere le informazioni di nome e categoria:

![Esempio di query SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Si noti che il processo è in esecuzione ma non vengono generati eventi nell'output. Nel riquadro **Monitoraggio**, illustrato di seguito, è possibile vedere che l'input produce dati, ma non sa quale passaggio del **JOIN** ha causato l'eliminazione di tutti gli eventi.

![Riquadro Monitoraggio](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
In questa situazione è possibile aggiungere alcune istruzioni SELECT INTO aggiuntive per "registrare" i risultati intermedi di JOIN e i dati che vengono letti dall'input.

In questo esempio sono stati aggiunti due nuovi "output temporanei". Può trattarsi di qualsiasi sink desiderato. Qui si usa Archiviazione di Azure come esempio:

![Aggiunta di ulteriori istruzioni SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

È quindi possibile riscrivere la query come segue:

![Query SELECT INTO riscritta](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Ora avviare nuovamente il processo e lasciarlo in esecuzione per alcuni minuti. Quindi eseguire una query su temp1 e temp2 con Visual Studio Cloud Explorer per generare le tabelle seguenti:

**temp1 table**
![SELECT INTO temp1 table](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 table**
![SELECT INTO temp2 table](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Come si può vedere, temp1 e temp2 hanno entrambe dati e la colonna del nome viene popolata in modo corretto in temp2. Tuttavia, poiché non sono ancora presenti dati nell'output, c'è qualcosa di sbagliato:

![Tabella SELECT INTO output1 senza dati](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Tramite il campionamento dei dati, è possibile essere quasi certi che il problema riguardi il secondo JOIN. È possibile scaricare i dati di riferimento dal BLOB e dare un'occhiata:

![Tabella SELECT INTO riferimento](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Come si può notare, il formato del GUID in questi dati di riferimento è diverso dal formato della colonna [da] di temp2. Ecco perché i dati non arrivavano in output1 come previsto.

È possibile correggere il formato dei dati, caricarlo nel BLOB di riferimento e riprovare:

![Tabella SELECT INTO temporanea](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Questa volta i dati nell'output vengono formattati e popolati come previsto.

![Tabella SELECT INTO finale](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>Ottenere aiuto

Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


