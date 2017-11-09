---
title: Guida per la risoluzione dei problemi di Analisi di flusso di Azure | Documentazione Microsoft
description: Come risolvere i problemi del processo di Analisi di flusso
keywords: guida per la risoluzione dei problemi
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: dcff312e4a282b15e76ea32aadb1981a496a2446
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Guida per la risoluzione dei problemi di Analisi di flusso di Azure

La risoluzione dei problemi di Azure di flusso di Azure può risultare complessa a prima vista. Dopo aver lavorato con molti utenti, è stata creata questa guida al fine di semplificare la procedura e rimuovere le ambiguità sugli input, gli output, le query e le funzioni.

## <a name="troubleshoot-your-stream-analytics-job"></a>Risoluzione dei problemi del processo di Analisi di flusso

Per ottenere i migliori risultati nella risoluzione dei problemi del processo di Analisi di flusso, applicare le linee guida seguenti:

1.  Testare la connettività:
    - Verificare la connettività agli input e output con il pulsante **Verifica connessione** per ogni input e output.

2.  Esaminare i dati di input:
    - Per verificare che è attivo il flusso dei dati di input verso l'hub eventi, usare [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) per connettersi all'hub eventi di Azure, se in uso.  
    - Usare il pulsante [**Dati di esempio**](stream-analytics-sample-data-input.md) per ogni input e scaricare i dati di esempio dell'input.
    - Esaminare i dati di esempio per comprendere la forma dei dati, ovvero lo schema e i [tipi di dati](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  Testare la query:
    - Nella scheda **Query** usare il pulsante **Verifica** e i dati di esempio scaricati per [testare la query](stream-analytics-test-query.md). Esaminare eventuali errori e provare a risolverli.
    - Se si usa [**Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), assicurarsi che i timestamp degli eventi siano successivi all'[ora di inizio del processo](stream-analytics-out-of-order-and-late-events.md).

4.  Eseguire il debug della query:
    - Ricreare la query progressivamente, partendo dalla semplice istruzione di selezione per arrivare agli aggregati più complessi, attenendosi alla procedura illustrata. Usare la clausola [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) per creare la logica di query passo per passo.
    - Usare [SELECT INTO](stream-analytics-select-into.md) per eseguire il debug dei passaggi della query.

5.  Eliminare i problemi comuni, ad esempio:
    - Una clausola [**WHERE**](https://msdn.microsoft.com/library/azure/dn835048.aspx) della query ha filtrato tutti gli eventi impedendo la generazione dell'output.
    - Quando si usano le funzioni finestra, attendere la durata dell'intera finestra per vedere l'output dalla query.
    - Il timestamp per gli eventi precede l'ora di inizio del processo, perciò gli eventi vengono eliminati.

6.  Usare l'ordine degli eventi:
    - Se tutti i passaggi precedenti hanno avuto esito positivo, accedere al pannello **Impostazioni** e selezionare [**Ordinamento eventi**](stream-analytics-out-of-order-and-late-events.md). Verificare che questo criterio sia configurato in modo sensato per lo scenario. Il criterio *non* viene applicato quando si usa il pulsante **Test** per testare la query. Questo aspetto rappresenta una differenza tra il test nel browser e l'esecuzione del processo in produzione.

7.  Eseguire il debug usando le metriche:
    - Se non si ottiene alcun output al termine della durata prevista (in base alla query), provare la procedura seguente:
        - Esaminare le [**metriche di monitoraggio**](stream-analytics-monitoring.md) nella scheda **Monitor**. Poiché i valori sono aggregati, le metriche sono posticipate di alcuni minuti.
            - Se Eventi di input è maggiore di zero, il processo è in grado di leggere i dati di input. Se Eventi di input non è maggiore di zero:
                - Per vedere se l'origine dati contiene dati validi, verificarla tramite [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Questa verifica viene eseguita se il processo usa Hub eventi come input.
                - Controllare se il formato di serializzazione dei dati e la codifica dei dati sono quelli previsti.
                - Se il processo usa un Hub eventi, verificare se il corpo del messaggio è *Null*.
            - Se Errori di conversione dati è maggiore di zero, significa che probabilmente:
                - Il processo potrebbe non riuscire a deserializzare gli eventi.
                - Lo schema degli eventi potrebbe non corrispondere allo schema definito o previsto degli eventi nella query.
                - I tipi di dati di alcuni dei campi nell'evento potrebbero non corrispondere alle aspettative.
            - Se Errori di runtime è maggiore di zero significa che il processo è in grado di ricevere i dati ma genera errori durante l'elaborazione della query.
                - Per trovare gli errori, consultare i [log di controllo](../azure-resource-manager/resource-group-audit.md) e filtrare lo stato *non riuscito*.
            - Se InputEvents è maggiore di zero e OutputEvents è uguale a zero, significa che una delle seguenti cose è vera:
                - L'elaborazione della query non ha prodotto eventi di output.
                - Il formato degli eventi o dei relativi campi potrebbe non essere valido, di conseguenza l'elaborazione della query non ha prodotto output.
                - Il processo non è riuscito a eseguire il push dei dati al [sink di output](stream-analytics-select-into.md) per motivi di connettività o autenticazione.
        - In tutti i casi di errore menzionati sopra, i messaggi di log delle operazioni specificano altri dettagli e la spiegazione della situazione, tranne nei casi in cui la logica della query ha filtrato tutti gli eventi. Se l'elaborazione di più eventi ha generato errori, Analisi di flusso registra i primi tre messaggi di errore dello stesso tipo entro 10 minuti nei log operazioni. Elimina quindi gli altri errori identici con il messaggio "Gli errori si verificano troppo rapidamente e verranno eliminati".

8. Eseguire il debug usando i log di controllo e diagnostica:
    - Usare i [log di controllo](../azure-resource-manager/resource-group-audit.md) e filtrare per identificare gli errori ed eseguirne il debug.
    - Usare i [log di diagnostica del processo](stream-analytics-job-diagnostic-logs.md) per identificare gli errori ed eseguirne il debug.

9. Esaminare gli output:
    - Quando lo stato del processo è *In esecuzione*, a seconda della durata prevista nella query è possibile vedere l'output nell'origine dati di sink.
    - Se non si vedono output che procedono verso un tipo di output specifico, reindirizzarli a un tipo di output che sia meno complesso, ad esempio un BLOB di Azure. Usando Storage Explorer, verificare se è possibile vedere l'output. Verificare inoltre se le limitazioni dell'output impediscono la ricezione dei dati.

10. Usare l'analisi del flusso di dati con le metriche del diagramma di processo:
    - Per analizzare il flusso di dati e identificare i problemi, usare il [diagramma del processo con le metriche](stream-analytics-job-diagram-with-metrics.md).

11. Aprire un caso di supporto:
    - Infine, se tutto il resto non dà risultati, aprire un caso di supporto tecnico Microsoft usando l'ID sottoscrizione che contiene il processo.

## <a name="get-help"></a>Ottenere aiuto

Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
