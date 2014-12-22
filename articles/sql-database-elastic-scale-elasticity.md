<properties title="Data Dependent Routing" pageTitle="Elasticità di partizionamento" description="Explains concepts and gives examples behind shard elasticity, the ability to scale out Azure SQL databases easily." metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale, elasticity" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Elasticità di partizionamento 

L'**elasticità di partizionamento** consente agli sviluppatori di applicazioni di aumentare e ridurre dinamicamente le risorse del database secondo le esigenze, permettendo l'ottimizzazione delle prestazioni delle applicazioni e anche la riduzione dei costi. La combinazione di Scalabilità elastica per il database SQL di Azure con i [livelli di servizio Basic, Standard e Premium](http://msdn.microsoft.com/it-it/library/azure/dn741340.aspx) offre scenari di elasticità veramente interessanti.  Scalabilità elastica consente il **ridimensionamento orizzontale**, un modello di progettazione in cui i database ("partizioni" in [termini di Scalabilità elastica](sql-database-elastic-scale-glossary.md)) vengono aggiunti o rimossi da un **set di partizioni** per aumentare o ridurre la capacità. In modo analogo, i livelli di servizio del database SQL offrono funzionalità di **ridimensionamento verticale**, in modo che le risorse di un singolo database possano essere aumentate o ridotte in base alla domanda.  Insieme, il ridimensionamento verticale di una singola partizione e il ridimensionamento orizzontale di molte partizioni offrono agli sviluppatori di applicazioni un ambiente molto flessibile che può essere ridimensionato per soddisfare le esigenze di prestazioni, capacità e ottimizzazione dei costi.

### Esempio di ridimensionamento orizzontale: Picco della domanda per un concerto

Uno scenario canonico per il ridimensionamento orizzontale è un'applicazione che elabora le transazioni per i biglietti di un concerto. Con un volume normale di clienti, l'applicazione usa risorse minime del database per gestire le transazioni di acquisto.  Tuttavia, quando vengono messi in vendita i biglietti di un concerto famoso, un singolo database non può gestire l'enorme picco nella domanda dei clienti. 

Per elaborare l'aumento significativo delle transazioni, il ridimensionamento dell'applicazione avviene orizzontalmente. L'applicazione è ora in grado di distribuire il carico delle transazioni tra molte partizioni. Quando le risorse aggiuntive non sono più necessarie, il livello di database viene ridotto per l'uso normale. In questo caso il ridimensionamento orizzontale consente a un'applicazione di aumentare le risorse per rispondere alle domanda dei clienti e di ridurle quando non sono più necessarie.   

### Esempio di ridimensionamento verticale: Telemetria

Uno scenario canonico per il ridimensionamento verticale riguarda un'applicazione che usa un **set di partizioni** per archiviare la telemetria operativa. In questo scenario è consigliabile inserire tutti i dati di telemetria per un singolo giorno in una singola partizione. In questa applicazione i dati per il giorno corrente sono inseriti in una partizione e viene eseguito il provisioning di una nuova partizione per i giorni successivi. I dati operativi possono quindi essere obsoleti ed essere sottoposti a query nel modo appropriato. 

Per inserire dati di telemetria con carichi elevati, è consigliabile usare un livello di servizio più elevato. In altre parole, un database Premium è preferibile a un database Basic. Dopo che il database ha raggiunto la propria capacità, passa dall'inserimento all'analisi e alla creazione di report. A tale scopo, le prestazioni del livello Standard sono appropriate per l'attività. Pertanto è possibile ridimensionare verticalmente il livello di servizio nelle partizioni (eccetto quella creata più di recente) per soddisfare i requisiti di prestazioni inferiori per i dati meno recenti. 

Il ridimensionamento verticale può anche essere usato per migliorare le prestazioni di un singolo database per ottenere un miglioramento delle prestazioni. Ne è un esempio un'applicazione di archiviazione delle imposte. In fase di archiviazione, è consigliabile mantenere tutti i dati di un singolo cliente nello stesso database e migliorare le prestazioni di tale partizione. A seconda dell'applicazione, il ridimensionamento verticale e orizzontale delle risorse è vantaggioso per ottimizzare i requisiti in termini di costi e prestazioni. 

Insieme, sia il ridimensionamento orizzontale che quello verticale del livello di database sono alla base delle applicazioni di Scalabilità elastica e dell'elasticità di partizionamento. 

Questo documento fornisce il contesto per gli scenari di elasticità di partizionamento, nonché riferimenti a implementazioni di esempio di ridimensionamento sia orizzontale che verticale. 

## Meccanismi dell'elasticità di partizionamento 

La funzione di ridimensionamento verticale e orizzontale è costituita da tre componenti di base: 

1. **Telemetria**
2. **Regola**
3. **Azione**   

## <a name="telemetry"> </a>Telemetria

L'**elasticità basata sui dati** è il fulcro di un'applicazione di Scalabilità elastica. A seconda dei requisiti di prestazioni, usare la telemetria per prendere decisioni basate sui dati in merito all'applicazione del ridimensionamento orizzontale o verticale.  

#### Origini dati di telemetria
Nel contesto del database SQL di Azure esistono alcune pratiche origini chiave che possono essere usate come origini dati per l'elasticità di partizionamento. 

1. La **telemetria delle prestazioni** viene esposta per durate di cinque minuti nella visualizzazione **sys.resource_stats**. 
2. La **telemetria della capacità del database** oraria viene esposta tramite la visualizzazione **sys.resource_usage**.  

È possibile analizzare l'uso di risorse delle prestazioni eseguendo la query seguente sul database master, dove 'Shard_20140623' è il nome del database di destinazione. 

    SELECT TOP 10 *  
    FROM sys.resource_stats  
    WHERE database_name = 'Shard_20140623'  
    ORDER BY start_time DESC 

La **telemetria delle prestazioni** può essere riepilogata per un intervallo di tempo (sette giorni nella query seguente) allo scopo di rimuovere i comportamenti temporanei. 

    SELECT  
    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent', 
        max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent', 
        avg(avg_physical_data_read_percent) AS 'Average Physical Data Read Utilization In Percent', 
        max(avg_physical_data_read_percent) AS 'Maximum Physical Data Read Utilization In Percent', 
        avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent', 
        max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent', 
        avg(active_session_count) AS 'Average # of Sessions', 
        max(active_session_count) AS 'Maximum # of Sessions', 
        avg(active_worker_count) AS 'Average # of Workers', 
        max(active_worker_count) AS 'Maximum # of Workers' 
    FROM sys.resource_stats  
    WHERE database_name = ' Shard_20140623' AND start_time > DATEADD(day, -7, GETDATE()); 

La **capacità del database** può essere misurata con una query simile nella visualizzazione **sys.resource_usage**. La voce max della colonna **storage_in_megabytes** restituisce le dimensioni correnti del database. I dati di telemetria sono utili per il ridimensionamento orizzontale di un'applicazione quando una determinata partizione raggiunge la propria capacità. 

    SELECT TOP 10 * 
    FROM [sys].[resource_usage] 
    WHERE database_name = 'Shard_20140623'  
    ORDER BY time DESC 

Poiché i dati sono inseriti in una determinata partizione, è utile effettuare una previsione anticipata di un giorno per determinare se la partizione dispone di capacità sufficiente per gestire il carico di lavoro successivo. Pur non essendo una vera implementazione di regressione lineare, la query seguente restituisce la differenza massima della capacità del database tra due giorni consecutivi.  Tali dati di telemetria possono quindi essere applicati a una regola che comporterà l'esecuzione di un'azione (o non azione). 

    WITH MaxDatabaseDailySize AS( 
        SELECT 
            ROW_NUMBER() OVER (ORDER BY convert(date, [time]) DESC) as [Order], 
            CONVERT(date,[time]) as [date],  
            MAX(storage_in_megabytes) as [MaxSizeDay] 
        FROM [sys].[resource_usage] 
        WHERE  
            database_name = 'Shard_20140623' 
        GROUP BY CONVERT(date,[time]) 
        ) 
    
    SELECT 
        MAX(ISNULL(Size.[MaxSizeDay] - PreviousDaySize.[MaxSizeDay], 0)) 
    FROM  
        MaxDatabaseDailySize Size INNER JOIN 
        MaxDatabaseDailySize PreviousDaySize ON Size.[order]+1 = PreviousDaySize.[order] 
    WHERE 
        Size.[order] < 8 

## <a name="rule"></a>Regola  

La regola è il motore decisionale che determina se un'azione viene o meno eseguita. Alcune regole sono molto semplici e altre molto più complesse. Come illustrato nel frammento di codice riportato di seguito, una regola incentrata sulla capacità può essere configurata in modo che quando una partizione raggiunge $SafetyMargin, ad esempio, 80% della capacità massima, viene eseguito il provisioning di una nuova partizione.

    # Determine if the current DB size plus the maximum daily delta size is greater than the threshold 
    if( ($CurrentDbSizeMb + $MaxDbDeltaMb) -gt ($MaxDbSizeMb * $SafetyMargin))  
    {#provision new shard} 

Considerate le origini dati precedenti, è possibile formulare una serie di regole per l'esecuzione di numerosi scenari di elasticità di partizionamento. 

## <a name="action"></a>Azione  

In base al risultato della regola, l'azione (o non di azione) costituisce il risultato. Le due azioni più comuni sono:

* Aumento o diminuzione del livello di servizio o del livello di prestazioni della partizione. 
* Aggiunta o rimozione di una partizione da un set di partizioni.

Si noti che nelle soluzioni di ridimensionamento orizzontale e verticale il risultato di un'azione non è immediato. Quando ad esempio viene applicato il ridimensionamento verticale, l'esecuzione del comando ALTER DATABASE per aumentare il livello di servizio di un database Basic a un database Premium richiede una quantità di tempo variabile. La durata dipende in larga misura dalle dimensioni del database. Per altre informazioni, vedere [Modifica dei livelli di servizio e dei livelli di prestazioni di un database](http://msdn.microsoft.com/library/azure/dn369872.aspx). Analogamente, anche l'allocazione o il provisioning di una nuova partizione non è un'operazione istantanea. Pertanto, per il ridimensionamento orizzontale e verticale, nelle applicazioni deve essere considerato un intervallo diverso da zero per la modifica o il provisioning di un nuovo database.  

## Esempio di scenario di elasticità di partizionamento 

L'esempio illustrato nella figura seguente evidenzia due scenari di scalabilità elastica: 
1. Ridimensionamento di una mappa di partizioni. 
2. Ridimensionamento verticale di una singola partizione.  

![Operationl Data Ingestion][1]

Per il ridimensionamento orizzontale, viene usata una regola (basata sulle dimensioni del database o dei dati) per eseguire il provisioning di una nuova partizione e registrarla nella mappa di partizioni, aumentando quindi orizzontalmente il livello del database. In secondo luogo, per il ridimensionamento verticale viene implementata una seconda regola in cui viene effettuato il downgrade da Premium Edition a Standard o Basic Edition per qualsiasi partizione più vecchia di un giorno. 

Considerare di nuovo lo scenario di telemetria: l'applicazione viene partizionata in base alla data. Raccoglie i dati di telemetria continuamente, richiedendo un'edizione ad alte prestazioni in fase di caricamento, ma prestazioni ridotte man mano che i dati diventano obsoleti. I dati del giorno corrente [Tnow] vengono scritti in un database ad alte prestazioni (Premium). Quando l'orologio raggiunge la mezzanotte, la partizione del giorno precedente (ora [T-1]) non viene più usata per l'inserimento. I dati correnti vengono inseriti dal giorno [Tnow] corrente. Prima del giorno successivo devono essere effettuati il provisioning e la registrazione della mappa partizioni ([T+1]).  

Questa operazione può essere eseguita mediante il provisioning anticipato di una nuova partizione ogni nuovo giorno o il provisioning di una nuova partizione quando quella corrente ([Tnow]) è prossima alla capacità massima. L'uso di uno di questi metodi consente di mantenere la posizione dei dati per tutti i dati di telemetria scritti per un determinato giorno. È anche possibile applicare il partizionamento per ora per una maggiore granularità. Dopo il provisioning di una nuova partizione e poiché [T-1] si usa per l'esecuzione di query e la creazione di report, è preferibile ridurre il livello di prestazioni del database per ridurne i costi. Dato che il contenuto del database diventa obsoleto, è possibile ridurre ulteriormente il livello di prestazioni e/o archiviare il contenuto dei database in Archiviazione di Azure oppure eliminarlo a seconda dell'applicazione. 

## Esecuzione di scenari di elasticità di partizionamento  

Per facilitare l'effettiva implementazione di scenari di ridimensionamento orizzontale e verticale, sono stati creati e pubblicati in Script Center numerosi [script di esempio di elasticità di partizionamento](http://go.microsoft.com/?linkid=9862617). Scritti per essere eseguiti nel servizio Automazione di Azure, i runbook di PowerShell forniscono numerosi metodi che interagiscono con le librerie client di Scalabilità elastica e il database SQL di Azure.  Usando come base questi esempi di codice o estraendo frammenti, è possibile creare gli script necessari per automatizzare scenari di ridimensionamento orizzontale, verticale o entrambi per la propria applicazione. 


[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-elasticity/data-ingestion.png

<!--anchors-->
[Telemetry]:#telemetry
[Rule]:#rule
[Action]:#action
