---
title: Dashboard di Power BI su Analisi di flusso | Documentazione Microsoft
description: Utilizzare un dashboard di Power BI streaming in tempo reale per raccogliere intelligence aziendali e analizzare i dati di volumi elevati di un processo di Analisi di flusso.
keywords: dashboard di analisi, dashboard in tempo reale
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: c60d620d9fc91cfef2340b645e15ed19d3d1f4e1
ms.openlocfilehash: 39b2852c39e8d3eb4687cbcfd9116e1d66fc9d9d


---
# <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Analisi di flusso e Power BI: un dashboard di analisi in tempo reale per il flusso di dati
Analisi di flusso di Azure consente l’uso di uno dei principali strumenti di business intelligence, Microsoft Power BI. Imparare a usare Analisi di flusso di Azure per l'analisi di volumi elevati di flussi di dati e per ottenere informazioni in un dashboard di analisi di Power BI in tempo reale.

Utilizzare [Microsoft Power BI](https://powerbi.com/) per creare rapidamente un dashboard in tempo reale. [Guardare un video che illustra lo scenario](https://www.youtube.com/watch?v=SGUpT-a99MA).

Questo articolo descrive come creare strumenti di business intelligence personalizzati usando Power BI come output per i processi di Analisi di flusso di Azure e usando un dashboard in tempo reale.

## <a name="prerequisites"></a>Prerequisiti
* Account di Microsoft Azure
* Account aziendale o dell'istituto di istruzione per Power BI
* Completare lo scenario di introduzione [Rilevamento delle frodi in tempo reale](stream-analytics-real-time-fraud-detection.md). Questo articolo si basa su tale flusso di lavoro e aggiunge un output di set di dati di streaming Power BI.

## <a name="add-power-bi-output"></a>Aggiungere l’output Power BI
Ora che è presente un input per il processo, è possibile definire un output di Power BI. Selezionare la casella al centro dell'area **Output** del dashboard dei processi. Scegliere il pulsante **+ Aggiungi** , quindi creare l'output.

![aggiungere l'output](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

Fornire l'**Alias di output**: è possibile usare qualsiasi alias di output purché sia di facile riferimento. L’alias dell’output risulta essere particolarmente utile se si decide di avere più output per il processo. In tal caso, è necessario fare riferimento a questo output nella query. Ad esempio, utilizzare il valore di alias di output = "StreamAnalyticsRealTimeFraudPBI".

Fare clic sul pulsante **Autorizza**.

![aggiungere l'autorizzazione](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

Verrà visualizzata una finestra per l'inserimento delle credenziali di Azure (account aziendale o dell'istituto di istruzione) e verrà fornito l'accesso al processo Azure all'area Power BI.

![campi di autorizzazione](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

La finestra di autorizzazione scomparirà una volta inseriti i dati e l'area di output Nuova avrà campi Nome del set di dati e Nome tabella.

![Area di lavoro pbi](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

Definire i campi come indicato di seguito:
* **Area di lavoro del gruppo**: selezionare nel tenant Power BI un'area di lavoro in cui verrà creato il set di dati.
* **Nome set di dati** - Fornire un nome set di dati all’output Power BI. Ad esempio, utilizzare "StreamAnalyticsRealTimeFraudPBI".
* **Nome tabella** - Fornire un nome tabella nel set di dati dell’output Power BI. Si supponga che venga denominata "StreamAnalyticsRealTimeFraudPBI". Attualmente, l’output di Power BI da processi di Analisi di flusso può avere solo una tabella in un set di dati.

Fare clic su **Crea** e a questo punto la configurazione dell'output è completa.

> [!WARNING]
> Inoltre, tenere presente che se Power BI dispone già di un set di dati e di una tabella caratterizzati dal nome fornito dall'utente nel processo di analisi di flusso, i dati esistenti verranno sovrascritti.
> Inoltre, non creare in modo esplicito il set di dati e la tabella nell'account di Power BI. Il set di dati e la tabella vengono creati automaticamente quando il processo di analisi di flusso viene avviato e inizia a generare output in Power BI. Se la query di processo non genera alcun risultato, il set di dati e la tabella non vengono creati.
> 
> 

Il set di dati viene creato con le seguenti impostazioni:
* defaultRetentionPolicy: BasicFIFO - data is FIFO, 200k maximum rows
* defaultMode: pushStreaming: supports both streaming tiles and traditional report-based visuals (aka push)
* la creazione di set di dati con altri flag al momento non è supportata

Per ulteriori informazioni sui set di dati di Power BI vedere i riferimenti a [API REST di Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-query"></a>Scrivere query
Andare nella scheda **Query** del processo. Scrivere la query, l’output che si desidera avere in Power BI. Ad esempio, potrebbe essere simile alla query SQL seguente per rilevare una frode SIM nel settore delle telecomunicazioni:


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (e.g. Australia and Europe) within 5 seconds) */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime
   
/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between 1 and 5 seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Creare il dashboard in Power BI

Andare al sito [Powerbi.com](https://powerbi.com) e accedere con l’account aziendale o dell’istituto di istruzione. Se la query del processo di Analisi di flusso produce risultati, verrà visualizzato il set di dati già creato:

![set di dati di streaming](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

Fare clic su Aggiungi riquadro e selezionare i dati di streaming personalizzati.

![set di dati di streaming personalizzati](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

Selezionare quindi il set di dati dall'elenco:

![il set di dati di streaming](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

A questo punto creare una scheda di visualizzazione e selezionare il campo fraudulentcalls.

![aggiungere la frode](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

Ora si dispone di un contatore di frodi.

![contatore di frodi](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

Eseguire nuovamente l'esercizio di aggiunta di un nuovo riquadro ma questa volta selezionare un grafico a linee. Aggiungere fraudulentcalls come valore e l'asse come windowend. Sono stati selezionati gli ultimi 10 minuti:

![chiamate fraudolente](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


Tenere presente che in questa esercitazione è stato mostrato come creare solo uno dei tipi di grafici possibili per un set di dati. Power BI consente di creare altri strumenti di business intelligence per clienti per l'organizzazione. Per visualizzare un altro esempio di dashboard Power BI, guardare il video [Introduzione a Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Per altre informazioni sulla configurazione di un output di Power BI e sull'uso dei gruppi di Power BI, vedere la [sezione Power BI](stream-analytics-define-outputs.md#power-bi) di [Informazioni sugli output di analisi di flusso](stream-analytics-define-outputs.md "Understanding Stream Analytics outputs"). Un'altra risorsa utile per maggiori informazioni sulla creazione di dashboard con Power BI è [Dashboard in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="limitations-and-best-practices"></a>Limitazioni e procedure consigliate
Power BI impiega vincoli di concorrenza e velocità effettiva come descritto qui: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Prezzi di Power BI")

Attualmente, è possibile chiamare Power BI approssimativamente una volta al secondo. Gli oggetti visivi di streaming supportano pacchetti di dimensioni 15kb. Oltre questo valore gli oggetti visivi di streaming restituiranno un errore (ma il push continuerà a funzionare).

A causa di tali vincoli Power BI soddisfa maggiormente quei casi in cui Analisi di flusso di Azure realizza una riduzione significativa del carico dei dati.
Si consiglia l'uso di TumblingWindow o di HoppingWindow per garantire che il push di dati sia di massimo 1 push/secondo e che la query soddisfi i requisiti di velocità effettiva. È possibile usare l'equazione seguente per calcolare il valore da indicare nella finestra in pochi secondi:

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Ad esempio, se si dispone di 1.000 dispositivi che inviano dati ogni secondo, si utilizza la SKU di Power BI Pro che supporta 1.000.000 righe/ora e si desidera ottenere i dati medi per dispositivo su Power BI, è possibile effettuare massimo un push ogni 4 secondi per dispositivo (come mostrato di seguito):  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Pertanto sarà necessario modificare la query originale in:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>Rinnovare l'autorizzazione
Se la password dell'account Power BI è stata modificata dopo la creazione o l'ultima autenticazione del processo, è necessario autenticare nuovamente l'account. Se Multi-Factor Authentication (MFA) è configurata nel tenant Azure Active Directory (AAD), sarà necessario rinnovare anche l'autorizzazione Power BI ogni due settimane. Un sintomo di questo problema è che non ci sono output del processo ed è presente un "Errore nell'autenticazione dell'utente" nei log delle operazioni.

Analogamente, se un processo tenta di avviarsi mentre il token è scaduto, si verificherà un errore e l'avvio del processo avrà esito negativo. Per risolvere questo problema, arrestare il processo in esecuzione e passare all'output di Power BI.  Fare clic sul collegamento "Rinnovare autorizzazione" e riavviare il processo dall'ultima volta che è stato arrestato per evitare la perdita di dati. Dopo l'aggiornamento dell'autorizzazione con Power BI, verrà visualizzato un avviso verde nell'area di autorizzazione che indica che il problema è stato risolto.

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


