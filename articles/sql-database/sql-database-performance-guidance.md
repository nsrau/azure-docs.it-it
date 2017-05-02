---
title: Prestazioni del database SQL di Azure per i singoli database | Microsoft Docs
description: "Questo articolo può essere utile per determinare il livello di servizio da scegliere per l&quot;applicazione. Illustra anche come ottimizzare l&quot;applicazione per ottenere il massimo dal database SQL di Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: dd8d95fa-24b2-4233-b3f1-8e8952a7a22b
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: a8a9d6efb299d0958d4024e62b2b24a76d768ddf
ms.lasthandoff: 04/15/2017


---
# <a name="azure-sql-database-and-performance-for-single-databases"></a>Database SQL di Azure e prestazioni per i singoli database
Il database SQL di Azure offre quattro [livelli di servizio](sql-database-service-tiers.md): Basic, Standard, Premium e Premium RS. Ogni livello di servizio isola rigorosamente le risorse che possono essere usate dal database SQL e assicura prestazioni prevedibili per tale livello di servizio. In questo articolo sono disponibili indicazioni utili per scegliere il livello di servizio per l'applicazione. Viene illustrato anche come ottimizzare l'applicazione per ottenere il massimo dal database SQL di Azure.

> [!NOTE]
> Questo articolo è incentrato sulle indicazioni relative alle prestazioni per singoli database nel database SQL di Azure. Per indicazioni sulle prestazioni relative ai pool elastici, vedere le [considerazioni su prezzo e prestazioni per i pool elastici](sql-database-elastic-pool-guidance.md). Si noti, tuttavia, che molte raccomandazioni sull'ottimizzazione contenute in questo articolo possono essere applicate ai database in un pool elastico ottenendo vantaggi simili a livello di prestazioni.
>
>

## <a name="why-service-tiers"></a>Vantaggi dei livelli di servizio
Anche se ogni carico di lavoro può presentare caratteristiche diverse, lo scopo dei livelli di servizio è offrire la prevedibilità delle prestazioni a diversi livelli. I clienti con requisiti di risorse di database su larga scala possono operare in un ambiente di calcolo più dedicato.

### <a name="common-service-tier-use-cases"></a>Casi d'uso comuni dei livelli di servizio
#### <a name="basic"></a>Basic
* **Si sta iniziando a usare il database SQL di Azure**. Le applicazioni sottoposte spesso a sviluppo non necessitano di livelli di prestazioni elevati. I database Basic costituiscono un ambiente ideale per lo sviluppo di database a una fascia di prezzo conveniente.
* **È presente un database con un singolo utente**. Le applicazioni in cui un singolo utente è associato a un database non sono in genere caratterizzate da requisiti elevati per concorrenza e prestazioni. Queste applicazioni sono candidati ottimali per il livello di servizio Basic.

#### <a name="standard"></a>Standard
* **Il database presenta più richieste simultanee**. Le applicazioni che gestiscono più utenti contemporaneamente necessitano in genere di livelli di prestazioni più elevate. I siti Web con traffico moderato o le applicazioni a livello di reparto aziendale che richiedono più risorse sono ad esempio candidati ottimali per il livello di servizio Standard.

#### <a name="premium"></a>Premium
La maggior parte dei casi d'uso del livello di servizio Premium presenta una o più di queste caratteristiche:

* **Picchi di carico elevati**. Un'applicazione che richiede una quantità elevata di CPU, memoria o input/output (I/O) per il completamento delle operazioni necessita di un livello a prestazioni elevate dedicato. Il livello di servizio Premium è ad esempio ideale nel caso in cui un'operazione di database utilizzi più core CPU per un lungo periodo di tempo.
* **Molte richieste simultanee**. Alcune applicazioni di database gestiscono molte richieste simultanee, ad esempio durante l'utilizzo di un sito Web con un elevato volume di traffico. Per i livelli di servizio Basic e Standard sono previsti limiti al numero di richieste simultanee per ogni database. Per le applicazioni per cui sono richieste più connessioni devono essere selezionate dimensioni di prenotazione appropriate per gestire il numero massimo di richieste necessarie.
* **Bassa latenza**. Alcune applicazioni devono garantire una risposta dal database in tempi minimi. Se una stored procedure specifica viene chiamata durante un'operazione più ampia del cliente, potrebbe essere necessario che la risposta alla chiamata in questione venga assicurata in non più di 20 millisecondi nel 99% dei casi. Questo tipo di applicazione trarrà vantaggio dal livello di servizio Premium per assicurare la disponibilità della potenza di calcolo necessaria.

* **Premium RS**. Progettato per i clienti con carichi di lavoro con un numero elevato di operazioni di I/O, ma che non richiedono una garanzia di disponibilità massima. Gli esempi includono test di carichi di lavoro ad alte prestazioni e carichi di lavoro di analisi in cui il database non è il sistema di registrazione.

Il livello di servizio esatto necessario per il database SQL dipende dai requisiti del carico massimo per ogni dimensione di risorsa. È possibile che alcune applicazioni usino quantità irrilevanti di una singola risorsa, ma abbiano requisiti significativi per altre.

## <a name="service-tier-capabilities-and-limits"></a>Limiti e funzionalità dei livelli di servizio
Ogni livello di servizio e prestazione è associato a limiti e caratteristiche di prestazione differenti. La tabella descrive queste caratteristiche per un singolo database.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!IMPORTANT]
> I clienti che scelgono livelli di prestazioni P11 e P15 possono usare fino a 4 TB di spazio di archiviazione incluso senza alcun costo aggiuntivo. L'opzione 4 TB è attualmente in anteprima pubblica nelle aree seguenti: Stati Uniti orientali 2, Stati Uniti occidentali, Europa occidentale, Asia sud-orientale, Giappone orientale, Australia orientale, Canada centrale e Canada orientale.
>

