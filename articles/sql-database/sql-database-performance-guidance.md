<properties
	pageTitle="Indicazioni sulle prestazioni del database SQL di Azure per i singoli database"
	description="Questo argomento fornisce indicazioni per stabilire quale livello di servizio rappresenta la soluzione adatta all'applicazione in uso e fornisce consigli per ottimizzare l'applicazione in modo da ottenere il massimo dal database SQL di Azure."
	services="sql-database"
	documentationCenter="na"
	authors="carlrabeler"
	manager="jhubbard"
	editor="" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="04/11/2016"
	ms.author="carlrab" />

# Indicazioni sulle prestazioni del database SQL di Azure per i singoli database

## Panoramica 

Il database SQL di Microsoft Azure offre tre [livelli di servizio](sql-database-service-tiers.md), ovvero Basic, Standard e Premium. I tre livelli isolano rigorosamente la risorsa fornita al database SQL di Azure e garantiscono prestazioni prevedibili. La velocità effettiva garantita per il database aumenta dal livello Basic a quello Standard fino al livello Premium.

>[AZURE.NOTE] I livelli di servizio Business e Web verranno ritirati a settembre 2015. Per altre informazioni, vedere [Domande frequenti sull'interruzione delle edizioni Web e Business](https://msdn.microsoft.com/library/azure/dn741330.aspx). Per informazioni dettagliate sull'aggiornamento di database Web e Business esistenti ai nuovi livelli di servizio, vedere [Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio](sql-database-upgrade-server-portal.md).

Questo documento fornisce indicazioni per stabilire quale livello di servizio rappresenta la soluzione adatta all'applicazione in uso e fornisce consigli per ottimizzare l'applicazione in modo da ottenere il massimo dal database SQL di Azure.

>[AZURE.NOTE] Questo articolo è incentrato sulle indicazioni relative alle prestazioni per singoli database nel database SQL. Per indicazioni sulle prestazioni relative ai pool di database elastici, vedere [Considerazioni di prezzo e prestazioni per un pool di database flessibile](sql-database-elastic-pool-guidance.md). Si noti tuttavia che molti suggerimenti sull'ottimizzazione disponibili in questo articolo per un singolo database possono essere applicati ai database in un pool elastico con vantaggi analoghi per le prestazioni.

**Autori:** Conor Cunningham, Kun Cheng, Jan Engelsberg

**Revisori tecnici:** Morgan Oslake, Joanne Marone, Keith Elmore, José Batista-Neto, Rohit Nayak

## Informazioni di base sul database SQL di Azure

Per comprendere in che modo i livelli di servizio Basic, Standard e Premium migliorano il servizio del database SQL di Azure, è opportuno avere una buona conoscenza di base del database SQL di Azure. Ci sono molti motivi per scegliere il database SQL di Azure. Uno di questi è la possibilità di evitare il lungo ciclo di acquisto e di installazione dell'hardware. Il database SQL di Azure consente di creare ed eliminare i database immediatamente, senza dover aspettare l'approvazione di un ordine di acquisto, l'arrivo dei computer, il miglioramento del sistema di alimentazione e raffreddamento o l'esecuzione dell'installazione. Microsoft gestisce questi problemi e riduce in maniera significativa il tempo necessario per passare dall'idea alla soluzione effettuando il pre-provisioning dell'hardware in base alla domanda di aggregazione in ognuno dei propri data center. In questo modo l'azienda può risparmiare settimane o mesi rispetto all'acquisto e alla distribuzione manuali dell'hardware.

Microsoft include anche numerose funzionalità automatiche di gestione nel database SQL di Azure, ad esempio la disponibilità elevata automatica e la gestione incorporata.

### Disponibilità elevata automatica (HA) 
 Il database SQL di Azure mantiene almeno tre repliche per ogni database utente e include anche una logica per eseguire automaticamente il commit di ogni modifica in modo sincrono a un quorum di repliche. Ciò garantisce che qualsiasi errore di una singola macchina non causerà la perdita di dati. Inoltre, ogni replica viene posizionata in rack hardware differenti in modo che eventuali perdite di alimentazione o commutazioni di rete non influiscano sul database. Infine, è disponibile una logica per ricompilare automaticamente le repliche in caso di perdita di una macchina in modo che tramite il sistema vengano mantenute automaticamente le proprietà di integrità desiderate anche se la macchina è compromessa. Grazie a questi meccanismi viene evitato il lungo processo richiesto oggi per installare e configurare le attuali soluzioni a disponibilità elevata. Con una soluzione a disponibilità elevata preconfigurata per i dati viene risolto un altro annoso problema relativo alla compilazione di una soluzione di database di importanza critica usando le tecniche tradizionali.

### Gestione incorporata 
 Il database SQL di Azure viene gestito come servizio. Di conseguenza, vi sono obiettivi di tempo definiti per ogni database, che evitano lunghi tempi di inattività di manutenzione. Microsoft offre una soluzione con fornitore unico per il servizio, quindi sarà necessario contattare un'unica società in caso di eventuali problemi. Inoltre, aggiorna continuamente il servizio, aggiungendo in ogni aggiornamento funzionalità, capacità e modalità di ricerca per migliorare l'esperienza utente. Gli aggiornamenti vengono eseguiti in modo trasparente e senza tempi di inattività, di conseguenza vengono integrati nel normale meccanismo di failover di disponibilità elevata. Questo consente di usare immediatamente le nuove funzionalità ogni volta che Microsoft ne annuncia la disponibilità, anziché attendere l'aggiornamento di un server durante eventuali tempi di inattività.

Tutte queste funzionalità vengono fornite in tutti i livelli di servizio, a partire da una fascia di prezzo di base bassa, pari a pochi euro al mese. Si tratta di un prezzo molto inferiore rispetto al costo di acquisto ed esecuzione del proprio server, pertanto anche per il progetto più piccolo è possibile avvalersi di Azure senza spendere molto.

## Differenze dei livelli di servizio
Sono disponibili tre livelli di servizio: Basic, Standard e Premium. Per ogni livello di servizio sono disponibili uno o più livelli di prestazioni che forniscono la potenza di elaborazione necessaria all'esecuzione dei database in modo prevedibile. La potenza di elaborazione viene espressa in [DTU (Database Transaction Units, unità di transazione del database)](sql-database-technical-overview.md#understand-dtus).

Il livello di servizio Basic è stato sviluppato per offrire una prevedibilità di prestazioni soddisfacente per ogni database calcolata a intervalli di un'ora ciascuno. Il valore di DTU di un database Basic consente di fornire risorse sufficienti al raggiungimento di prestazioni accettabili per i database di piccole dimensioni senza richieste simultanee.

Il livello di servizio Standard offre una migliore prevedibilità delle prestazioni e aumenta gli standard relativi ai database con più richieste simultanee, quali gruppi di lavoro e applicazioni Web. L'uso di un database con livello di servizio Standard consente di ridimensionare le applicazioni di database in base alle prestazioni prevedibili minuto per minuto.

La caratteristica principale del livello di servizio Premium in termini di prestazioni è rappresentata dalla prevedibilità delle prestazioni calcolata secondo per secondo per ciascun database Premium. L'uso del livello di servizio Premium consente di ridimensionare le applicazioni di database in base al carico massimo del database in questione e consente di eliminare i casi in cui la varianza di prestazioni può causare piccole query che possono richiedere più tempo del previsto in operazioni sensibili alla latenza. Questo modello può semplificare notevolmente i cicli di sviluppo e di convalida del prodotto necessari per applicazioni per cui occorre fare dichiarazioni forti sulle esigenze di risorse relative ai picchi, sulla varianza di prestazioni o sulla latenza di query.

Come per il livello Standard, con il livello di servizio Premium è possibile scegliere tra diversi livelli di prestazioni in base all'isolamento desiderato di un cliente.

Grazie alle impostazioni dei livelli di prestazioni nei livelli di servizio Standard e Premium, il cliente paga solo la capacità necessaria alle proprie esigenze e ha la facoltà di connettere o disconnettere tale capacità al variare del carico di lavoro. Ad esempio, se il carico di lavoro del database è intenso durante il periodo di acquisti per il ritorno a scuola, è possibile aumentare il livello di prestazioni per il database durante questo periodo di tempo e ridurlo in un secondo momento, al termine della fase di picco. In questo modo si possono diminuire i costi ottimizzando l'ambiente cloud in base alla stagionalità della propria attività. Questo modello è adatto anche per i cicli di rilascio di prodotti software. Un team di test può allocare la capacità durante l'esecuzione di test e rilasciare tale capacità una volta completata l'operazione. Queste richieste di capacità sono adatte per il modello per il quale si paga la capacità necessaria evitando così il costo di risorse dedicate usate di rado. Ciò offre un'esperienza di prestazioni molto più vicina al modello hardware dedicato tradizionale che molti clienti Microsoft hanno usato con SQL Server. Inoltre, dovrebbe consentire un'esecuzione più semplice di un set più ampio di applicazioni nel database SQL di Azure.

Per altre informazioni su livelli di prestazioni e DTU, vedere [Livelli di servizio e delle prestazioni del database SQL di Azure](sql-database-service-tiers.md).

## Vantaggi dei livelli di servizio

Anche se ogni carico di lavoro può presentare caratteristiche diverse, lo scopo dei livelli di servizio è quello di fornire un elevato livello di prevedibilità delle prestazioni impiegando diversi livelli di prestazioni. Questi livelli consentono ai clienti con una vasta gamma di requisiti di risorse relative ai propri database di operare in un ambiente di calcolo più dedicato.

### Casi di utilizzo del livello di servizio Basic:

- **Introduzione al database SQL di Azure**: per le applicazioni in fase di sviluppo non sono in genere necessari livelli di prestazioni elevati. I database Basic offrono un ambiente ideale per lo sviluppo di database a una fascia di prezzo conveniente.
- **Database con un singolo utente**: di norma, le applicazioni in cui un singolo utente è associato a un database non sono caratterizzate da concorrenza e prestazioni con requisiti elevati. Le applicazioni con requisiti di questo tipo rappresentano candidati ottimali per il livello di servizio Basic.

### Casi di utilizzo del livello di servizio Standard:

- **Database con più richieste simultanee**: le applicazioni usate da più utenti contemporaneamente, ad esempio siti Web con traffico moderato o applicazioni a livello di reparto aziendale che richiedono una quantità di risorse maggiore, rappresentano candidati ottimali per il livello di servizio Standard.

### Casi di utilizzo del livello di servizio Premium:

- **Carico massimo**: un'applicazione per la quale il completamento delle operazioni richiede un'elevata quantità di risorse di I/O, memoria o utilizzo della CPU. Ad esempio, l'utilizzo di database Premium è ideale nel caso in cui un'operazione di database usi più core CPU per un lungo periodo di tempo.
- **Molte richieste simultanee**: in alcune applicazioni di database vengono usate molte richieste simultanee, ad esempio durante l'utilizzo di un sito Web con un elevato volume di traffico. Per i livelli di servizio Basic e Standard sono previsti limiti al numero di richieste simultanee. Per le applicazioni per cui sono richieste più connessioni devono essere selezionate dimensioni di prenotazione appropriate per gestire il numero massimo di richieste necessarie.
- **Bassa latenza**: alcune applicazioni devono garantire una risposta dal database in tempi minimi. Se una stored procedure specificata viene chiamata durante un'operazione più ampia del cliente, potrebbe essere richiesto che la risposta alla chiamata in questione venga assicurata in non più di 20 millisecondi nel 99% dei casi. Questo tipo di applicazione trarrà vantaggio dai database Premium per assicurare la disponibilità della potenza di calcolo.

Il livello esatto necessario dipende dai requisiti del carico massimo per ciascuna dimensione di risorsa. È possibile che in alcune applicazioni vengano usate quantità irrilevanti di una risorsa alle quali, però, sono associati requisiti significativi in un'altra.

Per altre informazioni sui livelli di servizio, vedere [Livelli di servizio e delle prestazioni del database SQL di Azure](sql-database-service-tiers.md).

## Limiti e funzionalità dei livelli di servizio
Ogni livello di servizio e prestazione è associato a limiti e caratteristiche di prestazione differenti. La tabella seguente descrive queste caratteristiche per un singolo database.

[AZURE.INCLUDE [Tabella livelli di servizio database SQL](../../includes/sql-database-service-tiers-table.md)]

Le sezioni seguenti forniscono altre informazioni sulle aree della tabella precedente.

### Dimensioni massime del database

**Dimensioni massime del database** è semplicemente il limite delle dimensioni del database espresso in GB.

### DTU

**DTU** fa riferimento alle unità di transazione del database. Si tratta dell'unità di misura nel database SQL che rappresenta la potenza relativa dei database basata su una misura del mondo reale: la transazione del database. Consiste in una serie di operazioni tipiche per una richiesta di elaborazione di una transazione in linea (OLTP), misurate in base al numero di transazioni completate al secondo in condizioni di carico totale. Per altre informazioni sulle DTU, vedere [Comprendere le DTU](sql-database-technical-overview.md#understand-dtus). Per altre informazioni su come vengono misurate le DTU, vedere [Informazioni generali sul benchmark](sql-database-benchmark-overview.md).

### Ripristino temporizzato

Il **ripristino temporizzato** consente di ripristinare il database a un momento precedente. Il livello di servizio determina il numero di giorni di conservazione. Per altre informazioni, vedere [Ripristinare un database SQL di Azure a seguito di un errore causato dall'utente](sql-database-user-error-recovery.md).

### Ripristino di emergenza

Il **ripristino di emergenza** consente il ripristino da un'interruzione nel database SQL primario.

Il *ripristino geografico* è disponibile per tutti i livelli di servizio senza costi aggiuntivi. In caso di interruzione, è possibile usare il backup con ridondanza geografica più recente per ripristinare il database in qualsiasi area di Azure.

La replica geografica attiva e standard fornisce funzionalità di ripristino di emergenza analoghe, ma con un obiettivo del punto di ripristino più limitato. Ad esempio, con il ripristino geografico, l'obiettivo del punto di ripristino è inferiore a 1 ora (in altre parole, il backup può fare riferimento all'ora precedente). Con la replica geografica, invece, l'obiettivo del punto di ripristino è inferiore a 5 secondi.

Per altre informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

### Archiviazione di OLTP in memoria massima
L'**archiviazione OLTP in memoria massima** fa riferimento alla quantità massima di spazio di archiviazione disponibile per l'[anteprima OLTP in memoria](sql-database-in-memory.md) per i database Premium. Ciò è talvolta definita *archiviazione XTP in memoria*. Per monitorare l'uso dell'archiviazione in memoria, è possibile usare il portale di Azure classico o la vista **sys.dm\_db\_resource\_stats**. Per altre informazioni sul monitoraggio, vedere l'argomento relativo al [Monitoraggio dell'archiviazione OLTP in memoria XTP](sql-database-in-memory-oltp-monitoring.md).

