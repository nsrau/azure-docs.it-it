---
title: Monitoraggio delle prestazioni del database nel database SQL di Azure | Documentazione Microsoft
description: Informazioni sulle opzioni per il monitoraggio del database con gli strumenti di Azure e le viste a gestione dinamica.
keywords: monitoraggio database, prestazioni database cloud
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 09/20/2017
ms.author: carlrab
ms.openlocfilehash: ba2239b1a4cd14f7723e88ee83f7ad93da717e0a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Monitoraggio delle prestazioni del database nel database SQL di Azure
Il monitoraggio delle prestazioni di un database SQL in Azure inizia con il monitoraggio dell'utilizzo delle risorse rispetto al livello di prestazioni scelto per il database. Il monitoraggio consente di determinare se il database ha capacità in eccesso o se presenta problemi perché è stato superato il limite massimo di risorse e quindi decidere se è opportuno modificare il livello di prestazioni e il [livello di servizio](sql-database-service-tiers.md) del database. È possibile monitorare il database con strumenti grafici nel [portale di Azure](https://portal.azure.com) o con le [viste a gestione dinamica di SQL](https://msdn.microsoft.com/library/ms188754.aspx).

> [!TIP]
> Usare [Intelligent Insights SQL di Azure](sql-database-intelligent-insights.md) per il monitoraggio automatico delle prestazioni del database. Dopo aver rilevato un problema di prestazioni, viene generato un log di diagnostica con i dettagli e l'analisi della causa radice del problema. Quando possibile viene fornita un'indicazione di miglioramento delle prestazioni.
>

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorare i database tramite il portale di Azure
Nel [portale di Azure](https://portal.azure.com/)è possibile monitorare l'utilizzo di un database singolo selezionandolo e facendo clic sul grafico **Monitoraggio** . Verrà visualizzata una finestra della **metrica** in cui è possibile apportare modifiche facendo clic su l pulsante **Modifica grafico**. Aggiungere le metriche seguenti

* Percentuale CPU
* Percentuale di DTU
* Percentuale di I/O di dati
* Percentuale di dimensioni del database

Dopo aver aggiunto queste metriche, è possibile continuare a visualizzarle nel grafico **Monitoraggio** con altre informazioni nella finestra **Metrica**. Tutte le quattro metriche mostrano la percentuale media di utilizzo relativa alla **DTU** del database. Per informazioni dettagliate sulle DTU, vedere i [livelli di servizio](sql-database-service-tiers.md) .

![Monitoraggio del livello di servizio delle prestazioni del database.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

È inoltre possibile configurare gli avvisi sulle metriche delle prestazioni. Scegliere il pulsante **Aggiungi avviso** nella finestra **Metrica**. Seguire la procedura guidata per configurare l'avviso. È possibile scegliere di ricevere un avviso se la metrica supera una determinata soglia o scende al di sotto di una determinata soglia.

Ad esempio, se si prevede un aumento del carico di lavoro sul database, è possibile scegliere di configurare un avviso di posta elettronica ogni volta che il database raggiunge l'80% per una qualsiasi delle metriche delle prestazioni. È possibile utilizzarlo come un preavviso per capire quando potrebbe essere necessario passare a livello di prestazioni superiore.

Le metriche delle prestazioni consentono inoltre di determinare se è possibile effettuare il downgrade a un livello di prestazioni inferiore. Presupporre di utilizzare un database Standard S2 e tutte le metriche delle prestazioni mostrano che il database in media non utilizza più del 10% in un dato momento. È probabile che il database funzioni bene in Standard S1. Tuttavia, prestare attenzione ai picchi o alle fluttuazioni dei carichi di lavoro prima di decidere di passare a un livello di prestazioni inferiore.

## <a name="monitor-databases-using-dmvs"></a>Monitorare i database con le viste a gestione dinamica
Le stesse metriche esposte nel portale sono disponibili anche tramite le viste di sistema: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) nel database **master** logico del server e [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) nel database utente. Usare **sys.resource_stats** se è necessario monitorare meno dati granulari in un periodo di tempo più lungo. Usare **sys.dm_db_resource_stats** se è necessario monitorare più dati granulari in un periodo di tempo più breve. Per ulteriori informazioni, vedere la [Guida alle prestazioni del database SQL di Azure](sql-database-single-database-monitor.md#monitor-resource-use).

> [!NOTE]
> **sys.dm_db_resource_stats** restituisce un set di risultati vuoto quando viene usato nei database Web e Business Edition, che sono stati ritirati.
>
>

### <a name="monitor-resource-use"></a>Monitorare l'uso delle risorse

È possibile monitorare l'utilizzo di risorse usando [Informazioni dettagliate prestazioni query del Database SQL](sql-database-query-performance.md) e [Archivio query](https://msdn.microsoft.com/library/dn817826.aspx).

È inoltre possibile monitorare l'utilizzo tramite queste due viste:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
È possibile usare la vista [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) in ogni database SQL. La vista **sys.dm_db_resource_stats** mostra i dati recenti sull'uso delle risorse rispetto al livello di servizio. Informazioni relative a percentuali medie della CPU, dati I/O, scritture nei log e memoria vengono registrate ogni 15 secondi e vengono mantenute per un'ora.

Poiché questa vista fornisce una visione più granulare sull'uso delle risorse, usare prima **sys.dm_db_resource_stats** per eventuali analisi o risoluzioni di problemi allo stato corrente. Ad esempio, questa query descrive l'uso medio e massimo delle risorse per il database corrente nell'ultima ora:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Per altre query, vedere gli esempi in [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

#### <a name="sysresourcestats"></a>sys.resource_stats
La vista [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) nel database **master** fornisce informazioni aggiuntive utili per il monitoraggio dell'uso delle prestazioni del database SQL al relativo livello di servizio e di prestazioni. I dati vengono raccolti ogni 5 minuti e conservati per circa 14 giorni. Questa vista è utile per analisi cronologiche a lungo termine dell'uso delle risorse del database SQL.

Il grafico seguente illustra l'uso di risorse della CPU per un database Premium con livello di prestazioni P2 per ogni ora nell'arco di una settimana. Questo grafico inizia di lunedì, con 5 giorni lavorativi e un fine settimana in cui l'uso di risorse nell'applicazione è molto inferiore.

![Uso delle risorse del database SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

In base ai dati, per il livello di prestazioni P2 il carico massimo della CPU di questo database attualmente supera di poco il 50% dell'uso della CPU (a mezzogiorno di martedì). Se la CPU è il fattore più importante nel profilo delle risorse dell'applicazione, si può scegliere P2 come livello di prestazioni idoneo a garantire che il carico di lavoro sia sempre adeguato. Se si prevede che un'applicazione presenti un incremento nel tempo, è consigliabile avere un buffer di risorse aggiuntivo, in modo che l'applicazione non raggiunga mai il limite del livello di prestazioni. Se si aumenta il livello di prestazioni, è possibile evitare gli errori visibili ai clienti che si possono verificare se un database non ha risorse sufficienti per elaborare in modo efficiente le richieste, in particolare in ambienti sensibili alla latenza. Un esempio è costituito da un database che supporta un'applicazione per la creazione di pagine Web in base ai risultati delle chiamate al database.

Altri tipi di applicazioni possono interpretare in modo diverso lo stesso grafico. Se ad esempio un'applicazione prova a elaborare i dati del libro paga ogni giorno e usa lo stesso grafico, questo tipo di modello di processo batch potrebbe essere eseguito correttamente con un livello di prestazioni P1. Il valore di DTU del livello di prestazioni P1 è pari a 100, mentre quello del livello P2 è pari a 200. Il livello di prestazioni fornito dal livello di servizio P2 è doppio rispetto a quello fornito dal livello P1. Il 50% dell'uso della CPU nel livello P2 equivale quindi al 100% dell'uso della CPU in P1. Se l'applicazione non presenta timeout, è possibile che non sia rilevante se il completamento di un processo richiede 2 ore o 2,5 ore, se viene completato in giornata. Per un'applicazione che rientra in questa categoria è probabilmente sufficiente usare il livello di prestazioni P1. Si può sfruttare la presenza di periodi di tempo durante il giorno in cui l'uso delle risorse è inferiore, in modo da spalmare un picco massimo in altri momenti nel corso della giornata. Il livello di prestazioni P1 può essere ottimale per questo tipo di applicazione e può consentire di limitare i costi, purché i processi vengano completati in orario ogni giorno.

Il database SQL di Azure espone le informazioni sulla risorsa usata per ogni database attivo nella vista **sys.resource_stats** del database **master** in ogni server. I dati nella tabella vengono aggregati per intervalli di 5 minuti. Con i livelli di servizio Basic, Standard e Premium, è possibile che la visualizzazione dei dati nella tabella richieda più di 5 minuti, quindi i dati risultano più utili per le analisi cronologiche, invece che per le analisi in tempo quasi reale. Eseguire una query nella vista **sys.resource_stats** per visualizzare la cronologia recente di un database e per verificare se la prenotazione scelta ha offerto le prestazioni desiderate quando necessario.

> [!NOTE]
> È necessario essere connessi al database **master** del server logico del database SQL per eseguire la query **sys.resource_stats** negli esempi seguenti.
> 
> 

Questo esempio illustra la modalità di esposizione dei dati in questa vista:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Vista del catalogo sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

L'esempio successivo mostra i diversi modi in cui è possibile usare la vista del catalogo **sys.resource_stats** per ottenere informazioni sul modo in cui il database SQL usa le risorse:

1. Per esaminare l'uso delle risorse nella settimana precedente per il database userdb1, è possibile eseguire questa query:
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Per verificare l'idoneità del carico di lavoro per il livello di prestazioni, è necessario eseguire il drill-down in ogni aspetto delle metriche delle risorse, ovvero CPU, operazioni di lettura e scrittura, numero di thread di lavoro e numero di sessioni. Ecco la query modificata usando**sys.resource_stats** per segnalare i valori medi e massimi di queste metriche delle risorse:
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. Con queste informazioni sui valori medi e massimi di ogni metrica delle risorse è possibile valutare l'idoneità del livello di prestazioni scelto in rapporto al carico di lavoro. I valori medi di **sys.resource_stats** offrono in genere una buona baseline da usare nelle dimensioni di destinazione. Deve trattarsi dello strumento di misurazione principale. È ad esempio possibile che si usi il livello di servizio Standard con il livello di prestazioni S2. Le percentuali medie di uso per la CPU e per operazioni di scrittura e lettura I/O sono inferiori al 40%, il numero medio di thread di lavoro è inferiore a 50 e il numero medio di sessioni è inferiore a 200. Il carico di lavoro potrebbe essere idoneo per il livello di prestazioni S1. È facile verificare se il database rientra nei limiti dei thread di lavoro e delle sessioni. Per verificare se un database può rientrare in un livello di prestazioni inferiore prendendo in considerazione la CPU e le operazioni di lettura e scrittura, è sufficiente dividere il numero di DTU del livello di prestazioni inferiore per il numero di DTU del livello di prestazioni corrente e moltiplicare il risultato per 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Il risultato rappresenta la differenza di prestazioni relativa, in percentuale, tra i due livelli di prestazioni. Se l'uso delle risorse non supera questa quantità, il carico di lavoro potrebbe essere idoneo per il livello di prestazioni inferiore. È tuttavia necessario esaminare anche tutti gli intervalli dei valori di uso delle risorse e determinare, a livello di percentuale, la frequenza con cui il carico di lavoro del database può rientrare nel livello di prestazioni inferiore. La query seguente genera questa percentuale per ogni dimensione di risorsa, in base alla soglia del 40% calcolata in questo esempio:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    In base all'obiettivo del livello di servizio (SLO) del database, è possibile stabilire se il carico di lavoro può rientrare nel livello di prestazioni inferiore. Se l'obiettivo del livello di servizio del carico di lavoro del database è 99,9% e la query precedente restituisce valori superiori al 99,9% per tutte e tre le dimensioni della risorsa, è probabile che il carico di lavoro possa rientrare nel livello di prestazioni inferiore.
   
    La percentuale calcolata in precedenza consente inoltre di stabilire se è opportuno usare il livello di prestazioni superiore a quello attualmente in uso per soddisfare l'obiettivo del livello di servizio. Ad esempio, userdb1 mostra il valore di uso della CPU seguente per la settimana precedente:
   
   | Percentuale CPU media | Percentuale CPU massima |
   | --- | --- |
   | 24,5 |100,00 |
   
    L'utilizzo medio della CPU corrisponde a circa un quarto del limite del livello di prestazioni, che potrebbe rientrare nel livello di prestazioni del database. Il valore massimo corrisponde tuttavia al limite del livello di prestazioni del database. Per decidere se è necessario passare al livello di prestazioni superiore a quello attualmente in uso, Considerare il numero di volte in cui il carico di lavoro raggiunge il 100% e quindi confrontare tale numero con l'obiettivo del livello di servizio del carico di lavoro del database.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Se questa query restituisce un valore inferiore a 99,9% per qualsiasi delle tre dimensioni della risorsa, è consigliabile prendere in esame la possibilità di passare al livello di prestazioni superiore o usare tecniche di ottimizzazione dell'applicazione per ridurre il carico nel database SQL.
4. Questo esercizio tiene in considerazione anche l'aumento di carico di lavoro previsto in futuro.

Per i pool elastici, è possibile monitorare i singoli database nel pool con le tecniche descritte in questa sezione. Tuttavia, è anche possibile monitorare il pool nel complesso. Per informazioni, vedere [Monitorare e gestire un elastico](sql-database-elastic-pool-manage-portal.md).


### <a name="maximum-concurrent-requests"></a>Numero massimo di richieste simultanee
Per visualizzare il numero di richieste simultanee, eseguire questa query Transact-SQL sul database SQL:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Per analizzare il carico di lavoro di un database SQL locale, modificare questa query in modo che applichi il filtro al database specifico da analizzare. Se ad esempio è presente un database locale denominato MyDatabase, questa query Transact-SQL restituirà il numero di richieste simultanee in tale database:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Questo è solo uno snapshot in un singolo punto nel tempo. Per una migliore comprensione del carico di lavoro e dei requisiti relativi alle richieste simultanee, sarà necessario raccogliere molti campioni nel tempo.

### <a name="maximum-concurrent-logins"></a>Numero massimo di accessi simultanei
Per avere un'idea della frequenza degli accessi, è possibile analizzare i modelli dell'utente e dell'applicazione. È inoltre possibile eseguire carichi reali in un ambiente di test per assicurarsi di non raggiungere questo o gli altri limiti descritti in questo argomento. Non è disponibile alcuna vista di query singola o vista a gestione dinamica per la visualizzazione dei numeri o della cronologia degli accessi simultanei.

Se più client usano la stessa stringa di connessione, il servizio autentica ogni account di accesso. Se 10 utenti si connettono contemporaneamente a un database con nome utente e password identici, ci saranno dieci account di accesso simultanei. Questo limite si applica solo alla durata dell'account di accesso e dell'autenticazione. Se gli stessi 10 utenti si connettono in sequenza al database, il numero di account di accesso simultanei non sarà mai superiore a uno.

> [!NOTE]
> Questo limite attualmente non si applica ai database in pool elastici.
> 
> 

### <a name="maximum-sessions"></a>Numero massimo di sessioni
Per visualizzare il numero di sessioni attive correnti, eseguire questa query Transact-SQL sul database SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Se si analizza un carico di lavoro di SQL Server locale, modificare la query per concentrarsi su un database specifico. Questa query consente di determinare le possibili esigenze della sessione per tale database se si sta prendendo in considerazione lo spostamento nel database SQL di Azure.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Queste query restituiscono un conteggio temporizzato. Se si raccolgono più campioni nel tempo, si otterrà una comprensione ottimale dell'uso della sessione.

Per l'analisi del database SQL, è possibile anche ottenere dati statistici cronologici sulle sessioni eseguendo query della visualizzazione [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ed esaminando la colonna **active_session_count**. 

## <a name="next-steps"></a>Passaggi successivi

- Ottimizzare automaticamente gli indici di database e dei piani di esecuzione delle query usando [Ottimizzazione automatica del database SQL di Azure](sql-database-automatic-tuning.md).
- Monitorare le prestazioni del database automaticamente usando [Intelligent Insights SQL di Azure](sql-database-intelligent-insights.md). Questa funzionalità offre informazioni di diagnostica e analisi della causa radice dei problemi di prestazioni.