### <a name="maximum-in-memory-oltp-storage"></a>Archiviazione di OLTP in memoria massima
Per monitorare l'uso dell'archiviazione in memoria di Azure è possibile usare la vista **sys.dm_db_resource_stats**. Per altre informazioni sul monitoraggio, vedere [Monitorare l'archiviazione OLTP in memoria](sql-database-in-memory-oltp-monitoring.md).

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

## <a name="monitor-resource-use"></a>Monitorare l'uso delle risorse

È possibile monitorare l'utilizzo di risorse usando [Informazioni dettagliate prestazioni query del Database SQL](sql-database-query-performance.md) e [Archivio query](https://msdn.microsoft.com/library/dn817826.aspx).

È inoltre possibile monitorare l'utilizzo tramite queste due viste:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
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

### <a name="sysresourcestats"></a>sys.resource_stats
La vista [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) nel database **master** fornisce informazioni aggiuntive utili per il monitoraggio dell'uso delle prestazioni del database SQL al relativo livello di servizio e di prestazioni. I dati vengono raccolti ogni 5 minuti e conservati per circa 14 giorni. Questa vista è utile per analisi cronologiche a lungo termine dell'uso delle risorse del database SQL.

Il grafico seguente illustra l'uso di risorse della CPU per un database Premium con livello di prestazioni P2 per ogni ora nell'arco di una settimana. Questo grafico inizia di lunedì, con 5 giorni lavorativi e un fine settimana in cui l'uso di risorse nell'applicazione è molto inferiore.

![Uso delle risorse del database SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

In base ai dati, per il livello di prestazioni P2 il carico massimo della CPU di questo database attualmente supera di poco il 50% dell'uso della CPU (a mezzogiorno di martedì). Se la CPU è il fattore più importante nel profilo delle risorse dell'applicazione, si può scegliere P2 come livello di prestazioni idoneo a garantire che il carico di lavoro sia sempre adeguato. Se si prevede che un'applicazione presenti un incremento nel tempo, è consigliabile avere un buffer di risorse aggiuntivo, in modo che l'applicazione non raggiunga mai il limite del livello di prestazioni. Se si aumenta il livello di prestazioni, è possibile evitare gli errori visibili ai clienti che si possono verificare se un database non ha risorse sufficienti per elaborare in modo efficiente le richieste, in particolare in ambienti sensibili alla latenza. Un esempio è costituito da un database che supporta un'applicazione per la creazione di pagine Web in base ai risultati delle chiamate al database.

Altri tipi di applicazioni possono interpretare in modo diverso lo stesso grafico. Se ad esempio un'applicazione prova a elaborare i dati del libro paga ogni giorno e usa lo stesso grafico, questo tipo di modello di processo batch potrebbe essere eseguito correttamente con un livello di prestazioni P1. Il valore di DTU del livello di prestazioni P1 è pari a 100, mentre quello del livello P2 è pari a 200. Il livello di prestazioni fornito dal livello di servizio P2 è doppio rispetto a quello fornito dal livello P1. Il 50% dell'uso della CPU nel livello P2 equivale quindi al 100% dell'uso della CPU in P1. Se l'applicazione non presenta timeout, è possibile che non sia rilevante se il completamento di un processo richiede 2 ore o 2,5 ore, se viene completato in giornata. Per un'applicazione che rientra in questa categoria è probabilmente sufficiente usare il livello di prestazioni P1. Si può sfruttare la presenza di periodi di tempo durante il giorno in cui l'uso delle risorse è inferiore, in modo da spalmare un picco massimo in altri momenti nel corso della giornata. Il livello di prestazioni P1 può essere ottimale per questo tipo di applicazione e può consentire di limitare i costi, purché i processi vengano completati in orario ogni giorno.

Il database SQL di Azure espone le informazioni sulla risorsa usata per ogni database attivo nella vista **sys.resource_stats** del database **master** in ogni server. I dati nella tabella vengono aggregati per intervalli di 5 minuti. Con i livelli di servizio Basic, Standard, Premium e Premium RS, è possibile che la visualizzazione dei dati nella tabella richieda più di 5 minuti, quindi i dati risultano più utili per le analisi cronologiche anziché per le analisi in tempo quasi reale. Eseguire una query nella vista **sys.resource_stats** per visualizzare la cronologia recente di un database e per verificare se la prenotazione scelta ha offerto le prestazioni desiderate quando necessario.

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

## <a name="tune-your-application"></a>Ottimizzare l'applicazione
Nel sistema SQL Server tradizionale in locale il processo di pianificazione della capacità iniziale è spesso separato dal processo di esecuzione di un'applicazione in produzione. Vengono acquistate prima di tutto le licenze per hardware e prodotti e l'ottimizzazione delle prestazioni viene eseguita in un secondo momento. Quando si usa il database SQL di Azure, è consigliabile eseguire e ottimizzare al tempo stesso un'applicazione. Il modello di pagamento della capacità su richiesta consente di ottimizzare l'applicazione in modo da usare la quantità minima di risorse necessaria al momento specifico, invece di effettuare l'overprovisioning dell'hardware in base a ipotesi di piani di crescita futura per un'applicazione, che spesso si rivelano errati. Alcuni clienti potrebbero decidere di non ottimizzare un'applicazione e scegliere in alternativa di effettuare l'overprovisioning delle risorse hardware. Questo approccio potrebbe risultare valido se non si vuole modificare un'applicazione chiave in un periodo di attività elevata. L'ottimizzazione di un'applicazione può consentire tuttavia di ridurre i requisiti delle risorse e l'importo delle fatture mensili quando si usano i livelli di servizio nel database SQL di Azure.

### <a name="application-characteristics"></a>Caratteristiche dell'applicazione
Anche se i livelli di servizio del database SQL di Azure sono progettati per migliorare la stabilità e la prevedibilità delle prestazioni di un'applicazione, alcune procedure consigliate consentono di ottimizzare l'applicazione in modo da sfruttare al meglio le risorse in un livello di prestazioni. Anche se in molte applicazioni è possibile ottenere miglioramenti significativi delle prestazioni semplicemente passando a un livello di prestazioni o di servizio superiore, questo vantaggio non è assicurato per tutte le applicazioni senza un'ottimizzazione aggiuntiva. Per ottenere un aumento delle prestazioni, prendere in considerazione operazioni di ottimizzazione aggiuntive per le applicazioni con queste caratteristiche:

* **Applicazioni con prestazioni ridotte a causa di un comportamento "eccessivamente comunicativo"**. Le applicazioni con un livello di comunicazioni elevato eseguono un numero eccessivo di operazioni di accesso ai dati, sensibili alla latenza di rete. Potrebbe essere necessario modificare questi tipi di applicazioni, in modo da ridurre il numero di operazioni di accesso ai dati nel database SQL. È ad esempio possibile migliorare le prestazioni dell'applicazione usando tecniche come l'invio in batch di query ad hoc o lo spostamento delle query in stored procedure. Per altre informazioni, vedere [Invio di query in batch](#batch-queries).
* **Database con un carico di lavoro elevato che non possono essere supportati da una singola macchina virtuale intera**. I database che superano le risorse del livello di prestazioni Premium più elevato potrebbero trarre vantaggio dall'aumento del numero di istanze del carico di lavoro. Per altre informazioni, vedere [Partizionamento orizzontale tra database](#cross-database-sharding) e [Partizionamento funzionale](#functional-partitioning).
* **Applicazioni con query non ottimali**. Le applicazioni, in particolare quelle incluse nel livello di accesso ai dati, con query non ottimizzate correttamente potrebbero non ottenere vantaggi da un livello di prestazioni superiore. Tra queste sono incluse query prive della clausola WHERE, con indici mancanti o con statistiche obsolete. Queste applicazioni possono trarre vantaggio dalle tecniche standard di ottimizzazione delle prestazioni delle query. Per altre informazioni, vedere [Indici mancanti](#missing-indexes) e [Hint/Ottimizzazione di query](#query-tuning-and-hinting).
* **Applicazioni con struttura di accesso ai dati non ottimale**. Le applicazioni con problemi intrinseci di concorrenza per l'accesso ai dati, ad esempio il deadlock, potrebbero non trarre vantaggio da un livello di prestazioni superiore. Provare a ridurre i round trip nel database SQL di Azure memorizzando nella cache i dati sul lato client con il servizio Caching di Azure o un'altra tecnologia di memorizzazione nella cache. Vedere [Memorizzazione nella cache a livello di applicazione](#application-tier-caching).

## <a name="tuning-techniques"></a>Tecniche di ottimizzazione
Questa sezione illustra alcune tecniche che è possibile usare per ottimizzare il database SQL di Azure, in modo da ottenere le prestazioni migliori per l'applicazione ed eseguirla al livello di prestazioni più basso possibile. Alcune di queste tecniche corrispondono alle tradizionali procedure consigliate di ottimizzazione di SQL Server, ma altre sono specifiche del database SQL di Azure. In alcuni casi è possibile esaminare le risorse utilizzate per un database, in modo da individuare aree che richiedono ottimizzazione aggiuntiva e da estendere le tecniche tradizionali di SQL Server per l'uso nel database SQL di in Azure.

### <a name="azure-portal-tools"></a>Strumenti del portale di Azure
Nel portale di Azure sono disponibili i seguenti strumenti, utili per l'analisi e la risoluzione dei problemi di prestazioni del database SQL:

* [Query Performance Insight](sql-database-query-performance.md)
* [Advisor per database SQL](sql-database-advisor.md)

Il portale di Azure include altre informazioni su entrambi gli strumenti e sul relativo uso. Per diagnosticare e correggere in modo efficiente i problemi, è consigliabile provare prima di tutto a usare gli strumenti nel portale di Azure. È consigliabile usare gli approcci di ottimizzazione manuale illustrati di seguito, per gli indici mancanti e l'ottimizzazione delle query, solo in casi speciali.

### <a name="missing-indexes"></a>Indici mancanti
Un problema comune nelle prestazioni del database OLTP è correlato alla progettazione fisica del database. Spesso gli schemi di database vengono progettati e forniti senza verificare la scala (nel caricamento o nel volume di dati). Le prestazioni di un piano di query possono essere accettabili su scala ridotta, ma potrebbero purtroppo peggiorare notevolmente in caso di volumi elevati di dati a livello di produzione. L'origine più comune di questo problema è legata alla mancanza di indici adatti per soddisfare i filtri o altre restrizioni in una query. La mancanza di indici si manifesta spesso con una scansione di tabella quando potrebbe essere sufficiente una ricerca dell'indice.

In questo esempio il piano di query selezionato usa un'analisi quando invece sarebbe sufficiente una ricerca:

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![Piano di query con indici mancanti](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Il database SQL di Azure può consentire di trovare e risolvere condizioni comuni di indici mancanti. Le viste a gestione dinamica integrate nel database SQL di Azure esaminano le compilazioni di query in cui un indice ridurrebbe in modo significativo il costo stimato per l'esecuzione di una query. Durante l'esecuzione di query, il database SQL tiene traccia della frequenza con cui viene eseguito ogni piano di query e del divario stimato tra il piano di query eseguito e quello previsto in presenza dell'indice. È possibile usare queste viste a gestione dinamica per ipotizzare rapidamente quali modifiche alla progettazione fisica del database potrebbero migliorare il costo complessivo del carico di lavoro per un database e il rispettivo carico di lavoro reale.

È possibile usare questa query per valutare gli indici potenzialmente mancanti:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

In questo esempio la query ha restituito questo suggerimento:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Dopo la creazione, la stessa istruzione SELECT seleziona un piano diverso, che usa una ricerca invece di un'analisi, e quindi esegue il piano in modo più efficiente:

![Piano di query con indici corretti](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

L'aspetto chiave è che la capacità di I/O di un sistema apposito condiviso è più limitata di un server dedicato. Esiste un vantaggio nella riduzione dell'I/O non necessario, che consente di sfruttare al massimo il sistema nel valore di DTU di ogni livello di prestazioni incluso nei livelli di servizio per il database SQL di Azure. Le opzioni appropriate di progettazione fisica del database possono migliorare notevolmente la latenza delle singole query, la velocità effettiva di richieste simultanee gestibili per unità di scala e ridurre i costi necessari per soddisfare la query. Per altre informazioni sulle DMV di indici mancanti, vedere [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Hint/Ottimizzazione di query
Query Optimizer nel database SQL di Azure è simile a Query Optimizer tradizionale di SQL Server. La maggior parte delle procedure consigliate per ottimizzare le query e per comprendere i motivi delle limitazioni del modello per Query Optimizer si applica anche al database SQL di Azure. Se si ottimizzano query nel database SQL di Azure, si potrebbe ottenere il vantaggio aggiuntivo della riduzione delle richieste di risorsa aggregate. L'applicazione potrebbe essere eseguita a un costo inferiore rispetto a un'applicazione equivalente non ottimizzata, perché potrebbe usare un livello di prestazioni inferiore.

Un esempio comune in SQL Server e applicabile anche al database SQL di Azure è costituito dal modo in cui Query Optimizer "analizza" i parametri. Durante la compilazione, Query Optimizer valuta il valore corrente di un parametro per determinare se può generare un piano di query più idoneo. Anche se questa strategia può comportare spesso la creazione di un piano di query significativamente più veloce rispetto a un piano compilato senza valori di parametri noti, non funziona attualmente in modo perfetto in SQL Server e nel database SQL di Azure. In alcuni casi il parametro non viene analizzato e in altri casi viene analizzato ma il piano generato non è ottimale per l'intero set di valori di parametri in un carico di lavoro. Microsoft include gli hint per la query (direttive) per consentire di specificare la finalità più deliberatamente ed eseguire l'override del comportamento predefinito per l'analisi dei parametri. Se si usano gli hint, è spesso possibile risolvere i casi in cui il comportamento predefinito di SQL Server o del database SQL di Azure non è perfetto per il carico di lavoro di un cliente specifico.

L'esempio successivo illustra il modo in cui Query Processor può generare un piano non ottimale per i requisiti di prestazioni e risorse. L'esempio mostra anche che l'uso di un hint di query può consentire di ridurre il tempo di esecuzione delle query e i requisiti delle risorse per il database SQL:

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

Tramite il codice di installazione viene creata una tabella indicante la differenza nella distribuzione dei dati. Il piano di query ottimale varia in base al parametro selezionato. Il comportamento di memorizzazione nella cache del piano non esegue sempre la ricompilazione della query in base al valore di parametro più comune, purtroppo. È quindi possibile che un piano non ottimale venga memorizzato nella cache e usato per molti valori, anche se un piano diverso potrebbe costituire mediamente una scelta migliore. Il piano di query crea quindi due stored procedure identiche, ad eccezione del fatto che una include un hint di query speciale.

**Esempio (parte 1)**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Esempio (parte 2)**

È consigliabile attendere almeno 10 minuti prima di iniziare la parte 2 dell'esempio, in modo che i risultati siano distinti nei dati di telemetria risultanti.

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Ogni parte di questo esempio prova a eseguire 1.000 volte un'istruzione INSERT con parametri, per generare un carico sufficiente utilizzabile in un set di dati di test. Durante l'esecuzione di stored procedure, Query Processor esamina il valore del parametro passato alla procedura durante la prima compilazione ("analisi" dei parametri). Query Processor memorizza nella cache il piano risultante e lo usa per le chiamate successive, anche se il valore del parametro è diverso. È possibile che non venga usato il piano ottimale in tutti i casi. È a volte necessario consentire a Query Optimizer la selezione di un piano che sia migliore per la metà dei casi anziché per il caso specifico quando la query viene compilata per la prima volta. In questo esempio, il piano iniziale genera un piano di "analisi" che legge tutte le righe per cercare tutti i valori corrispondenti al parametro:

![Ottimizzazione delle query mediante un piano di analisi](./media/sql-database-performance-guidance/query_tuning_1.png)

Poiché la procedura è stata eseguita con il valore 1, il piano risultante è ottimale per il valore 1, ma non per tutti gli altri valori nella tabella. È probabile che il risultato non sia quello che si sceglierebbe se si potesse selezionare casualmente ogni piano, perché presenta prestazioni inferiori e usa una quantità maggiore di risorse.

Se si esegue il test con `SET STATISTICS IO` impostato su `ON`, le operazioni di analisi logica in questo esempio vengono eseguite in background. Come si può notare, 1.148 operazioni di lettura vengono eseguite dal piano e ciò è poco efficiente, se il caso medio consiste nel restituire solo una riga:

![Ottimizzazione delle query mediante un'analisi logica](./media/sql-database-performance-guidance/query_tuning_2.png)

La seconda parte dell'esempio usa un hint per la query per indicare a Query Optimizer di usare un valore specifico durante il processo di compilazione. In questo caso impone a Query Processor di ignorare il valore passato come parametro e di presupporre invece `UNKNOWN`. Ciò fa riferimento a un valore con frequenza media nella tabella, ignorando eventuali asimmetrie. Il piano risultante è un piano basato su ricerca, più veloce e con un minore impiego medio di risorse rispetto al piano della parte 1 dell'esempio:

![Ottimizzazione delle query mediante un hint di query](./media/sql-database-performance-guidance/query_tuning_3.png)

È possibile verificarne l'effetto nella tabella **sys.resource_stats**. Si verifica un ritardo dal momento in cui il test viene eseguito a quando i dati popolano la tabella. Per questo esempio, la parte 1 è stata eseguita durante l'intervallo di tempo 22:25:00 e la parte 2 nell'intervallo di tempo 22:35:00. Nell'intervallo di tempo precedente sono state usate più risorse rispetto a quello successivo, in seguito ai miglioramenti di efficienza di piano.

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Risultati di esempio di ottimizzazione delle query](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Anche se il volume in questo esempio è intenzionalmente ridotto, l'effetto dei parametri non ottimali può essere significativo, in particolare nei database di dimensioni più grandi. La differenza, nei casi estremi, può essere di alcuni secondi per i casi veloci e di alcune ore per i casi lenti.
>
>

Esaminando **sys.resource_stats** è possibile determinare se la risorsa usata per un test usa una quantità di risorse superiore o inferiore rispetto a un altro test. Quando si confrontano i dati, separare gli intervalli di test in modo che non rientrino nella stessa finestra di 5 minuti nella vista **sys.resource_stats**. L'obiettivo dell'esercizio consiste nel ridurre la quantità totale di risorse usate e non di ridurre le risorse di picco. In genere, anche con l'ottimizzazione di una parte di codice per la latenza viene ridotto il consumo di risorse. Assicurarsi che le modifiche apportate a un'applicazione siano necessarie e che non influiscano negativamente sull'esperienza dei clienti che potrebbero usare hint di query nell'applicazione.

Se un carico di lavoro include un set di query ripetute, è spesso consigliabile acquisire e confermare la validità delle scelte del piano perché determinerà l'unità di dimensioni minima delle risorse per ospitare il database. Dopo la convalida, esaminare di nuovo occasionalmente i piani per assicurarsi che siano ancora ottimali. Per altre informazioni, vedere [Hint per la query (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Partizionamento orizzontale tra database
Poiché il database SQL di Azure viene eseguito in hardware apposito, i limiti della capacità per un database singolo sono inferiori a quelli per un'installazione locale tradizionale di SQL Server. Alcuni clienti usano tecniche di partizionamento orizzontale per estendere le operazioni in più database quando non rientrano nei limiti relativi a un database singolo nel database SQL di Azure. La maggior parte dei clienti che usa le tecniche di partizionamento orizzontale nel database SQL di Azure suddivide i dati di una singola dimensione in più database. Per questo approccio è necessario comprendere che le applicazioni OLTP eseguono spesso transazioni applicabili a una riga o a un piccolo gruppo di righe nello schema.

> [!NOTE]
> Il database SQL offre ora una libreria per semplificare il partizionamento orizzontale. Per altre informazioni, vedere [Panoramica della libreria client dei database elastici](sql-database-elastic-database-client-library.md).
>
>

Se, ad esempio, un database include il nome del cliente, l'ordine e i dettagli dell'ordine (come illustrato nel database di esempio tradizionale Northwind incluso in SQL Server), è possibile suddividere questi dati in più database raggruppando un cliente con l'ordine correlato e con le informazioni dettagliate sull'ordine. È possibile assicurare che i dati del cliente rimangano in un singolo database. L'applicazione suddividerebbe i diversi clienti tra database, estendendo di fatto il carico tra più database. Con il partizionamento orizzontale, i clienti possono evitare di raggiungere il limite massimo delle dimensioni del database, ma anche il database SQL di Azure può elaborare carichi di lavoro notevolmente maggiori rispetto ai limiti corrispondenti ai diversi livelli di prestazioni, a condizione che ogni singolo database rientri nel relativo valore di DTU.

Anche se il partizionamento orizzontale del database non riduce la capacità aggregata delle risorse per una soluzione, è notevolmente efficace nel supportare soluzioni di dimensioni molto elevate distribuite in più database. Ogni database può essere eseguito a livelli di prestazioni diversi per supportare database "efficaci" di dimensioni molto grandi con requisiti molto elevati a livello di risorse.

### <a name="functional-partitioning"></a>Partizionamento funzionale
Gli utenti di SQL Server combinano spesso molte funzioni all'interno di un singolo database. Se un'applicazione include ad esempio la logica per gestire l'inventario di un negozio, è possibile che quel database includa la logica associata all'inventario, il rilevamento degli ordini di acquisto, le stored procedure e le viste indicizzate o materializzate mediante le quali sono stati gestiti i report di fine mese. Questa tecnica semplifica l'amministrazione del database per operazioni quali il backup, ma richiede anche il ridimensionamento dell'hardware per gestire il carico massimo in tutte le funzioni di un'applicazione.

Se si usa un'architettura con aumento del numero di istanze nel database SQL di Azure, è consigliabile suddividere le diverse funzioni di un'applicazione in diversi database. Se si usa questa tecnica, ogni applicazione viene ridimensionata in modo indipendente. Quando un'applicazione viene usata con maggiore frequenza e il carico nel relativo database aumenta, l'amministratore può scegliere un livello di prestazioni indipendente per ogni funzione in un'applicazione. Nei limiti, questa architettura fa sì che le dimensioni di un'applicazione diventino più grandi di quelle gestibili da una singola macchina apposita, perché il carico viene distribuito in più macchine.

### <a name="batch-queries"></a>Invio di query in batch
Per le applicazioni che accedono ai dati con un uso frequente ed elevato di query ad hoc, gran parte del tempo di risposta viene speso nelle comunicazioni di rete tra il livello applicazione e il livello di database SQL di Azure. Anche quando l'applicazione e il database SQL di Azure risiedono nello stesso data center, la latenza di rete tra questi due elementi potrebbe essere aumentata per un numero elevato di operazioni di accesso ai dati. Per ridurre i round trip di rete per le operazioni di accesso ai dati, prendere in considerazione l'uso dell'opzione per l'invio in batch delle query ad hoc o per la compilazione delle query come stored procedure. L'invio in batch delle query ad hoc consente di inviare più query come un unico grande batch in una singola operazione al database SQL di Azure. La compilazione di query ad hoc in stored procedure può produrre lo stesso risultato dell'invio in batch. L'uso di una stored procedure offre inoltre il vantaggio di aumentare le opportunità di memorizzare nella cache i piani di query nel database SQL di Azure per esecuzioni successive della stessa stored procedure.

Alcune applicazioni comportano un utilizzo elevato di scrittura. È a volte possibile ridurre il carico totale di I/O in un database considerando la modalità di invio in batch delle scritture. Questa operazione è spesso semplice come l'uso di transazioni esplicite anziché di transazioni autocommit in stored procedure e batch ad hoc. Per una valutazione delle differenti tecniche che si possono usare, vedere [Tecniche di esecuzione in batch per applicazioni del database SQL in Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Provare a usare il proprio carico di lavoro per individuare il modello ottimale per l'invio in batch. Assicurarsi di comprendere che un modello potrebbe offrire garanzie di coerenza transazionale leggermente diverse. Per trovare il carico di lavoro ottimale che consenta un uso delle risorse minimo è necessario individuare la corretta combinazione di compromessi tra prestazioni e coerenza.

### <a name="application-tier-caching"></a>Memorizzazione nella cache a livello di applicazione
Alcune applicazioni di database contengono carichi di lavoro con intensa attività di lettura. I livelli di memorizzazione nella cache possono consentire di ridurre il carico nel database e di ridurre il livello di prestazioni necessario per supportare un database usando il database SQL di Azure. La [Cache Redis di Azure](https://azure.microsoft.com/services/cache/), in caso di carico di lavoro con intensa attività di lettura, può consentire di leggere i dati una volta o forse una volta per macchina di livello applicazione, a seconda della relativa configurazione, e di archiviare i dati al di fuori del database SQL. In questo modo è possibile ridurre il carico del database (CPU e I/O letti), ma vi sarà un impatto sulla coerenza transazionale poiché i dati letti dalla cache potrebbero non essere sincronizzati rispetto ai dati nel database. Anche se in molte applicazioni è accettabile un livello di incoerenza, ciò non rappresenta una soluzione valida per tutti i carichi di lavoro. È necessario conoscere bene tutti i requisiti delle applicazioni prima di implementare una strategia di caching a livello di applicazione.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sui livelli di servizio, vedere [Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL](sql-database-service-tiers.md)
* Per altre informazioni sui pool elastici, vedere [Informazioni sui pool elastici di Azure](sql-database-elastic-pool.md)
* Per informazioni sulle prestazioni e sui pool elastici, vedere [Quando usare un pool elastico](sql-database-elastic-pool-guidance.md)