>[AZURE.NOTE] L'anteprima OLTP in memoria è attualmente supportata solo per i database singoli e non per i database nei pool di database elastici.

### Numero massimo di richieste simultanee

**Numero massimo di richieste simultanee** è il numero massimo di richieste simultanee eseguite da un utente o da un'applicazione contemporaneamente nel database. Per visualizzare il numero di richieste simultanee, eseguire la query Transact-SQL seguente sul database SQL:

	SELECT COUNT(*) AS [Concurrent_Requests] 
	FROM sys.dm_exec_requests R

Se si analizza il carico di lavoro di un database SQL Server locale, è necessario modificare la query per filtrare il database specifico che si sta analizzando. Ad esempio, se si dispone di un database locale denominato MyDatabase, la query Transact-SQL seguente restituirà il numero di richieste simultanee in tale database.

	SELECT COUNT(*) AS [Concurrent_Requests] 
	FROM sys.dm_exec_requests R
	INNER JOIN sys.databases D ON D.database_id = R.database_id
	AND D.name = 'MyDatabase'

Tenere presente che questo è solo uno snapshot in un singolo punto nel tempo. Per una migliore comprensione del carico di lavoro, è necessario raccogliere molti esempi nel tempo per comprendere i requisiti delle richieste simultanee.

### Numero massimo di accessi simultanei

