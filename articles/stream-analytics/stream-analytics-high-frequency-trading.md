---
title: Simulazione di trading ad alta frequenza con Analisi di flusso | Microsoft Docs
description: Come eseguire il training e l'assegnazione di punteggi per un modello di regressione lineare nello stesso processo di Analisi di flusso
keywords: apprendimento automatico, analisi avanzata, regressione lineare, simulazione, aggregazione definita dall'utente, funzione definita dall'utente
documentationcenter: 
services: stream-analytics
author: zhongc
manager: jhubbard
editor: cgronlun
ms.assetid: 997ccfc1-abaf-4c12-bef2-632481140f05
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/05/2017
ms.author: zhongc
ms.openlocfilehash: f25a27a86b366b2302657c44108cd823b0384831
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Simulazione di trading ad alta frequenza con Analisi di flusso
La combinazione di linguaggio SQL, funzioni definite dall'utente di JavaScript e aggregazioni definite dall'utente in Analisi di flusso di Azure consente agli utenti di eseguire analisi avanzate. Le analisi avanzate possono includere il training e l'assegnazione dei punteggi di Machine Learning online, oltre alla simulazione di processi con stato. Questo articolo illustra come eseguire la regressione lineare in un processo di Analisi di flusso di Azure che esegue il training e l'assegnazione di punteggi in modo continuo in uno scenario di trading ad alta frequenza.

## <a name="high-frequency-trading"></a>Trading ad alta frequenza
Il flusso logico del trading ad alta frequenza include:
1. Acquisizione di quotazioni in tempo reale da un listino di Borsa.
2. Creazione di un modello predittivo basato sulle quotazioni, per poter anticipare l'andamento dei prezzi.
3. Esecuzione di ordini di acquisto o di vendita per trarre profitto dalla stima corretta degli andamenti dei prezzi. 

Sono quindi necessari:
* Feed di quotazioni in tempo reale.
* Modello predittivo applicabile alle quotazioni in tempo reale.
* Simulazione di trading che offre una dimostrazione di profitti o perdite dell'algoritmo di trading.

