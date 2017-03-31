---
title: Dashboard di Power BI in Analisi di flusso di Azure | Microsoft Docs
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
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 3dbc49d35def6d7b12ade529d1dd1156dee9d75b
ms.openlocfilehash: 09c54f8cce119c1cbe6a08e969236612447d9e17
ms.lasthandoff: 02/27/2017


---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Analisi di flusso e Power BI: dashboard di analisi in tempo reale per lo streaming dei dati
Analisi di flusso di Azure permette di usare uno dei principali strumenti di Business Intelligence, Microsoft Power BI. Informazioni su come usare Analisi di flusso di Azure per analizzare volumi elevati di dati di streaming e ottenere informazioni da un dashboard di analisi in tempo reale di Power BI.

Utilizzare [Microsoft Power BI](https://powerbi.com/) per creare rapidamente un dashboard in tempo reale. [Guardare un video che illustra questo scenario](https://www.youtube.com/watch?v=SGUpT-a99MA).

Questo articolo illustra come creare strumenti di Business Intelligence personalizzati usando Power BI come output per i processi di Analisi di flusso di Azure e come usare un dashboard in tempo reale.

## <a name="prerequisites"></a>Prerequisiti
* Account di Microsoft Azure.
* Account aziendale o dell'istituto di istruzione per Power BI.
* Completamento dello scenario [Rilevamento delle frodi in tempo reale](stream-analytics-real-time-fraud-detection.md). Questo articolo si basa sul flusso di lavoro descritto in [Rilevamento delle frodi in tempo reale](stream-analytics-real-time-fraud-detection.md) e aggiunge un output del set di dati di streaming di Power BI.

## <a name="add-power-bi-output"></a>Aggiungere l’output Power BI
Ora che è presente un input per il processo, è possibile definire un output di Power BI.

1. Selezionare la casella **Output** al centro del dashboard dei processi. Selezionare **+ Aggiungi** per creare l'output.

    ![Aggiungere l'output](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

2. Specificare l'**Alias di output**. È possibile usare qualsiasi alias di output, purché sia di facile riferimento. L'alias di output è utile se si decide di avere più output per il processo. In tal caso, fare riferimento all'output nella query. Ad esempio, usare il valore "StreamAnalyticsRealTimeFraudPBI" per l'alias di output.

3. Selezionare **Autorizza**.

    ![Aggiungere l'autorizzazione](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

4. Verrà visualizzata una finestra in cui è possibile specificare le credenziali di Azure per l'account aziendale o dell'istituto di istruzione. La finestra permette anche al processo di Azure di accedere all'area di Power BI.

    ![Campi di autorizzazione](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

5. Dopo aver specificato le informazioni necessarie, la finestra di autorizzazione scompare. Nell'area **Nuovo output** sono presenti i campi **Nome set di dati** e **Nome tabella**.

    ![Area di lavoro PBI](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

6. Definire i campi come indicato di seguito:
    * **Area di lavoro del gruppo**: selezionare un'area di lavoro nel tenant Power BI in cui creare il set di dati.
    * **Nome set di dati**: specificare un nome del set di dati per l'output di Power BI. Ad esempio, "StreamAnalyticsRealTimeFraudPBI".
    * **Nome tabella**: specificare un nome della tabella nel set di dati dell'output di Power BI. Ad esempio, "StreamAnalyticsRealTimeFraudPBI". Attualmente, l'output di Power BI da processi di Analisi di flusso può avere solo una tabella in un set di dati.

7. Selezionare **Crea**. A questo punto la configurazione dell'output è terminata.

> [!WARNING]
> Se Power BI ha già un set di dati e una tabella con lo stesso nome specificato nel processo di Analisi di flusso, i dati esistenti vengono sovrascritti.
> Non è consigliabile creare in modo esplicito il set di dati e la tabella nell'account di Power BI. Vengono creati automaticamente quando il processo di Analisi di flusso viene avviato e inizia a generare output in Power BI. Se la query del processo non genera alcun risultato, il set di dati e la tabella non vengono creati.
>
>

Il set di dati viene creato con le impostazioni seguenti:
* **defaultRetentionPolicy: BasicFIFO**: dati FIFO, con un massimo di 200.000 righe.
* **defaultMode: pushStreaming**: supporta sia i riquadri in streaming che gli oggetti visivi tradizionali basati sui report, detti anche push.

Attualmente non è possibile creare set di dati con altri flag.

Per altre informazioni sui set di dati di Power BI, vedere le informazioni di riferimento sull'[API REST di Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-query"></a>Scrivere query
Andare nella scheda **Query** del processo. Scrivere la query di cui si vuole generare l'output in Power BI. Ad esempio, potrebbe essere simile alla query SQL seguente per rilevare una frode SIM nel settore delle telecomunicazioni:


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime

/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between one and five seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Creare il dashboard in Power BI

1. Passare a [Powerbi.com](https://powerbi.com) e accedere con l'account aziendale o dell'istituto di istruzione. Se la query del processo di Analisi di flusso genera risultati, il set di dati risulta già creato, come mostra la figura seguente:

    ![Set di dati di streaming](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. Fare clic su **Aggiungi riquadro** e selezionare i dati di streaming personalizzati.

    ![Set di dati di streaming personalizzato](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

3. Selezionare il set di dati dall'elenco.

    ![Set di dati di streaming](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

4. Creare una scheda di visualizzazione. Quindi selezionare il campo **fraudulentcalls**.

    ![Aggiungere la frode](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

    Ora si dispone di un contatore di frodi.

    ![Contatore di frodi](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

5. Eseguire nuovamente l'esercizio di aggiunta di un riquadro. Questa volta, selezionare il grafico a linee. Aggiungere **fraudulentcalls** come valore e **windowend** come asse. Sono stati selezionati gli ultimi 10 minuti, come mostra lo screenshot seguente:

![Chiamate fraudolente](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


Questa esercitazione mostra come creare solo uno dei tipi di grafico possibili per un set di dati. Power BI consente di creare altri strumenti di business intelligence per clienti per l'organizzazione. Per visualizzare un altro esempio di dashboard Power BI, guardare il video [Introduzione a Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Per altre informazioni sulla configurazione di un output di Power BI e sull'uso dei gruppi di Power BI, vedere la sezione [Power BI](stream-analytics-define-outputs.md#power-bi) dell'articolo relativo agli [output di Analisi di flusso](stream-analytics-define-outputs.md "output di Analisi di flusso"). [Dashboard in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/) è un'altra risorsa utile.

## <a name="learn-about-limitations-and-best-practices"></a>Informazioni sulle limitazioni e le procedure consigliate
Power BI impiega vincoli di concorrenza e di velocità effettiva. Per altre informazioni, vedere [questa pagina relativa a Power BI](https://powerbi.microsoft.com/pricing "Prezzi di Power BI").

Attualmente, è possibile chiamare Power BI approssimativamente una volta al secondo. Gli oggetti visivi di streaming supportano pacchetti da 15 KB. Oltre tale limite, gli oggetti visivi di streaming hanno esito negativo, ma il push continua a funzionare.

A causa di queste limitazioni, Power BI è più adatto ai casi in cui Analisi di flusso di Azure riduce notevolmente il carico di dati.
È consigliabile usare la finestra a cascata o la finestra di salto per assicurarsi che il push di dati avvenga al massimo una volta al secondo e che la query rientri nei requisiti di velocità effettiva.

Per calcolare il valore e visualizzare la finestra in secondi, è possibile usare questa equazione:

![Equazione&1;](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Ad esempio:
- Sono presenti 1.000 dispositivi che inviano dati a intervalli di un secondo.
- Si usa lo SKU di Power BI Pro che supporta 1.000.000 righe l'ora.
- Si vuole pubblicare la quantità di dati medi per ogni dispositivo in Power BI.

L'equazione diventa quindi la seguente:

![Equazione&2;](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Ciò significa che è possibile modificare la query originale come segue:

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
Se la password è stata modificata dopo la creazione o l'ultima autenticazione del processo, è necessario autenticare nuovamente l'account Power BI. Se Azure Multi-Factor Authentication è configurato nel tenant di Azure Active Directory (Azure AD), è anche necessario rinnovare l'autorizzazione di Power BI ogni due settimane. Se non viene rinnovata, si potrebbero verificare problemi come la mancanza di output dei processi o un errore di autenticazione dell'utente nei log delle operazioni.

Analogamente, se si tenta di avviare un processo dopo che il token è scaduto, si verifica un errore e l'avvio del processo ha esito negativo. Per risolvere questo problema, arrestare il processo in esecuzione e passare all'output di Power BI. Per evitare la perdita di dati, fare clic sul collegamento **Rinnova autorizzazione** e riavviare il processo selezionando **Ora ultimo arresto**.

Dopo aver aggiornato l'autorizzazione con Power BI, viene visualizzato un avviso verde nell'area di autorizzazione che indica che il problema è stato risolto.

## <a name="get-help"></a>Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