Il **numero massimo di accessi simultanei** rappresenta il limite del numero di utenti o applicazioni che tentano di connettersi al database contemporaneamente. Si noti che anche se questi client usano la stessa stringa di connessione, il servizio autentica ogni account di accesso. Pertanto se dieci utenti sono connessi contemporaneamente al database con nome utente e password identici, ci saranno dieci account di accesso simultanei. Questo limite si applica solo alla durata dell'account di accesso e dell'autenticazione. Pertanto, se gli stessi dieci utenti sono connessi in sequenza al database, il numero di account di accesso simultanei non sarà mai superiore a uno.

>[AZURE.NOTE] Questo limite attualmente non si applica ai database nel pool di database elastici.

Non esiste alcuna query o DMV che consente di visualizzare la cronologia o il numero degli accessi simultanei. Per avere un'idea della frequenza degli accessi, è possibile analizzare i modelli dell'utente e dell'applicazione. È inoltre possibile eseguire carichi reali in un ambiente di test per assicurarsi di non raggiungere questo o gli altri limiti descritti in questo argomento.

### Numero massimo di sessioni

**Numero massimo di sessioni** è il numero massimo di connessioni aperte simultanee al database. Quando un utente accede, viene stabilita una sessione che rimane attiva finché l'utente si disconnette o la sessione scade. Per visualizzare il numero di sessioni attive, eseguire la query Transact-SQL seguente sul database SQL:

	SELECT COUNT(*) AS [Sessions]
	FROM sys.dm_exec_connections

Se si analizza un carico di lavoro di SQL Server locale, modificare la query per concentrarsi su un database specifico. Ciò consentirà di determinare le possibili esigenze della sessione per tale database se è necessario spostarlo nel database SQL di Azure.

	SELECT COUNT(*)  AS [Sessions]
	FROM sys.dm_exec_connections C
	INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
	INNER JOIN sys.databases D ON (D.database_id = S.database_id)
	WHERE D.name = 'MyDatabase'

Queste query restituiscono un numero riferito a un determinato momento, pertanto la raccolta di più esempi in un periodo di tempo più ampio offre informazioni più precise sull'utilizzo della sessione.

Per l'analisi del database SQL, è possibile anche eseguire una query **sys.resource\_stats** per ottenere dati statistici cronologici sulle sessioni mediante la colonna **active\_session\_count**. Nella seguente sezione sul monitoraggio sono disponibili altre informazioni sull'uso di questa vista.

## Monitoraggio dell'uso delle risorse
Sono disponibili due viste che consentono di monitorare l'utilizzo delle risorse per un database SQL rispetto al relativo livello di servizio:

- [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx)

>[AZURE.NOTE] È inoltre possibile usare il portale di Azure classico per visualizzare l'utilizzo delle risorse. Per un esempio, vedere [Livelli di servizio - Monitoraggio delle prestazioni](sql-database-service-tiers.md#monitoring-performance).

### Uso di sys.dm\_db\_resource\_stats
La vista [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) è presente in ogni database SQL e fornisce dati di utilizzo delle risorse recenti rispetto al livello di servizio. Informazioni relative a percentuali medie della CPU, dati I/O, scritture nei log e memoria vengono registrate ogni 15 secondi e vengono mantenute per un'ora.