### <a name="real-time-quote-feed"></a>Feed di quotazioni in tempo reale
IEX offre gratuitamente [quotazioni di richiesta e di offerta in tempo reale](https://iextrading.com/developer/docs/#websockets) usando socket.io. È possibile scrivere un semplice programma console per ricevere le quotazioni in tempo reale ed eseguire il push a Hub eventi di Azure come origine dati. Il codice seguente è una struttura del programma. Il codice omette la gestione degli errori per brevità. È anche necessario includere nel progetto i pacchetti NuGet SocketIoClientDotNet e WindowsAzure.ServiceBus.


    using Quobject.SocketIoClientDotNet.Client;
    using Microsoft.ServiceBus.Messaging;

    var symbols = "msft,fb,amzn,goog";
    var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
    var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");

    socket.On(Socket.EVENT_MESSAGE, (message) =>
    {
        eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
    });

    socket.On(Socket.EVENT_CONNECT, () =>
    {
        socket.Emit("subscribe", symbols);
    });

Di seguito sono riportati alcuni eventi di esempio generati:

    {"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}

>[!NOTE]
>Il timestamp dell'evento è **lastUpdated**, sotto forma di valore epoch.

### <a name="predictive-model-for-high-frequency-trading"></a>Modello predittivo per il trading ad alta frequenza
A scopo di dimostrazione verrà usato un modello lineare descritto da Darryl Shen in [questo documento](http://eprints.maths.ox.ac.uk/1895/1/Darryl%20Shen%20%28for%20archive%29.pdf).

Lo squilibrio negli ordini di volumi (VOI, Volume Order Imbalance) è una funzione di prezzo e volume di offerta/richiesta correnti e prezzo e volume di offerta/richiesta dell'ultimo tick. Il documento identifica una correlazione tra VOI e variazione di prezzo futura e definisce un modello lineare tra gli ultimi 5 valori VOI e il cambiamento di prezzo nei 10 tick successivi. Il training del modello viene eseguito usando i dati del giorno precedente con regressione lineare. 

Il modello sottoposto a training viene quindi usato per ottenere le previsioni del cambiamento di prezzo per le quotazioni nel giorno di trading corrente in tempo reale. Quando è previsto un cambiamento di prezzo sufficientemente elevato, viene eseguita una negoziazione. A seconda dell'impostazione della soglia, nel corso di un giorno di trading possono verificarsi migliaia di negoziazioni per un singolo titolo.

![Definizione di VOI](./media/stream-analytics-high-frequency-trading/voi-formula.png)

Le operazioni di training e previsione verranno ora espresse in un processo di Analisi di flusso di Azure.

Per prima cosa, viene eseguita la pulizia degli input. Il valore epoch viene convertito in un valore di tipo datetime con **DATEADD**. Per eseguire la coercizione dei tipi di dati senza che si verifichino errori nella query, si usa **TRY_CAST**. È sempre consigliabile eseguire il cast dei campi di input nei tipi di dati previsti in modo da evitare un comportamento imprevisto in caso di manipolazione o confronto dei campi.

    WITH
    typeconvertedquotes AS (
        /* convert all input fields to proper types */
        SELECT
            System.Timestamp AS lastUpdated,
            symbol,
            DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
            TRY_CAST(bidSize as bigint) AS bidSize,
            TRY_CAST(bidPrice as float) AS bidPrice,
            TRY_CAST(askSize as bigint) AS askSize,
            TRY_CAST(askPrice as float) AS askPrice,
            TRY_CAST(volume as bigint) AS volume,
            TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
            TRY_CAST(lastSalePrice as float) AS lastSalePrice
        FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
    ),
    timefilteredquotes AS (
        /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
        /* clean up invalid data points */
        SELECT * FROM typeconvertedquotes
        WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
    ),

Successivamente, si usa la funzione **LAG** per ottenere i valori dell'ultimo tick. Come **LIMIT DURATION** viene scelto arbitrariamente il valore di un'ora. Data la frequenza delle quotazioni, si può presumere di trovare il tick precedente tornando indietro di un'ora.  

    shiftedquotes AS (
        /* get previous bid/ask price and size in order to calculate VOI */
        SELECT
            symbol,
            (bidPrice + askPrice)/2 AS midPrice,
            bidPrice,
            bidSize,
            askPrice,
            askSize,
            LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
            LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
            LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
            LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
        FROM timefilteredquotes
    ),

È quindi possibile calcolare il valore VOI. I valori Null vengono filtrati, nell'eventualità che il tick precedente non esista.

    currentPriceAndVOI AS (
        /* calculate VOI */
        SELECT
            symbol,
            midPrice,
            (CASE WHEN (bidPrice < bidPricePrev) THEN 0
                ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
             END) -
            (CASE WHEN (askPrice < askPricePrev) THEN askSize
                ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
             END) AS VOI
        FROM shiftedquotes
        WHERE
            bidPrice IS NOT NULL AND
            bidSize IS NOT NULL AND
            askPrice IS NOT NULL AND
            askSize IS NOT NULL AND
            bidPricePrev IS NOT NULL AND
            bidSizePrev IS NOT NULL AND
            askPricePrev IS NOT NULL AND
            askSizePrev IS NOT NULL
    ),

A questo punto, si usa di nuovo **LAG** per creare una sequenza con 2 valori VOI consecutivi seguiti da 10 valori di prezzo medio consecutivi.

    shiftedPriceAndShiftedVOI AS (
        /* get 10 future prices and 2 previous VOIs */
        SELECT
            symbol,
            midPrice AS midPrice10,
            LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
            LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
            LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
            LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
            LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
            LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
            LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
            LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
            LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
            LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
            LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
            LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),

I dati vengono quindi trasformati in input per un modello lineare a due variabili. Anche in questo caso vengono filtrati gli eventi per cui non sono disponibili tutti i dati.

    modelInput AS (
        /* create feature vector, x being VOI, y being delta price */
        SELECT
            symbol,
            (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
            VOI1 AS x1,
            VOI2 AS x2
        FROM shiftedPriceAndShiftedVOI
        WHERE
            midPrice1 IS NOT NULL AND
            midPrice2 IS NOT NULL AND
            midPrice3 IS NOT NULL AND
            midPrice4 IS NOT NULL AND
            midPrice5 IS NOT NULL AND
            midPrice6 IS NOT NULL AND
            midPrice7 IS NOT NULL AND
            midPrice8 IS NOT NULL AND
            midPrice9 IS NOT NULL AND
            midPrice10 IS NOT NULL AND
            midPrice IS NOT NULL AND
            VOI1 IS NOT NULL AND
            VOI2 IS NOT NULL
    ),

Dato che Analisi di flusso di Azure non include una funzione di regressione lineare predefinita, si usano le aggregazioni **SUM** e **AVG** per calcolare i coefficienti per il modello lineare.

![Formula di regressione lineare](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

    modelagg AS (
        /* get aggregates for linear regression calculation,
         http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
        SELECT
            symbol,
            SUM(x1 * x1) AS x1x1,
            SUM(x2 * x2) AS x2x2,
            SUM(x1 * y) AS x1y,
            SUM(x2 * y) AS x2y,
            SUM(x1 * x2) AS x1x2,
            AVG(y) AS avgy,
            AVG(x1) AS avgx1,
            AVG(x2) AS avgx2
        FROM modelInput
        GROUP BY symbol, TumblingWindow(hour, 24, -4)
    ),
    modelparambs AS (
        /* calculate b1 and b2 for the linear model */
        SELECT
            symbol,
            (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
            (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
            avgy,
            avgx1,
            avgx2
        FROM modelagg
    ),
    model AS (
        /* calculate a for the linear model */
        SELECT
            symbol,
            avgy - b1 * avgx1 - b2 * avgx2 AS a,
            b1,
            b2
        FROM modelparambs
    ),

Per usare il modello del giorno precedente per l'assegnazione dei punteggi dell'evento corrente, si vogliono unire le quotazioni con il modello. Invece di usare **JOIN**, tuttavia, si esegue **UNION** su eventi di modello ed eventi di quotazione e quindi si usa **LAG** per associare gli eventi al modello del giorno precedente e poter così ottenere esattamente una corrispondenza. A causa del fine settimana, è necessario tornare indietro di tre giorni. Se venisse usato un semplice **JOIN**, si otterrebbero tre modelli per ogni evento di quotazione.

    shiftedVOI AS (
        /* get two consecutive VOIs */
        SELECT
            symbol,
            midPrice,
            VOI AS VOI1,        
            LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),
    VOIAndModel AS (
        /* combine VOIs and models */
        SELECT
            'voi' AS type,
            symbol,
            midPrice,
            VOI1,
            VOI2,
            0.0 AS a,
            0.0 AS b1,
            0.0 AS b2
        FROM shiftedVOI
        UNION
        SELECT
            'model' AS type,
            symbol,
            0.0 AS midPrice,
            0 AS VOI1,
            0 AS VOI2,
            a,
            b1,
            b2
        FROM model
    ),
    VOIANDModelJoined AS (
        /* match VOIs with the latest model within 3 days (72 hours, to take the weekend into account) */
        SELECT
            symbol,
            midPrice,
            VOI1 as x1,
            VOI2 as x2,
            LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
            LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
            LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
        FROM VOIAndModel
        WHERE type = 'voi'
    ),

È ora possibile creare previsioni e generare segnali di acquisto/vendita in base al modello, con un valore di soglia di 0,02. Un valore di negoziazione 10 corrisponde a un acquisto. Un valore di negoziazione -10 corrisponde a una vendita.

    prediction AS (
        /* make prediction if there is a model */
        SELECT
            symbol,
            midPrice,
            a + b1 * x1 + b2 * x2 AS efpc
        FROM VOIANDModelJoined
        WHERE
            a IS NOT NULL AND
            b1 IS NOT NULL AND
            b2 IS NOT NULL AND
            x1 IS NOT NULL AND
            x2 IS NOT NULL
    ),
    tradeSignal AS (
        /* generate buy/sell signals */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,     
            midPrice,
            efpc,
            CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
            DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
        FROM prediction
    ),

### <a name="trading-simulation"></a>Simulazione di trading
Dopo aver definito i segnali di trading, si vuole testare l'efficacia della strategia di trading senza eseguire realmente negoziazioni. 

A questo scopo si usa un'aggregazione definita dall'utente, con finestre di salto per ogni minuto. Con il raggruppamento aggiuntivo in base alla data e la clausola having, la finestra può coprire solo eventi appartenenti allo stesso giorno. Per una finestra di salto su due giorni, la funzione **GROUP BY** in base alla data separa il raggruppamento in giorno precedente e giorno corrente. La clausola **HAVING** filtra le finestre che terminano nel giorno corrente con raggruppamento nel giorno precedente.

    simulation AS
    (
        /* perform trade simulation for the past 7 hours to cover an entire trading day, and generate output every minute */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,
            date,
            uda.TradeSimulation(tradeSignal) AS s
        FROM tradeSignal
        GROUP BY HoppingWindow(minute, 420, 1), symbol, date
        Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
    )

L'aggregazione definita dall'utente di JavaScript inizializza tutti gli accumulatori nella funzione `init`, calcola la transizione di stato con ogni evento aggiunto alla finestra e restituisce i risultati della simulazione alla fine della finestra. Il processo generale di trading prevede:

- Acquisto di titoli quando non se ne possiedono e viene ricevuto un segnale di acquisto.
- Vendita di titoli quando se ne possiedono e viene ricevuto un segnale di vendita.
- Vendita allo scoperto se non si è in possesso di titoli. 

In caso di posizione di scoperto, se viene ricevuto un segnale di acquisto viene eseguito l'acquisto a copertura. In questa simulazione non si possiedono o si vendono mai allo scoperto 10 azioni di un determinato titolo e il costo della transazione è fisso a 8 dollari.


    function main() {
        var TRADE_COST = 8.0;
        var SHARES = 10;

        this.init = function () {
            this.own = false;
            this.pos = 0;
            this.pnl = 0.0;
            this.tradeCosts = 0.0;
            this.buyPrice = 0.0;
            this.sellPrice = 0.0;
            this.buySize = 0;
            this.sellSize = 0;
            this.buyTotal = 0.0;
            this.sellTotal = 0.0;
        }

        this.accumulate = function (tradeSignal, timestamp) {

            if(!this.own && tradeSignal.trade == 10) {
              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;
            } else if(!this.own && tradeSignal.trade == -10) {
              // Sell to open
              this.own = true;
              this.pos = -1
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
              // Sell to close
              this.own = false;
              this.pos = 0;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;

              // Sell to open
              this.own = true;
              this.pos = -1;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;        
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
              // Buy to close
              this.own = false;
              this.pos = 0;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;

              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;         
              this.buyTotal += SHARES * tradeSignal.midprice;
            }
        }

        this.computeResult = function () {
            var result = {
                "pnl": this.pnl,
                "buySize": this.buySize,
                "sellSize": this.sellSize,
                "buyTotal": this.buyTotal,
                "sellTotal": this.sellTotal,
                "tradeCost": this.tradeCost
                };
            return result;
        }
    }

L'output viene infine inserito in un dashboard di Power BI per la visualizzazione.

    SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
    SELECT
        symbol,
        time,
        date,
        TRY_CAST(s.pnl as float) AS pnl,
        TRY_CAST(s.buySize as bigint) AS buySize,
        TRY_CAST(s.sellSize as bigint) AS sellSize,
        TRY_CAST(s.buyTotal as float) AS buyTotal,
        TRY_CAST(s.sellTotal as float) AS sellTotal
        INTO pnlDashboard
    FROM simulation /* output trade simulation to PBI */

![Negoziazioni](./media/stream-analytics-high-frequency-trading/trades.png)

![Profitti e perdite](./media/stream-analytics-high-frequency-trading/pnl.png)


## <a name="summary"></a>Riepilogo
È possibile implementare un modello di trading ad alta frequenza realistico con una query moderatamente complessa in Analisi di flusso di Azure. A causa dell'assenza di una funzione di regressione lineare predefinita, è necessario semplificare il modello passando da cinque variabili di input a due. Un utente determinato può tuttavia implementare anche algoritmi con dimensioni superiori e complessità maggiore come aggregazioni definite dall'utente di JavaScript. 

È opportuno rilevare che la maggior parte della query, tranne l'aggregazione definita dall'utente di JavaScript, può essere testata e sottoposta a debug in Visual Studio tramite gli [strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-tools-for-visual-studio.md). Dopo aver scritto la query iniziale, l'autore ha impiegato meno di 30 minuti per il test e il debug della query in Visual Studio. 

Attualmente non è possibile eseguire il debug dell'aggregazione definita dall'utente in Visual Studio. Questa funzionalità verrà presto abilitata con la possibilità di scorrere il codice JavaScript. Notare anche che i nomi dei campi impiegati nell'aggregazione definita dall'utente sono interamente in lettere minuscole. In fase di test della query, questo comportamento non era ovvio. Con il livello di compatibilità 1.1 di Analisi di flusso di Azure, tuttavia, viene mantenuto l'uso di lettere maiuscole e minuscole nei nomi dei campi ed è quindi un comportamento più naturale.

Questo articolo ha lo scopo di offrire uno spunto a tutti gli utenti di Analisi di flusso di Azure, che potranno usare questo servizio per eseguire analisi avanzate quasi in tempo reale, in modo continuo. Inviare eventuali commenti e suggerimenti per semplificare l'implementazione di query per scenari di analisi avanzata.
