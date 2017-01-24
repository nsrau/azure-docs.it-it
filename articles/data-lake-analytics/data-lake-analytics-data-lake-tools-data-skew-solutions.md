---
title: Possibili soluzioni per le differenze dei dati in Azure Data Lake | Documentazione Microsoft
description: Possibili soluzioni per i problemi di differenza dei dati in Azure Data Lake.
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/16/2016
ms.author: yanacai
translationtype: Human Translation
ms.sourcegitcommit: f2b2a2230900ad5007500f9efb4c8e7ee0aca165
ms.openlocfilehash: 1ddad5c3dc7006895ee38e06b1134857f95c3e7a


---

# <a name="possible-data-skew-solutions-for-azure-data-lake"></a>Possibili soluzioni per le differenze dei dati in Azure Data Lake

## <a name="what-is-data-skew-problem"></a>Cos'è un problema di differenza dei dati?

In poche parole, si ha una differenza dei dati quando un valore è rappresentato in modo eccessivo. Si ipotizzi di assegnare 50 revisori fiscali alla verifica delle dichiarazioni dei redditi, una persona per ogni Stato. Il revisore dello Wyoming torna a casa in anticipo, poiché il numero di abitanti del Wyoming è ridotto, mentre il revisore della California lavora fino a tardi perché la popolazione di quello Stato è maggiore. 
    ![Esempio di un problema di differenza dei dati](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png) 

Nel caso precedente, i dati non sono equamente distribuiti tra i revisori, e ciò impone ad alcuni di lavorare di più rispetto ad altri. Nell'esecuzione dei processi è possibile riscontrare esempi simili al precedente, in cui a un vertice vengono assegnati più dati rispetti ai suoi pari, allungando la durata dell'esecuzione e rallentando infine l'intero processo. In alcuni casi, ciò può impedire la riuscita del processo, poiché i vertici hanno un runtime limitato a 5 ore e un limite di memoria di 6 GB.

## <a name="how-to-troubleshoot"></a>Come risolvere questo problema?

Lo strumento aiuta a rilevare un eventuale problema di differenza dei dati nel processo; per risolvere il problema è possibile provare le soluzioni riportate di seguito.

### <a name="solution-1-improve-table-partition"></a>Soluzione 1. Migliorare la partizione della tabella

#### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Opzione 1. Filtrare in anticipo un valore chiave differente

Se ciò non influisce sulla logica di business, è possibile filtrare in anticipo il valore che ha la maggiore frequenza. Ad esempio, la colonna GUID contiene un numero elevato di 000-000-000, ma non si vuole eseguire un'aggregazione in base a tale valore. In questo caso, è possibile scrivere "WHERE GUID != "000-000-000"" per filtrare il valore ad alta frequenza prima di eseguire l'aggregazione.

#### <a name="option-2-pick-a-different-partitiondistribution-key"></a>Opzione 2. Selezionare una chiave di partizione/distribuzione differente

Nell'esempio precedente, se si vuole soltanto verificare le dichiarazioni in tutto il paese, è possibile selezionare il numero ID come chiave per migliorare la distribuzione dei dati. Talvolta la scelta di una chiave di partizione/distribuzione differente consente di distribuire i dati in modo più uniforme, ma è necessario verificare che la scelta non abbia impatto sulla logica di business. Ad esempio, se si vuole calcolare la somma delle imposte per ciascuno Stato, è consigliabile scegliere lo Stato come chiave di partizione. In questo caso, fare riferimento all'Opzione 3.

#### <a name="option-3-add-more-partitiondistribution-key"></a>Opzione 3. Aggiungere più chiavi di partizione/distribuzione

Oltre a usare lo Stato come chiave di partizione, è possibile aggiungere altre chiavi di partizione, ad esempio il codice postale, per ridurre le dimensioni dei dati di partizione e rendere la distribuzione dei dati più uniforme.

#### <a name="option-4-round-robin-distribution"></a>Opzione 4. Distribuzione tramite ROUND ROBIN

Qualora non sia possibile trovare una chiave adeguata per la partizione e la distribuzione, si può usare la distribuzione ROUND ROBIN. La distribuzione ROUND ROBIN considera ogni riga in modo uguale e la inserisce in modo casuale nel bucket corrispondente. In questo caso, i dati vengono distribuiti uniformemente ma si perdono le informazioni sulla località, riducendo anche le prestazioni del processo rispetto ad alcune operazioni. Inoltre, se si sta eseguendo un'aggregazione per risolvere un problema di chiave differente, il problema non verrà risolto. Per altre informazioni sul round robin, vedere [qui](https://msdn.microsoft.com/en-us/library/mt706196.aspx#dis_sch).

### <a name="case-2-improve-query-plan"></a>Caso 2. Migliorare il piano di query

#### <a name="option-1-create-statistics-for-column"></a>Opzione 1. CREATE STATISTICS per colonna

L'istruzione CREATE STATISTICS per le tabelle, disponibile in U-SQL, consente di offrire più informazioni all'utilità di ottimizzazione delle query relativamente alle caratteristiche dei dati archiviati in una tabella, ad esempio la distribuzione dei valori e così via. Per la maggior parte delle query, l'utilità di ottimizzazione delle query genera già le statistiche necessarie per un piano di query di alta qualità; in alcuni casi è necessario creare statistiche aggiuntive con CREATE STATISTICS o modificare la progettazione della query così da migliorarne le prestazioni. Altre informazioni sono reperibili [qui](https://msdn.microsoft.com/en-us/library/azure/mt771898.aspx).

**Esempio di codice:**

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Si noti che le informazioni statistiche non vengono aggiornate automaticamente, pertanto se si aggiornano i dati nella tabella ma si dimentica di ricreare le statistiche, le prestazioni delle query potrebbero peggiorare.
>
>

#### <a name="option-2-use-skewfactor"></a>Opzione 2. Utilizzo di SKEWFACTOR

Nel citato esempio sulle imposte, se si vuole ottenere il totale delle imposte per ogni Stato, l'unica scelta è raggruppare per Stato usando GROUP BY, che implica tuttavia un problema di differenza dei dati. Tuttavia, è possibile inserire un suggerimento dati nella query per identificare le differenze nelle chiavi, in modo da ottimizzare l'esecuzione del piano di query.

In genere, è possibile impostare il parametro su 0,5 e 1, laddove 0,5 indica una differenza, ma non netta, mentre 1 indica una differenza marcata. Il suggerimento ha un impatto sull'ottimizzazione del piano di esecuzione per l'istruzione corrente e per tutte quelle successive, ed è quindi necessario assicurarsi di aggiungerlo prima della potenziale aggregazione a livello della chiave con differenza.

    SKEWFACTOR (columns) = x

    Provides hint that given columns have a skew factor x between 0 (no skew) and 1 (very heavy skew).

**Esempio di codice:**

    //Adding SKEWFACTOR hint
    @Impressions = 
        SELECT * FROM 
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions = 
        SELECT 
            ClientId, 
            Query, 
            SUM(PageClicks) AS Clicks
        FROM 
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    // Query 2 for Key: Query
    @Display = 
        SELECT * FROM @Sessions 
            INNER JOIN @Campaigns 
                ON @Sessions.Query == @Campaigns.Query
        ;   

Oltre a SKEWFACTOR, per specifici casi di chiavi unite con differenza, se è noto che l'altro set di righe è di piccole dimensioni, è possibile aggiungere il suggerimento ROWCOUNT nell'istruzione di U-SQL prima di JOIN, per indicare all'utilità di ottimizzazione che uno dei set di righe è piccolo, in modo che possa scegliere una strategia di trasmissione unita per migliorare le prestazioni. Si noti tuttavia che l'opzione ROWCOUNT non risolve il problema della differenza, ma può solo fornire un piccolo aiuto.

    OPTION(ROWCOUNT = n)

    Identify small row set before join by giving an estimated integer row count.

**Esempio di codice:**

    // Unstructured (24 hours daily log impressions)
    @Huge   = EXTRACT ClientId int, ... 
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    // Small subset (ie: ForgetMe opt out)
    @Small  = SELECT * FROM @Huge 
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    // Result (not enough info to determine simple Broadcast join)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

### <a name="case-3-improve-user-defined-reducer-and-combiner"></a>Caso 3. Migliorare il riduttore e il combinatore definiti dall'utente

A volte per gestire una logica di processo complessa viene usato un operatore definito dall'utente; in alcuni casi, un riduttore e un combinatore ben scritti possono ridurre il problema della differenza dei dati.

#### <a name="option-1-use-recursive-reducer-if-possible"></a>Opzione 1. Usare un riduttore ricorsivo se possibile

Per impostazione predefinita, un riduttore definito dall'utente viene eseguito in modalità non ricorsiva, ovvero il lavoro di riduzione per una chiave verrà distribuito su un unico vertice. In caso di differenza dei dati, il set di dati più grandi potrebbe essere elaborato in un unico vertice e richiedere un tempo di esecuzione lungo. 

Per migliorare le prestazioni, è possibile aggiungere un attributo nel codice che esegua il riduttore in modalità ricorsiva. I set di dati di grandi dimensioni possono quindi essere distribuiti su più vertici ed eseguiti in parallelo, accelerando così il processo. 

Si noti che per trasformare un riduttore non ricorsivo in ricorsivo è necessario assicurarsi che l'algoritmo sia associativo. Ad esempio, la somma è associativa mentre la media non lo è. È necessario anche verificare che l'input e output per il riduttore rispettino lo stesso schema.

**Attributo per il riduttore ricorsivo:**

    [SqlUserDefinedReducer(IsRecursive = true)]

**Esempio di codice:**

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow> 
            Reduce(IRowset input, IUpdatableRow output)
        {
            // your reducer code here
        }
    }

#### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Opzione 2. Usare la modalità di combinazione a livello di riga se possibile

Alla stregua del suggerimento ROWCOUNT per specifici casi di unioni di chiavi differenti, la modalità di combinazione tenta di distribuire set di valori di chiavi con marcate differenze su più vertici, in modo da ottenere un'esecuzione simultanea. Ciò non può risolvere il problema della differenza dei dati, ma può offrire un aiuto aggiuntivo per set di valori di chiave con differenze marcate.

Per impostazione predefinita, la modalità del combinatore è Full, ovvero il set di righe a sinistra e il set di righe a destra non possono essere separati. Impostando la modalità su Left/Right/Inner è possibile l'unione a livello di riga. Il sistema separa i set di righe corrispondenti e li distribuisce su più vertici che vengono eseguiti in parallelo. Prima di configurare la modalità del combinatore, tuttavia, assicurarsi che sia possibile separare il set di righe corrispondente.

Di seguito un esempio di set di righe a sinistra separato. Nell'esempio, ogni riga di output dipende da una singola riga di input a sinistra e potenzialmente da tutte le righe a destra con lo stesso valore chiave. Se si imposta la modalità del combinatore su Left, il sistema separa il grande set di righe a sinistra in set più piccoli, e li assegna a più vertici.
![Illustrazione della modalità del combinatore](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png) 
   
>[!NOTE]
>Si noti che impostando una modalità del combinatore errata, la combinazione sarà meno efficiente e i risultati potrebbero essere errati.
>
>

**Attributi per il combinatore：**

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): ogni riga di output dipende da una singola riga di input a sinistra e potenzialmente da tutte le righe a destra con lo stesso valore chiave.

- SqlUserDefinedCombiner(Mode=CombinerMode.Right): ogni riga di output dipende da una singola riga di input a destra e potenzialmente da tutte le righe a sinistra con lo stesso valore chiave.

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): ogni riga di output dipende da una sola riga di input a sinistra e a destra con lo stesso valore.

**Esempio di codice:**

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow> 
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        // your combiner code here
        }
    }


<!--HONumber=Dec16_HO3-->