Poiché questa vista fornisce una visione più granulare sull'utilizzo delle risorse, è consigliabile usare innanzitutto **sys.dm\_db\_resource\_stats** per eventuali analisi o risoluzioni di problemi allo stato corrente. Ad esempio, la query seguente mostra l'utilizzo medio e massimo delle risorse per il database corrente nell'ultima ora:

	SELECT  
	    AVG(avg_cpu_percent) AS 'Average CPU Utilization In Percent', 
	    MAX(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent', 
	    AVG(avg_data_io_percent) AS 'Average Data IO In Percent', 
	    MAX(avg_data_io_percent) AS 'Maximum Data IO In Percent', 
	    AVG(avg_log_write_percent) AS 'Average Log Write Utilization In Percent', 
	    MAX(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent', 
	    AVG(avg_memory_usage_percent) AS 'Average Memory Usage In Percent', 
	    MAX(avg_memory_usage_percent) AS 'Maximum Memory Usage In Percent' 
	FROM sys.dm_db_resource_stats;  

Per altre query, vedere gli esempi in [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### Uso di sys.resource\_stats

La vista [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) nel database **master** fornisce informazioni aggiuntive per il monitoraggio dell'uso delle prestazioni del database SQL nell'ambito del relativo livello di servizio e di prestazioni. I dati vengono raccolti ogni cinque minuti e conservati per circa 14 giorni. Questa vista è più utile per analisi cronologiche a lungo termine dell'utilizzo delle risorse del database SQL.

Il grafico seguente illustra l'utilizzo di risorse della CPU per un database Premium con livello di prestazioni P2 per ogni ora nell'arco di una settimana. Questo grafico specifico inizia di lunedì, con 5 giorni lavorativi e un fine settimana in cui l'utilizzo di risorse nell'applicazione è molto inferiore.

![Utilizzo delle risorse del database SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

In base ai dati, per il livello di prestazioni P2 il carico massimo della CPU di questo database attualmente supera di poco il 50% dell'utilizzo della CPU (a mezzogiorno di martedì). Se la CPU è il fattore più importante nel profilo delle risorse dell'applicazione, si può scegliere il P2 come livello di prestazioni idoneo a garantire che il carico di lavoro sia sempre adeguato. Se si prevede un aumento delle dimensioni dell'applicazione nel tempo, è consigliabile permettere un buffer di risorse aggiuntive in modo che non venga mai raggiunta la capacità massima da parte dell'applicazione. Ciò consente di evitare errori visibili da parte del cliente causati dall'incapacità del database di elaborare le richieste in modo efficiente, in particolare in ambienti sensibili alla latenza, ad esempio nel caso di un database che supporta un'applicazione mediante la quale vengono disegnate pagine Web in base ai risultati delle chiamate di database.

È interessante notare che lo stesso grafico può essere interpretato in modo diverso da altri tipi di applicazioni. Ad esempio, se tramite un'applicazione si è tentato di elaborare i dati del libro paga ogni giorno ed è stato usato lo stesso grafico, questo tipo di modello di processo batch potrebbe essere eseguito correttamente con un livello di prestazioni P1. Il valore di DTU del livello di prestazioni P1 è pari a 100, mentre quello del livello P2 è pari a 200. Ciò significa che il livello di prestazioni fornito dal livello di servizio P2 è doppio rispetto a quello fornito dal livello P1. Il 50% dell'utilizzo della CPU nel livello P2 corrisponde dunque al 100% dell'utilizzo della CPU nel livello P1. Finché l'applicazione non presenta dei timeout, è possibile che non sia importante che il completamento di un processo di grandi dimensioni richieda 2 o 2,5 ore, a condizione che venga però eseguito in giornata. Per un'applicazione che rientra in questa categoria probabilmente è sufficiente usare il livello di prestazioni P1. Si può sfruttare la presenza di periodi di tempo durante il giorno in cui l'utilizzo delle risorse è inferiore, in altre parole si può spalmare un picco massimo in altri momenti nel corso della giornata. Il livello di prestazioni P1 potrebbe essere ideale per un'applicazione di questo tipo (consentendo inoltre di limitare i costi), a condizione che i processi possano essere completati in orario ogni giorno.

Il database SQL di Azure espone le informazioni sulla risorsa utilizzata per ogni database attivo nella vista **sys.resource\_stats** del database **master** in ogni server. I dati nella tabella vengono aggregati per intervalli di 5 minuti. Con i livelli di servizio Basic, Standard e Premium, la visualizzazione dei dati nella tabella può richiedere più di cinque minuti. Di conseguenza questi dati sono più adatti a un'analisi cronologica che a un'analisi quasi in tempo reale. Se si esegue una query sulla vista **sys.resource\_stats**, viene visualizzata la cronologia recente di un database, in cui è possibile convalidare se tramite la prenotazione selezionata sono state fornite le risorse desiderate, quando necessario.

>[AZURE.NOTE] È necessario essere connessi al database **master** del server logico del database SQL per eseguire la query **sys.resource\_stats** negli esempi seguenti.

L'esempio seguente illustra l'esposizione dei dati in questa vista:

	SELECT TOP 10 * 
	FROM sys.resource_stats 
	WHERE database_name = 'resource1' 
	ORDER BY start_time DESC

![Statistiche relative alle risorse di sistema](./media/sql-database-performance-guidance/sys_resource_stats.png)

L'esempio seguente illustra i vari modi mediante i quali è possibile comprendere l'utilizzo delle risorse del database SQL usando la vista del catalogo **sys.resource\_stats**.

>[AZURE.NOTE] Alcune delle colonne di **sys.resource\_stats** sono state modificate nella versione 12 dei database correnti, pertanto è possibile che le query di esempio negli esempi seguenti generino errori. Gli aggiornamenti futuri di questo argomento forniranno le nuove versioni delle query per risolvere questo problema.

1. Ad esempio, per esaminare l'utilizzo delle risorse della settimana passata per il database, "userdb1", è possibile eseguire la query seguente.
	
		SELECT * 
		FROM sys.resource_stats 
		WHERE database_name = 'userdb1' AND 
		      start_time > DATEADD(day, -7, GETDATE())
		ORDER BY start_time DESC;
	
2. Per valutare l'idoneità del livello di prestazioni in rapporto al carico di lavoro, è necessario eseguire il drill-down in corrispondenza di ogni aspetto delle metriche delle risorse: CPU, operazioni di lettura e scrittura, numero dei thread di lavoro e numero di sessioni. Di seguito è riportata la query modificata usando sys.resource\_stats per segnalare i valori medi e massimi di queste metriche delle risorse.
	
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
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
3. Con le informazioni sopra riportate sui valori medi e massimi di ogni metrica delle risorse è possibile valutare l'idoneità del livello di prestazioni scelto in rapporto al carico di lavoro. Nella maggior parte dei casi, i valori medi di sys.resource\_stats garantiscono una buona base di riferimento da usare nelle dimensioni di destinazione. Deve trattarsi dello strumento di misurazione principale. Ad esempio, se si usa il livello di servizio Standard con il livello di prestazioni S2, le percentuali di utilizzo medio della CPU e delle operazioni di lettura e scrittura non superano il 40%, il numero medio di thread di lavoro non supera 50 e il numero medio di sessioni è inferiore a 200, il carico di lavoro in esame può rientrare nel livello di prestazioni S1. È facile verificare se il database rientra nei limiti dei thread di lavoro e delle sessioni. Per verificare se un database può rientrare in un livello di prestazioni inferiore prendendo in considerazione la CPU e le operazioni di lettura e scrittura, è sufficiente dividere il numero di DTU del livello di prestazioni inferiore per il numero di DTU del livello di prestazioni corrente e moltiplicare il risultato per 100:
	
	**S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
	
	Il risultato rappresenta la differenza di prestazioni relativa, in percentuale, tra i due livelli di prestazioni. Se l'utilizzo di risorse corrente non supera questa percentuale, il carico di lavoro può rientrare nel livello di prestazioni inferiore a quello attuale. È tuttavia necessario esaminare anche tutti gli intervalli dei valori di utilizzo delle risorse e determinare, a livello di percentuale, la frequenza con cui il carico di lavoro del database può rientrare nel livello di prestazioni inferiore. La query seguente genera questa percentuale per ogni dimensione di risorsa, in base alla soglia del 40% calcolata in precedenza.
	
		SELECT 
		    (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_physical_data_read_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data Read Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
	In base all'obiettivo del livello di servizio (SLO) del database, è possibile stabilire se il carico di lavoro può rientrare nel livello di prestazioni inferiore. Se l'obiettivo del livello di servizio del carico di lavoro del database è 99,9% e dalla query precedente vengono restituiti valori maggiori di 99,9 per tutte e tre le dimensioni della risorsa, con ogni probabilità il carico di lavoro può rientrare nel livello di prestazioni inferiore.
	
	La percentuale calcolata in precedenza consente inoltre di stabilire se è necessario usare il livello di prestazioni superiore a quello attualmente in uso per soddisfare l'obiettivo del livello di servizio. Ad esempio, l'utilizzo di "userdb1" della settimana precedente è il seguente.
	
	| Percentuale CPU media | Percentuale CPU massima |
	|---|---|
	| 24,5 | 100,00 |
	
	L'utilizzo medio della CPU corrisponde a circa un quarto del limite del livello di prestazioni, che potrebbe rientrare esattamente nel livello di prestazioni del database. Il valore massimo corrisponde tuttavia al limite del livello di prestazioni del database. Per decidere se è necessario passare al livello di prestazioni superiore a quello attualmente in uso, occorre considerare il numero di volte in cui il carico di lavoro raggiunge il 100% e confrontare tale numero con l'obiettivo del livello di servizio del carico di lavoro del database.
	
		SELECT 
		(COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent’
		,(COUNT(database_name) - SUM(CASE WHEN avg_physical_data_read_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data Read Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
	Se la query precedente restituisce un valore inferiore a 99,9 per ciascuna delle tre dimensioni della risorsa, è consigliabile prendere in esame la possibilità di passare al livello di prestazioni superiore a quello attualmente in uso o, in alternativa, usare tecniche di ottimizzazione dell'applicazione per ridurre il carico nel database SQL di Azure.
	
4. L'esercizio precedente deve tener conto anche dell'aumento di carico di lavoro previsto in futuro.

## Ottimizzazione dell'applicazione

Nel sistema SQL Server tradizionale in locale il processo di pianificazione della capacità iniziale è spesso separato dal processo di esecuzione di un'applicazione in produzione. In altre parole, l'acquisto di hardware e delle licenze associate per eseguire SQL Server viene effettuato all'inizio, mentre l'ottimizzazione delle prestazioni viene eseguita in un secondo momento. Quando si usa il database SQL di Azure è generalmente consigliabile (e, forse, desiderabile dal momento si riceve la fattura ogni mese) eseguire l'interleave del processo di esecuzione e di ottimizzazione di un'applicazione Il modello di pagamento per la capacità su richiesta consente di ottimizzare l'applicazione in modo che vengano usate le risorse minime necessarie al momento invece di effettuare l'overprovisioning massivo nell'hardware in base a ipotesi di piani di crescita futura per un'applicazione, che sono spesso errati dal momento che riguardano un futuro troppo lontano. Si noti che alcuni clienti potrebbero decidere di non ottimizzare un'applicazione e di scegliere, in alternativa, di effettuare l'overprovisioning delle risorse hardware. Questo approccio è utile se non si vuole cambiare un'applicazione chiave in periodi di attività intensiva dell'applicazione. L'ottimizzazione di un'applicazione può consentire di ridurre i requisiti delle risorse e l'importo delle fatture mensili quando si usano i livelli di servizio nel database SQL di Azure.

### Caratteristiche dell'applicazione

Anche se i livelli di servizio sono progettati per migliorare la stabilità e la prevedibilità delle prestazioni di un'applicazione, esistono alcune procedure consigliate per ottimizzare l'applicazione in modo da sfruttare al meglio le funzionalità. Sebbene in molte applicazioni sia possibile ottenere miglioramenti significativi delle prestazioni semplicemente passando a un livello di prestazioni e/o di servizio superiore, questo vantaggio non è assicurato per tutte le applicazioni senza un'ottimizzazione aggiuntiva. Anche per le applicazioni che presentano le caratteristiche seguenti sarà necessario prendere in considerazione un'ottimizzazione aggiuntiva dell'applicazione per migliorare ulteriormente le prestazioni quando si usa il database SQL di Azure.

- **Applicazioni con prestazioni ridotte a causa del comportamento "frammentato"**: tra queste sono incluse le applicazioni mediante le quali viene effettuato un numero eccessivo di operazioni di accesso ai dati che sono sensibili alla latenza di rete. Per queste applicazioni può essere necessaria una modifica per ridurre il numero di operazioni di accesso ai dati al database SQL di Azure. Ad esempio, l'applicazione può essere migliorata usando tecniche come l'invio in batch di query ad-hoc o la trasformazione delle query in stored procedure. Per altre informazioni, vedere la sezione "Invio di query in batch" riportata di seguito.
- **Database con un carico di lavoro elevato che non può essere supportato da una singola macchina**: i database in cui le risorse del livello di prestazioni Premium più elevato vengono superate non rappresentano i candidati ideali. Questi database possono trarre vantaggio dalla scalabilità orizzontale del carico di lavoro. Per altre informazioni, vedere le sezioni "Partizionamento orizzontale tra database" e "Partizionamento funzionale" riportate di seguito.
- **Applicazioni contenenti query non ottimali**: nelle applicazioni con query non ottimizzate correttamente, soprattutto nel livello di accesso ai dati, i vantaggi derivanti dalla scelta di un livello di prestazioni superiore potrebbero non soddisfare le aspettative. Tra queste sono incluse query prive della clausola WHERE, con indici mancanti o con statistiche obsolete. Queste applicazioni potranno trarre vantaggio dalle tecniche standard di ottimizzazione delle prestazioni delle query. Per altre informazioni, vedere le sezioni "Indici mancanti" e "Hint/Ottimizzazione di query" riportate di seguito.
- **Applicazioni con progettazione di accesso ai dati non ottimale**: le applicazioni con problemi intrinseci di concorrenza per l'accesso ai dati, ad esempio il deadlock, potrebbero non trarre vantaggio dalla scelta di un livello di prestazioni superiore. Gli sviluppatori di applicazioni devono provare a ridurre i round trip nel database SQL di Azure memorizzando nella cache i dati sul lato client tramite il servizio di caching di Azure o altre tecnologie di memorizzazione nella cache. Vedere la sezione "Memorizzazione nella cache a livello di applicazione" riportata di seguito.

## Tecniche di ottimizzazione
Questa sezione illustra alcune tecniche che è possibile usare per ottimizzare il database SQL di Azure in modo da ottenere prestazioni ottimali dall'applicazione ed eseguire così operazioni usando il livello di prestazioni più basso possibile. Diverse tecniche corrispondono alle tradizionali procedure consigliate di ottimizzazione di SQL Server, ma alcune sono specifiche per il database SQL di Azure. In alcuni casi è possibile estendere le tecniche tradizionali di SQL Server per poterle usare anche nel database SQL di Azure esaminando le risorse utilizzate per un database in modo da poter individuare le aree da ottimizzare ulteriormente.

### Query Performance Insight e Index Advisor
Il database SQL fornisce due strumenti nel portale di Azure classico per l'analisi e la risoluzione dei problemi di prestazioni con il database:

- [Query Performance Insight](sql-database-query-performance.md)
- [Index Advisor](sql-database-index-advisor.md)

Per altre informazioni su ciascuno strumento e sul relativo utilizzo, vedere i collegamenti precedenti. Le due sezioni seguenti sugli indici mancanti e sull'ottimizzazione delle query forniscono altri modi per trovare e correggere manualmente problemi di prestazioni simili. Si consiglia di provare innanzitutto gli strumenti nel portale per diagnosticare e correggere i problemi in modo più efficiente. Usare l'approccio di ottimizzazione manuale per i casi particolari.

### Indici mancanti
Un problema comune nelle prestazioni del database OLTP è correlato alla progettazione fisica del database. Spesso gli schemi di database vengono progettati e forniti senza verificare la scala (nel caricamento o nel volume di dati). Purtroppo, le prestazioni di un piano di query possono essere accettabili su scala ridotta, ma potrebbero peggiorare notevolmente in caso di volumi elevati di dati a livello di produzione. L'origine più comune di questo problema è legata alla mancanza di indici adatti per soddisfare i filtri o altre restrizioni in una query. Spesso, questa situazione si manifesta come una scansione di tabella quando potrebbe essere sufficiente una ricerca dell'indice.

L'esempio seguente crea un caso in cui il piano di query selezionato contiene una scansione quando invece basterebbe una ricerca.

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

Il database SQL di Azure include una funzionalità per suggerire agli amministratori del database come individuare e correggere condizioni comuni di indici mancanti. Le DMV incorporate nel database SQL di Azure considerano i casi in cui, durante la compilazione di query, l'uso di un indice consentirebbe di ridurre in modo significativo il costo stimato per l'esecuzione di una query. Durante l'esecuzione di query, si tiene traccia della frequenza con cui viene eseguito ogni piano di query e del divario stimato tra l'esecuzione del piano di query e quello immaginato in cui sarebbe presente l'indice in questione. Questo consente a un amministratore di database di ipotizzare rapidamente quali modifiche di progettazione fisica del database potrebbero migliorare il costo complessivo del carico di lavoro per un database specificato e il relativo carico di lavoro reale.

>[AZURE.NOTE] Prima di usare le DMV per trovare gli indici mancanti, innanzitutto rivedere la sezione su [Query Performance Insight e Index Advisor](#query-performance-insight-and-index-advisor).

La query seguente può essere usata per valutare i potenziali indici mancanti.

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

In questo esempio viene suggerito il seguente indice.

	CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Al termine della creazione, la stessa istruzione SELECT seleziona ora un piano diverso in cui viene usata una ricerca anziché una scansione, effettuando l'esecuzione in modo più efficiente come illustrato nel piano di query riportato di seguito.

![Piano di query con indici corretti](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

L'aspetto chiave è che la capacità di I/O di un sistema apposito condiviso è generalmente più limitata di un server dedicato. Esiste pertanto un vantaggio nella riduzione dell'I/O non necessario, che consente di sfruttare al massimo il sistema entro il valore di DTU di ciascun livello di prestazioni incluso nei livelli di servizio per il database SQL di Azure. Le opzioni appropriate di progettazione fisica del database possono migliorare notevolmente la latenza delle singole query, la velocità effettiva di richieste simultanee gestibili per unità di scala e ridurre i costi necessari per soddisfare la query. Per altre informazioni sulle DMV di indici mancanti, vedere [sys.dm\_db\_missing\_index\_details](https://msdn.microsoft.com/library/ms345434.aspx).

### Hint/Ottimizzazione di query
Query Optimizer nel database SQL di Azure è molto simile a Query Optimizer tradizionale di SQL Server. Molte delle procedure consigliate per ottimizzare le query e per comprendere i motivi delle limitazioni del modello per Query Optimizer vengono applicate anche al database SQL di Azure. L'ottimizzazione delle query nel database SQL di Azure può presentare l'ulteriore vantaggio di ridurre le richieste di risorse aggregate e consentire l'esecuzione di un'applicazione a un costo inferiore rispetto ad una equivalente non ottimizzata, dal momento che può essere eseguita usando un livello di prestazioni inferiore.

Un esempio comune visualizzato in SQL Server applicabile anche al database SQL di Azure è correlato alla modalità con cui viene eseguito lo sniffing dei parametri durante la compilazione per tentare di creare un piano più appropriato. Lo sniffing dei parametri è un processo mediante il quale tramite Query Optimizer viene considerato il valore corrente di un parametro quando si compila una query con l'intento di generare un piano di query ottimale. Sebbene con questa strategia sia spesso possibile la generazione di un piano di query notevolmente più veloce di un piano compilato senza la conoscenza dei valori di parametro, il comportamento attuale di SQL Server o del database SQL di Azure non è perfetto. Vi sono casi in cui lo sniffing del parametro non viene eseguito e altri in cui questa operazione viene eseguita ma il piano generato non è ottimale per il set completo di valori di parametro in un carico di lavoro. Microsoft include gli hint per la query (direttive) per consentire di specificare la finalità più deliberatamente ed eseguire l'override del comportamento predefinito per lo sniffing dei parametri. Spesso, l'uso di hint può risolvere i casi in cui il comportamento predefinito di SQL Server o del database SQL di Azure non è perfetto per un determinato carico di lavoro del cliente.

L'esempio seguente illustra il modo in cui tramite Query Processor è possibile generare un piano non ottimale sia per le prestazioni sia per i requisiti di risorse e come l'uso di un hint per la query può ridurre il tempo di esecuzione di query e i requisiti di risorse nel database SQL.

Di seguito è riportata un'impostazione di esempio.

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

Tramite il codice di installazione viene creata una tabella indicante la differenza nella distribuzione dei dati. Il piano di query ottimale varia in base al parametro selezionato. Purtroppo, il comportamento di memorizzazione nella cache dei piani non sempre consente la ricompilazione della query in base al valore del parametro più comune, pertanto è possibile che venga memorizzato nella cache e usato un piano non ottimale per molti valori, anche quando un piano diverso rappresenterebbe una scelta migliore per la metà dei casi. Successivamente vengono create due stored procedure identiche, ad eccezione del fatto che una include un hint per la query speciale (l'utilità è spiegata di seguito).

**Esempio (parte 1):**

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

**Esempio (parte 2: attendere 10 minuti prima di tentare questa parte in modo che sia ovviamente diversa nei dati di telemetria risultante):**

	EXEC psp2 @param2=1;
	TRUNCATE TABLE t1;
	
	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp2 @param2=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

Ogni parte di questo esempio prova a eseguire 1000 volte un'istruzione INSERT con parametri, per generare un carico sufficiente utilizzabile in un set di dati di test. Durante l'esecuzione di stored procedure, tramite Query Processor viene esaminato il valore del parametro passato alla procedura durante la prima compilazione (noto come "sniffing" dei parametri). Il piano risultante viene memorizzato nella cache e usato per le chiamate successive, anche se il valore del parametro è diverso. Di conseguenza, è possibile che non venga usato il piano ottimale in tutti i casi. Talvolta i clienti devono consentire a Query Optimizer la selezione di un piano che sia migliore per la metà dei casi anziché per il caso specifico quando la query viene compilata per la prima volta. In questo esempio, tramite il piano iniziale verrà generato un piano di scansione mediante il quale vengono lette tutte le righe per cercare tutti i valori corrispondenti al parametro.

![Ottimizzazione di query](./media/sql-database-performance-guidance/query_tuning_1.png)

Poiché la procedura è stata eseguita con il valore 1, il piano risultante è stato ottimale per 1 valore, anziché per tutti gli altri nella tabella. Il comportamento risultante probabilmente non è quello desiderato se si seleziona casualmente ogni piano, dal momento che viene eseguito più lentamente e con l'uso di più risorse.

Eseguendo il test con "SET STATISTICS IO ON" viene illustrato che il lavoro di scansione logica è stato effettuato automaticamente in questo esempio. Si può notare che sono state eseguite 1148 letture tramite il piano, che non è un risultato efficiente se per metà dei casi viene restituita una sola riga:

![Ottimizzazione di query](./media/sql-database-performance-guidance/query_tuning_2.png)

La seconda parte dell'esempio usa un hint per la query per indicare a Query Optimizer di usare un valore specifico durante il processo di compilazione. In questo caso, il comportamento di Query Processor viene forzato in modo che venga ignorato il valore passato come parametro e, in alternativa, venga presunto "UNKNOWN", vale a dire un valore con frequenza media nella tabella (ignorando la differenza). Il piano risultante è un piano basato su ricerca, più veloce e con un minore impiego medio di risorse rispetto al piano della parte 1 dell'esempio.

![Ottimizzazione di query](./media/sql-database-performance-guidance/query_tuning_3.png)

Il relativo impatto può essere visualizzato esaminando la tabella di **sys.resource\_stats**. Si noti che vi sarà un ritardo dal momento in cui si esegue il test al momento in cui i dati vengono popolati nella tabella. Per questo esempio, la parte 1 è stata eseguita durante l'intervallo di tempo 22:25:00 e la parte 2 nell'intervallo di tempo 22:35: 00. Si noti che nell'intervallo di tempo precedente sono state usate più risorse rispetto a quello successivo (in seguito ai miglioramenti di efficienza di piano).

	SELECT TOP 1000 * 
	FROM sys.resource_stats 
	WHERE database_name = 'resource1' 
	ORDER BY start_time DESC

![Ottimizzazione di query](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] Anche se l'esempio usato qui è intenzionalmente piccolo, l'impatto di parametri non ottimali può essere significativo, in particolare in database di grandi dimensioni. La differenza, in casi estremi, può essere di secondi e ore per i casi veloci e lenti.

Esaminando **sys.resource\_stats** è possibile determinare se la risorsa usata per uno specifico test usa una quantità di risorse superiore o inferiore rispetto a un altro test. Quando si confrontano i dati, eseguire i test a intervalli temporali sufficientemente distanziati affinché nella vista **sys.resource\_stats** non risultino raggruppati negli stessi intervalli di 5 minuti. Si noti inoltre che l'obiettivo dell'esercizio consiste nel ridurre il numero totale di risorse usate e non di ridurre le risorse di picco in quanto tali. In genere, anche con l'ottimizzazione di una parte di codice per la latenza viene ridotto l'utilizzo di risorse. Quando si usano gli hint per la query, verificare che le modifiche considerate in un'applicazione siano effettivamente necessarie e non influiscano negativamente sull'esperienza degli utenti che usano un'applicazione.

Se un carico di lavoro include un set di query ripetute, è spesso consigliabile acquisire e convalidare la validità di quelle scelte del piano poiché probabilmente determinerà l'unità di dimensioni della risorsa minima per ospitare il database. Dopo l'esecuzione della convalida, con un nuovo esame occasionale di quei piani sarà possibile garantirne la validità. Per altre informazioni sugli hint per la query, vedere [Hint per la query (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### Partizionamento orizzontale tra database
Poiché il database SQL di Azure viene eseguito in hardware apposito, vi sono in genere limiti della capacità inferiori per un singolo database rispetto a quelli in un'installazione tradizionale di SQL Server. Molti clienti usano tecniche di partizionamento orizzontale per estendere le operazioni in più database quando non rientrano nei limiti relativi a un singolo database nel database SQL di Azure. La maggior parte dei clienti che al momento usa le tecniche di partizionamento orizzontale nel database SQL di Azure suddivide i dati di una singola dimensione in più database. L'approccio prevede che si sia a conoscenza del fatto che spesso tramite le applicazioni OLTP si effettuano transazioni che si applicano solo a una riga o a un piccolo gruppo di righe nello schema.

>[AZURE.NOTE] Il database SQL offre ora una libreria per semplificare il partizionamento orizzontale. Per altre informazioni, vedere [Panoramica della libreria client dei database elastici](sql-database-elastic-database-client-library.md).

Ad esempio, se un database include il cliente, l'ordine e i dettagli dell'ordine (come illustrato nel database di esempio Northwind tradizionale disponibile in SQL Server), questi dati possono essere suddivisi in più database raggruppando un cliente con le relative informazioni sull'ordine e sui dettagli dell'ordine e garantendo che rimangano in un singolo database. L'applicazione suddividerebbe i diversi clienti tra database, estendendo di fatto il carico tra più database. Questo consente ai clienti di evitare di raggiungere il limite massimo delle dimensioni del database, ma consente anche al database SQL di Azure di elaborare carichi di lavoro notevolmente maggiori rispetto ai limiti corrispondenti ai diversi livelli di prestazioni, a condizione che ogni singolo database rientri nel relativo valore di DTU.

Anche se il partizionamento orizzontale del database non riduce la capacità di risorse aggregate per una soluzione, questa tecnica è estremamente efficiente nel supportare soluzioni di grandi dimensioni estese in più database e consente l'esecuzione di ciascun database in un livello di prestazioni diverso, in modo da supportare database effettivi di notevoli dimensioni con requisiti di risorse elevati.

### Partizionamento funzionale
Gli utenti di SQL Server combinano spesso molte funzioni all'interno di un singolo database. Ad esempio, se un'applicazione include la logica per gestire l'inventario di un negozio, è possibile che quel database includa la logica associata all'inventario, il rilevamento degli ordini di acquisto, le stored procedure e le viste indicizzate o materializzate mediante le quali sono stati gestiti i report di fine mese e altre funzioni. Questa tecnica offre il vantaggio di poter amministrare facilmente il database per operazioni come il backup, ma richiede anche il ridimensionamento dell'hardware per gestire il carico massimo in tutte le funzioni di un'applicazione.

Nell'architettura con scalabilità orizzontale usata nel database SQL di Azure, è spesso utile suddividere le diverse funzioni di un'applicazione in database differenti. In questo modo è possibile applicare la scalabilità a ognuno di essi in modo indipendente. Quando un'applicazione viene usata con maggiore frequenza e il relativo database riceve quindi un carico maggiore, ciò consente all'amministratore di scegliere un livello di prestazioni in modo indipendente per ciascuna funzione all'interno di un'applicazione. Nei limiti, questa architettura fa sì che le dimensioni di un'applicazione diventino più grandi di quelle gestibili da una singola macchina apposita estendendo il carico tra più macchine.

### Invio di query in batch
Per le applicazioni mediante le quali si accede ai dati con un utilizzo frequente ed elevato di query ad hoc, gran parte del tempo di risposta viene speso nelle comunicazioni di rete tra il livello applicazione e il livello di database SQL di Azure. Anche quando l'applicazione e il database SQL di Azure risiedono nello stesso data center, la latenza di rete tra questi due elementi potrebbe essere aumentata per un numero elevato di operazioni di accesso ai dati. Per ridurre i round trip in rete per queste operazioni di accesso ai dati, è consigliabile che lo sviluppatore di applicazioni consideri le opzioni di invio in batch delle query ad hoc o la compilazione di queste in stored procedure. Con l'invio in batch delle query ad hoc è possibile inviare più query come un unico grande batch in una singola operazione al database SQL di Azure. La compilazione di query ad hoc in stored procedure può produrre lo stesso risultato dell'invio in batch. L'uso di una stored procedure offre inoltre il vantaggio di aumentare le opportunità di memorizzare nella cache i piani di query nel database SQL di Azure per esecuzioni successive della stessa stored procedure.

Alcune applicazioni comportano un utilizzo elevato di scrittura. Talvolta, è possibile ridurre il carico totale di I/O in un database considerando la modalità di invio in batch delle scritture. Questa operazione è spesso semplice come l'uso di transazioni esplicite anziché di transazioni autocommit in stored procedure e batch ad hoc. Una valutazione delle differenti tecniche che si possono usare è disponibile in [Tecniche di esecuzione in batch per applicazioni del database SQL in Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Eseguire dei test sul proprio carico di lavoro per trovare il modello appropriato per l'invio in batch, tenendo in considerazione il fatto che un modello specificato può presentare garanzie di coerenza transazionale leggermente diverse. Per trovare il carico di lavoro ottimale che consenta un uso delle risorse minimo è necessario individuare la corretta combinazione di compromessi tra prestazioni e coerenza.

### Memorizzazione nella cache a livello di applicazione
Alcune applicazioni di database contengono carichi di lavoro con intensa attività di lettura. È possibile usare i livelli di memorizzazione nella cache per ridurre il carico nel database e potenzialmente per ridurre il livello di prestazioni necessario per supportare un database usando il database SQL di Azure. La [Cache Redis di Azure](https://azure.microsoft.com/services/cache/) consente a un cliente con un carico di lavoro con intensa attività di lettura di leggere i dati una volta o forse una volta per macchina di livello applicazione, a seconda della relativa configurazione, e di archiviare i dati al di fuori del database SQL di Azure. In questo modo si dispone di una possibilità per ridurre il carico del database (CPU e I/O letti), ma vi sarà un impatto sulla coerenza transazionale poiché i dati letti dalla cache potrebbero essere obsoleti rispetto ai dati nel database. Anche se vi sono numerose applicazioni in cui l'incoerenza è accettabile, ciò non rappresenta una soluzione valida per tutti i carichi di lavoro. È necessario conoscere bene tutti i requisiti delle applicazioni prima di usare una strategia di memorizzazione a livello di applicazione.

## Conclusione

I livelli di servizio nel database SQL di Azure consentono di aumentare gli standard relativi ai tipi di applicazioni create nel cloud. Insieme a un'ottimizzazione diligente delle applicazioni, offre prestazioni potenti e prevedibili per la propria applicazione. Questo documento descrive le tecniche consigliate per ottimizzare il consumo di risorse da parte di un database in modo da rientrare perfettamente in uno dei livelli di prestazioni. L'ottimizzazione è un esercizio continuo nel modello cloud e i livelli di servizio, con i livelli di prestazioni correlati, consentono agli amministratori di ottenere massimi livelli di prestazioni e al tempo stesso ridurre i costi nella piattaforma Microsoft Azure.

<!---HONumber=AcomDC_0413_2016-->