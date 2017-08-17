---
title: Risolvere i problemi di asimmetria dei dati tramite Strumenti Azure Data Lake per Visual Studio | Documentazione Microsoft
description: Potenziali risoluzioni dei problemi di asimmetria dei dati tramite Strumenti Azure Data Lake per Visual Studio.
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 9b284ef33be4b935569fc368d81ddf040b2c2b7d
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Risolvere i problemi di asimmetria dei dati tramite Strumenti Azure Data Lake per Visual Studio

## <a name="what-is-data-skew"></a>Informazioni sull'asimmetria dei dati

In poche parole, un'asimmetria dei dati si verifica quando un valore è rappresentato in modo eccessivo. Si supponga di avere incaricato 50 esaminatori di imposte per controllare le dichiarazioni dei redditi, un esaminatore per ogni stato degli Stati Uniti. L'esaminatore del Wyoming, che ha una popolazione ridotta, ha poco da fare. In California, invece, l'esaminatore è molto indaffarato a causa della popolazione di notevole entità.
    ![Esempio di un problema di asimmetria dei dati](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

In questo scenario, i dati sono distribuiti in modo non uniforme tra tutti gli esaminatori di imposta, il che significa che alcuni esaminatori lavorano di più rispetto ad altri. Nell'attività personale, non di rado si verificano situazioni simili allo scenario degli esaminatori di imposta qui presentato. In termini più tecnici, un vertice ottiene molti più dati rispetto agli altri elementi, una situazione che costringe il vertice a lavorare di più rispetto agli altri e che, di conseguenza, rallenta l'intero processo. Ancor peggio, il processo potrebbe avere esito negativo in quanto i vertici potrebbero presentare, ad esempio, un limite di runtime di 5 ore e un limite della memoria pari a 6 GB.

## <a name="resolving-data-skew-problems"></a>Risoluzione dei problemi di asimmetria dei dati

Gli Strumenti Azure Data Lake per Visual Studio consentono di rilevare eventuali problemi di asimmetria dei dati all'interno dei processi. Se si verifica un problema, è possibile risolverlo tramite le soluzioni presentate in questa sezione.

## <a name="solution-1-improve-table-partitioning"></a>Soluzione 1. Migliorare il partizionamento delle tabelle

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Opzione 1. Filtrare in anticipo un valore chiave differente

Se ciò non influisce sulla logica di business, è possibile filtrare in anticipo i valori a frequenza più elevata. Ad esempio, se sono presenti molti 000-000-000 nella colonna GUID, l'utente potrebbe scegliere di non aggregare tale valore. Prima di eseguire l'aggregazione, è possibile scrivere "WHERE GUID != "000-000-000"" per filtrare il valore ad alta frequenza.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Opzione 2. Selezionare una chiave di partizione o distribuzione differente

Nell'esempio precedente, se si desidera solo verificare il carico di lavoro della verifica fiscale in tutto il paese, è possibile migliorare la distribuzione dei dati selezionando il numero ID come chiave. La scelta di una chiave di partizione o distribuzione differente consente talvolta di distribuire i dati in modo più uniforme, ma è necessario verificare che la scelta non abbia impatto sulla logica di business. Ad esempio, per calcolare l'importo dell'imposta per ogni stato, è consigliabile specificare _Stato_ come chiave di partizione. Se il problema persiste, provare con l'opzione 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Opzione 3. Aggiungere più chiavi di partizione o distribuzione

Invece di usare solo _Stato_ come chiave di partizione, è possibile impiegare più di una chiave per il partizionamento. Ad esempio, è consigliabile aggiungere un _CAP_ come chiave di partizione aggiuntiva per ridurre le dimensioni della partizione dei dati e distribuire i dati in modo più uniforme.

### <a name="option-4-use-round-robin-distribution"></a>Opzione 4. Usare la distribuzione round robin

Qualora non sia possibile trovare una chiave adeguata per la partizione e la distribuzione, si può usare la distribuzione round robin. La distribuzione round robin considera tutte le righe in modo uguale e le inserisce in modo casuale nei bucket corrispondenti. I dati vengono distribuiti uniformemente ma si perdono le informazioni sulla località, uno svantaggio che riduce anche le prestazioni del processo rispetto ad alcune operazioni. In aggiunta, se si sta eseguendo un'aggregazione per risolvere un problema di chiave differente, il problema dell'asimmetria dei dati non verrà risolto. Per altre informazioni sulla distribuzione round robin, vedere la sezione U-SQL Table Distributions (Distribuzioni di tabelle U-SQL) in [CREATE TABLE (U-SQL): Creating a Table with Schema](https://msdn.microsoft.com/en-us/library/mt706196.aspx#dis_sch) (CREATE TABLE (U-SQL): creazione di una tabella con Schema).

## <a name="solution-2-improve-the-query-plan"></a>Soluzione 2. Migliorare il piano di query

### <a name="option-1-use-the-create-statistics-statement"></a>Opzione 1. Usare l'istruzione CREATE STATISTICS

U-SQL fornisce l'istruzione CREATE STATISTICS nelle tabelle. Questa istruzione offre al Query Optimizer altre informazioni relative alle caratteristiche dei dati, ad esempio la distribuzione dei valori, archiviati in una tabella. Per la maggior parte delle query, il Query Optimizer genera già le statistiche necessarie per un piano di query di elevata qualità. In alcuni casi, potrebbe essere necessario migliorare le prestazioni delle query creando statistiche aggiuntive con CREATE STATISTICS o modificando la struttura delle query. Per altre informazioni, vedere la pagina [CREATE STATISTICS (U-SQL)](https://msdn.microsoft.com/en-us/library/azure/mt771898.aspx) (CREATE STATISTICS (U-SQL)).

Esempio di codice:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Le informazioni statistiche non vengono aggiornate in automatico. Se si aggiornano i dati in una tabella senza generare nuovamente le statistiche, le prestazioni delle query potrebbero risultare ridotte.

### <a name="option-2-use-skewfactor"></a>Opzione 2. Utilizzo di SKEWFACTOR

Se si desidera sommare le imposte per ogni stato, è necessario usare lo stato GROUP BY, un approccio che non evita il problema dell'asimmetria dei dati. Tuttavia, è possibile inserire un suggerimento dati nella query per identificare le asimmetrie dei dati nelle chiavi, in modo da preparare un piano di esecuzione personale.

In genere, è possibile impostare il parametro su 0,5 e 1, laddove 0,5 indica un'asimmetria non netta, mentre 1 indica un'asimmetria marcata. Dal momento che il suggerimento ha un impatto sull'ottimizzazione del piano di esecuzione per l'istruzione corrente e per tutte quelle successive, è quindi necessario assicurarsi di aggiungerlo prima della potenziale aggregazione a livello della chiave con asimmetria.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Esempio di codice:

    //Add a SKEWFACTOR hint.
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

    //Query 2 for Key: Query
    @Display =
        SELECT * FROM @Sessions
            INNER JOIN @Campaigns
                ON @Sessions.Query == @Campaigns.Query
        ;   

### <a name="option-3-use-rowcount"></a>Opzione 3: Usare ROWCOUNT  
Oltre a SKEWFACTOR, per casi specifici di unioni di chiavi asimmetriche, se si sa che gli altri set di righe unite sono ridotti, è possibile indicarlo all'Optimizer tramite l'aggiunta di un suggerimento ROWCOUNT nell'istruzione U-SQL prima di JOIN. In questo modo, l'Optimizer può scegliere una strategia di aggregazione della trasmissione per migliorare le prestazioni. Tenere presente che ROWCOUNT non risolve il problema dell'asimmetria dei dati, ma può offrire aiuto aggiuntivo.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Esempio di codice:

    //Unstructured (24-hour daily log impressions)
    @Huge   = EXTRACT ClientId int, ...
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    //Small subset (that is, ForgetMe opt out)
    @Small  = SELECT * FROM @Huge
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    //Result (not enough information to determine simple broadcast JOIN)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Soluzione 3. Migliorare il riduttore e il combinatore definiti dall'utente

A volte per gestire una logica di processo complessa viene usato un operatore definito dall'utente; in alcuni casi, un riduttore e un combinatore ben scritti possono ridurre il problema dell'asimmetria dei dati.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Opzione 1. Usare un riduttore ricorsivo se possibile

Per impostazione predefinita, un riduttore definito dall'utente viene eseguito in modalità non ricorsiva, ovvero il lavoro di riduzione per una chiave verrà distribuito su un unico vertice. In caso di asimmetria dei dati, i set di dati di grandi dimensioni potrebbero essere elaborati in un unico vertice e richiedere un tempo di esecuzione lungo.

Per migliorare le prestazioni, è possibile aggiungere un attributo nel codice per definire l'esecuzione del riduttore in modalità ricorsiva. A questo punto, i set di dati di grandi dimensioni possono essere distribuiti su più vertici ed eseguiti in parallelo, al fine di velocizzare il processo.

Per trasformare un riduttore non ricorsivo in ricorsivo è necessario assicurarsi che l'algoritmo sia associativo. Ad esempio, la somma è associativa mentre la media non lo è. È necessario anche verificare che l'input e output per il riduttore rispettino lo stesso schema.

Attributo del riduttore ricorsivo:

    [SqlUserDefinedReducer(IsRecursive = true)]

Esempio di codice:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Opzione 2. Usare la modalità di combinazione a livello di riga, se possibile

Analogamente al suggerimento ROWCOUNT per specifici casi di unioni di chiavi asimmetriche, la modalità del combinatore tenta di distribuire set di valori di chiavi asimmetriche su più vertici, in modo da ottenere un'esecuzione simultanea. La modalità del combinatore non può risolvere il problema dell'asimmetria dei dati, ma può offrire un aiuto aggiuntivo per set di valori di chiavi asimmetriche di grandi dimensioni.

Per impostazione predefinita, la modalità del combinatore è Full, ovvero il set di righe a sinistra e il set di righe a destra non possono essere separati. L'impostazione della modalità su Left/Right/Inner consente l'unione a livello di riga. Il sistema separa il set di righe corrispondente e le distribuisce in più vertici che vengono eseguiti in parallelo. Prima di configurare la modalità del combinatore, tuttavia, assicurarsi che sia possibile separare i set di righe corrispondente.

L'esempio seguente illustra un set di righe sinistro separato. Ogni riga di output dipende da una singola riga di input a sinistra e potenzialmente da tutte le righe a destra con lo stesso valore chiave. Se si imposta la modalità del combinatore su Left, il sistema separa il grande set di righe a sinistra in set più piccoli e li assegna a più vertici.

![Illustrazione della modalità del combinatore](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Se si imposta la modalità del combinatore errata, la combinazione è meno efficiente e i risultati potrebbero essere errati.

Attributi della modalità del combinatore:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): ogni riga di output dipende da una singola riga di input a sinistra e potenzialmente da tutte le righe a destra con lo stesso valore chiave.

- SqlUserDefinedCombiner(Mode=CombinerMode.Right): ogni riga di output dipende da una singola riga di input a destra e potenzialmente da tutte le righe a sinistra con lo stesso valore chiave.

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): ogni riga di output dipende da una sola riga di input a sinistra e a destra con lo stesso valore.

Esempio di codice:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }

