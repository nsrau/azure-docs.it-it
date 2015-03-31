<properties 
	pageTitle="Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio" 
	description="Informazioni su come aggiornare i database Web o Business ai nuovi livelli di servizio/prestazioni del database SQL di Azure. Aggiornare il database SQL, modificare i livelli di servizio e modificare i livelli delle prestazioni." 
	services="sql-database" 
	documentationCenter="" 
	authors="jenniehubbard" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.date="2/23/2015" 
	ms.author="jhubbard; v-stste@microsoft.com" 
	ms.workload="data-services" 
	ms.topic="article" 
	ms.tgt_pltfrm=""/>


# Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio

I database basati sull'edizione Web e sull'edizione Business del database SQL di Azure stanno per essere deprecati e verranno [ritirati a settembre 2015](http://msdn.microsoft.com/library/azure/dn741330.aspx). È pertanto necessario iniziare a pianificare l'aggiornamento dei database Web o Business esistenti ai livelli di servizio Basic, Standard o Premium.

## Informazioni generali

<p> I database SQL Web e Business di Azure vengono eseguiti in un ambiente multi-tenant condiviso senza capacità riservate di risorse per il database. L'attività di altri database all'interno di questo cluster di risorse condivise può influire sulle prestazioni dell'utente. La disponibilità di risorse in qualsiasi momento dipende molto da altri carichi di lavoro simultanei in esecuzione nel sistema. Ciò può comportare prestazioni dell'applicazione di database estremamente variabili e imprevedibili. I suggerimenti dei clienti indicano che queste prestazioni imprevedibili sono difficili da gestire e che sono preferibili prestazioni più prevedibili. 

Per risolvere questa situazione, il servizio database SQL di Azure ha introdotto nuovi livelli di servizio [(Basic, Standard e Premium)](http://msdn.microsoft.com/library/dn741340.aspx), che offrono prestazioni prevedibili e un'ampia gamma di nuove funzionalità per la sicurezza e la continuità aziendale. Questi nuovi livelli di servizio sono progettati per fornire un grado di risorse specificato per un carico di lavoro di database, indipendentemente da altri carichi di lavoro del cliente in esecuzione in tale ambiente. Il risultato è un comportamento delle prestazioni estremamente prevedibile. 

Queste modifiche fanno nascere domande su come valutare e decidere quale sia il nuovo livello di servizio più adatto ai database Web e Business correnti e sull'effettivo processo di aggiornamento.

In definitiva, la scelta migliore è costituita dalla combinazione di livello di servizio e livello di prestazioni in grado di garantire un equilibrio ottimale tra funzionalità, prestazioni e costo, ma anche di soddisfare pienamente le esigenze e i requisiti aziendali dell'applicazione.

Questo documento presenta una metodologia guidata per l'aggiornamento dei database Web o Business ai nuovi livelli di servizio/livelli di prestazioni.

È importante tenere presente che i database SQL di Azure non sono bloccati in un livello di servizio o di prestazioni specifico e che pertanto è possibile passare facilmente a un altro dei vari livelli di servizio e di prestazioni disponibili quando cambiano i requisiti del database . I database SQL Basic, Standard e Premium vengono in effetti fatturati su base oraria e si ha la possibilità di aumentare o ridurre le risorse di ognuno di essi quattro volte nell'arco di un periodo di 24 ore (mediante il [portale Azure o a livello di codice](http://msdn.microsoft.com/library/azure/ff394116.aspx)). Questo significa che è possibile cambiare livello di servizio e di prestazioni per massimizzare le prestazioni, sfruttare al meglio il set di funzionalità e ridurre al minimo i costi del database in base ai requisiti e al carico di lavoro variabile dell'applicazione. Ciò significa anche che la valutazione e la modifica del livello di servizio e di prestazioni del database (ai fini dell'aumento o della riduzione delle risorse) rappresentano un processo in continua evoluzione che deve essere parte integrante delle procedure pianificate per la manutenzione e l'ottimizzazione delle prestazioni. 


## Aggiornare database Web e Business

Quando si esegue l'aggiornamento a un nuovo livello di servizio/prestazioni, i database Web o Business restano online e continuano a funzionare per l'intera durata dell'operazione di aggiornamento. Al momento della transizione effettiva al nuovo livello di prestazioni, le connessioni al database possono interrompersi temporaneamente per un periodo molto breve (in genere, per alcuni secondi). Se un'applicazione ha una gestione degli errori temporanei per le interruzioni di connessione, è sufficiente impostare la protezione dalle connessioni interrotte alla fine dell'aggiornamento. 

L'aggiornamento di un database Web o Business a un nuovo livello di servizio prevede i seguenti passaggi:


1. Determinare il livello di servizio in base alla funzionalità
2. Determinare un livello di prestazioni accettabile in base alla cronologia d'uso delle risorse
3. Determinare i motivi per cui le prestazioni correnti del database Web o Business tendono ai livelli Premium più elevati
4. Ottimizzare il carico di lavoro in base a un livello di prestazioni inferiore
5. *Eseguire l'aggiornamento al nuovo livello di prestazioni/livello di servizio*
6. Monitorare l'aggiornamento al nuovo livello di servizio/prestazioni
7. Monitorare il database dopo l'aggiornamento



## 1. Determinare il livello di servizio in base alla funzionalità

I livelli di servizio Basic, Standard e Premium offrono set di funzionalità diversi. Pertanto, per scegliere un livello appropriato, è innanzitutto necessario determinare il livello di servizio in grado di garantire il livello minimo di funzionalità necessario per l'applicazione e la propria azienda. 

Ad esempio, considerare per quanto tempo è necessario conservare i backup, se sono richieste funzionalità di [replica geografica standard o attiva](http://msdn.microsoft.com/library/azure/dn783447.aspx) le dimensioni massime complessive necessarie per i database e così via. Questi requisiti determinano la scelta del livello di servizio minimo.

Il livello "Basic" viene usato soprattutto per database molto piccoli con un'attività ridotta. Quindi, quando si sceglie il livello di servizio per un aggiornamento, in genere è consigliabile iniziare con il livello "Standard" o "Premium" in base al livello minimo di funzionalità necessarie.

I livelli di prestazioni e le funzionalità del nuovo livello di servizio vengono riepilogati e messi a confronto nella seguente tabella:

![Service Tier feature comparison][1]


**Risorse aggiuntive per confrontare i livelli di servizio e di prestazioni:**

| Articolo | Descrizione |
|:--|:--|
|[Livelli di servizio (edizioni) del database SQL di Azure](http://msdn.microsoft.com/library/azure/dn741340.aspx)| Informazioni generali sui livelli di servizio Basic, Standard e Premium.|
|[Livelli di servizio e livelli di prestazioni del database SQL di Azure](http://msdn.microsoft.com/library/dn741336.aspx)| Metriche e funzionalità di ogni livello di servizio (e istruzioni su come monitorare l'uso dei database nel portale di gestione o mediante viste a gestione dinamica). |
|[Differenze dei livelli di servizio](http://msdn.microsoft.com/library/dn369873.aspx#Different)| Informazioni aggiuntive sui diversi livelli di servizio, inclusi alcuni motivi per cui preferire un determinato livello rispetto a un altro. |
|[Continuità aziendale del database SQL di Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)|Dettagli delle funzionalità di continuità aziendale e ripristino di emergenza (ripristino a un momento specifico, ripristino geografico, replica geografica) disponibili per i diversi livelli di servizio.|
|[Database SQL - Prezzi](http://azure.microsoft.com/pricing/details/sql-database/)|Informazioni dettagliate sui prezzi per i diversi livelli di servizio e di prestazioni.|

<br>

Dopo la selezione del livello di servizio adatto a soddisfare i requisiti del database, il passaggio successivo consiste nello scegliere un livello di prestazioni accettabile per i carichi di lavoro effettivi del database. 



## 2. Determinare un livello di prestazioni accettabile in base alla cronologia d'uso delle risorse

Il servizio database SQL di Azure espone informazioni nel portale di gestione e in viste di sistema per fornire il nuovo livello di servizio e di prestazioni comparabile suggerito per il database Web o Business esistente.

Poiché ai database Web e Business non sono associati limiti di DTU/risorse garantiti, i valori delle percentuali vengono normalizzati in termini di quantità di risorse disponibili per un database nel livello di prestazioni S2. Il consumo percentuale di DTU medio di un database in qualsiasi intervallo specifico può essere calcolato come il valore percentuale più alto tra utilizzo di CPU, IO e log in tale intervallo.


Usare il portale di gestione per avere una panoramica generale dell'uso percentuale di DTU, quindi esaminare i dettagli usando le viste di sistema.


### Come visualizzare il consumo di DTU nel portale di gestione
Il portale di gestione fornisce informazioni sul consumo di DTU per un database Web o Business esistente.
Le informazioni sulle DTU sono disponibili nel portale Azure corrente.


**Portale di gestione**

1. Accedere al [portale di gestione](https://manage.windowsazure.com) e passare a un database Web o Business esistente.
2. Fare clic sulla scheda **MONITORAGGIO**.
3. Fare clic su **AGGIUNGI METRICHE**.
4. Selezionare **Percentuale DTU** e fare clic sul segno di spunta nella parte inferiore per confermare.

Dopo la conferma, nella tabella dovrebbero venire visualizzati i dati relativi alla **percentuale DTU**. Tenere presente che si tratta di una percentuale rispetto alle DTU in un database nel livello Standard(S2), ovvero 50 DTU.

È interessante notare anche che questi dati rappresentano la media dei campioni acquisiti ogni 5 minuti, pertanto tra un campione e l'altro possono verificarsi brevi picchi di attività che non si riflettono in tali metriche.

![DTU percentage data][2]

I dati nell'esempio precedente mostrano un uso medio pari a circa 10 DTU (19,23% di 50) e una percentuale di DTU massima di ~28 DTU (55,83% x 50). 
Presupponendo che questi dati rappresentino il carico di lavoro tipico, probabilmente sarà opportuno selezionare Standard(S1) per l'aggiornamento iniziale. Standard(S0) garantisce 10 DTU, ovvero l'uso medio, ma ciò significa che il database funzionerà in media al 100% della capacità e questa non è mai una situazione ideale. Se S1 è una buona scelta per l'uso medio, potrebbe non esserlo per una situazione di picco massimo (MAX). I picchi potrebbero ad esempio verificarsi a seguito di un processo di manutenzione notturno senza quindi influire sull'uso effettivo da parte degli utenti, pertanto il fatto di avere prestazioni ridotte durante tale periodo di tempo potrebbe non costituire un problema. Ma se non si è certi di quando si verificheranno eventuali picchi, è opportuno analizzare ulteriormente il consumo percentuale di DTU.

Per esaminare i dettagli relativi al consumo di risorse di un database, è possibile avvalersi delle viste di sistema fornite.


### Viste di sistema


I dati relativi al consumo di risorse dei database Web e Business sono accessibili tramite la vista [sys.resource_stats](http://msdn.microsoft.com/library/azure/dn269979.aspx) nel database master del server logico in cui si trova il database corrente. Visualizza i dati sul consumo delle risorse come percentuali del limite del livello di prestazioni. Questa vista fornisce i dati fino a un massimo di 14 giorni fa, a intervalli di 5 minuti.  Per recuperare il consumo di DTU medio per un database, inoltrare la seguente query sul database master:

 
                   
     SELECT start_time, end_time
	 ,(SELECT Max(v)
         FROM (VALUES (avg_cpu_percent)
                    , (avg_physical_data_read_percent)
                    , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.resource_stats
    WHERE database_name = '<your db name>'
    ORDER BY end_time DESC;

Le informazioni sul consumo di DTU in termini di livello di database S2 consentono di normalizzare il consumo corrente dei database Web/Business in termini di database con nuovi livelli e di verificare dove funzionano meglio. Se, ad esempio, il consumo percentuale di DTU medio mostra un valore pari all'80%, significa che il database sta usando DTU a un tasso dell'80% del limite di un database al livello di prestazioni S2. Se nella vista **sys.resource_stats** appaiono valori superiori al 100%, significa che è necessario un livello di prestazioni superiore a S2. Prendiamo l'esempio di un picco con un valore percentuale di DTU pari al 300%.  Significa che si stanno usando tre volte più risorse di quelle disponibili in un livello S2. Per determinare una dimensione iniziale ragionevole, confrontare le DTU disponibili in un livello S2 (50 DTU) con le dimensioni più elevate successive (S3/P1 = 100 DTU o 200% di S2, P2 = 200 DTU o 400% di S2). Poiché si è al 300% di S2, è preferibile iniziare con un livello P2 e ripetere il test. 

In base alla percentuale d'uso di DTU e all'edizione massima necessaria per il carico di lavoro, è possibile determinare il livello di servizio e il livello di prestazioni più adatti al carico di lavoro del database (come indicato dalla percentuale di DTU e dalle potenze di DTU relative di diversi [livelli di prestazioni)](http://msdn.microsoft.com/library/azure/dn741336.aspx). Ecco una tabella che illustra il mapping della percentuale di consumo di risorse Web/Business ai gradi di prestazioni dei nuovi livelli equivalenti: 

![Resource consumption][4]

> **Nota:**
> I relativi numeri di DTU tra i diversi livelli di prestazioni si basano sul carico di lavoro del [benchmark del database SQL di Azure](http://msdn.microsoft.com/library/azure/dn741327.aspx). Poiché il carico di lavoro del database è probabilmente diverso da quello del benchmark, è consigliabile usare i calcoli precedenti come linea guida per l'adeguamento iniziale per il database Web/Business nei nuovi livelli. Una volta spostato il database al nuovo livello, usare il processo illustrato nella sezione precedente per convalidare/ottimizzare il livello di servizio più appropriato alle esigenze del carico di lavoro.
> 
> mentre il nuovo livello di prestazioni/livello della nuova edizione suggerito prende in considerazione l'attività del database negli ultimi 14 giorni, questi dati si basano sulla media dei dati di esempio sul consumo di risorse calcolata su 5 minuti. Per questo è possibile che non vengano considerati aumenti improvvisi di attività con una durata inferiore a 5 minuti. Questa linea guida dovrebbe quindi essere usata come punto di partenza a cui aggiornare il database. Una volta aggiornato il database al livello consigliato, sono necessarie altre operazioni di monitoraggio, test e convalida, quindi il database può essere spostato a un livello/grado di prestazioni superiore/inferiore in base alle esigenze.

Ecco una query sul database master, che esegue il calcolo per il database con livello Web/Business e suggerisce l'edizione adatta al carico di lavoro per ognuno di questi intervalli di dati di esempio da 5 minuti.

> **Nota:** questa query è utile solo per i database Web/Business e **non** darà risultati corretti per i database nei nuovi livelli.

    WITH DTU_mapping AS
    ( SELECT *
        FROM ( VALUES (1, 10,'Basic'), (2, 20,'S0'), (3, 40,'S1'), (4, 100, 'S2')
                    , (5, 200, 'S3/P1'), (6, 1600,'Premium')
           ) AS t(id, percent_of_S2, target_edition)
    ), rc as
    ( SELECT start_time, end_time
           , (SELECT Max(v)
                FROM (VALUES (avg_cpu_percent)
                       		, (avg_physical_data_read_percent)
                       		, (avg_log_write_percent)
                   ) AS value(v)) as [avg_DTU_percent]
        FROM sys.resource_stats	
       WHERE database_name = 'WebDB'
    )
    SELECT rc.*
         , (SELECT TOP(1) t.target_edition
              FROM DTU_mapping AS t
             WHERE t.percent_of_S2 > CAST(1.2*rc.avg_DTU_percent as int)
             ORDER BY t.percent_of_S2) as target_edition
    FROM rc;

**Risultati di esempio:**

![Sample Result](http://i.imgur.com/CTnjv26.png)

Il grafico rappresenta la tendenza del consumo percentuale medio di DTU nel corso del tempo. Ecco un grafico di esempio per un database che si trova per lo più in un livello S2, con alcuni picchi di attività che fanno salire il livello del database a P1.  Il consumo di DTU nel corso del tempo passa dai limiti 'Basic' fino ai limiti 'P1'. Per adattare completamente questo database al nuovo livello, sarà necessario un database con livello di servizio Premium e livello di prestazioni 'P1'. D'altra parte, un database con livello S2 può funzionare se queste impennate occasionali al livello P1 sono rare.

![DTU Usage](http://i.imgur.com/e4N4ay5.png)

**Impatto della memoria sulle prestazioni:** anche se la memoria è una delle dimensioni delle risorse che contribuisce alla classificazione DTU, il database SQL è progettato per usare tutta la memoria disponibile per le operazioni del database. Per questo motivo il consumo della memoria non è incluso nel consumo di DTU medio nella query precedente. D'altra parte, se si passa a un livello di prestazioni inferiore, la memoria disponibile per il database viene ridotta. Ciò può comportare un consumo di IO più elevato che influisce sul consumo di DTU. Quindi, quando si passa a un livello di prestazioni inferiore, assicurarsi di avere abbastanza capacità nella percentuale di IO. Usare la DMV [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) citata sopra per monitorarla.



## 3. Determinare i motivi per cui le prestazioni correnti del database Web o Business tendono ai livelli Premium più elevati

Per i singoli database Web e Business non viene riservata alcuna quantità specifica di capacità delle risorse. Inoltre, non è disponibile alcun meccanismo mediante il quale i clienti possano aumentare o ridurre le prestazioni per un database Web o Business. Le prestazioni dei database Web e Business possono pertanto essere molto variabili, passando inaspettatamente da un livello minimo a un livello Premium. Questa variabilità delle prestazioni dipende *unfairly* dal livello generale di consumo delle risorse in un determinato momento da parte degli altri database dell'ambiente multi-tenant che condividono le risorse.  

L'obiettivo del servizio database SQL di Azure è naturalmente quello di consentire un'esecuzione il più possibile ottimale al 100% per tutti i database Web e Business. Finora è stato pertanto possibile mantenere livelli di prestazioni medie tendenti ai livelli Premium per i database Web e Premium. Ecco perché le prestazioni del database esistente possono essere riconducibili ai livelli Premium attuali. Sfortunatamente questo ha generato aspettative poco realistiche al momento di confrontare i database Web e Business con i nuovi livelli di servizio/prestazioni durante la valutazione del livello di servizio a cui passare con l'aggiornamento.

Per comprendere più chiaramente le differenze tra i livelli Web/Business e i livelli di servizio Basic, Standard e Premium, fare riferimento all'immagine sottostante. Considerare nove database SQL in esecuzione nel modello Web/Business con risorse condivise rispetto a nove database SQL in esecuzione nel modello di livelli di servizio Basic, Standard e Premium. Nel modello Web/Business sono evidenti gli effetti dei 'noisy neighbors' sugli altri database che fanno parte del pool di risorse condivise. Quando un database esegue un carico di lavoro a elevato uso di risorse, l'operazione incide su tutti gli altri database e le risorse disponibili iniziano a diminuire. Nei livelli di servizio Basic, Standard e Premium ***viene*** allocata una quantità specifica di risorse per ciascun database, in modo che gli altri database dell'ambiente condiviso siano essenzialmente esclusi dal problema dei vicini che disturbano e possano operare in base al livello di prestazioni selezionato. 

![Predictable performance of the new service tiers][3]

Se la percentuale di DTU complessiva è particolarmente elevata, iniziare a studiare le metriche dettagliate che costituiscono le DTU, ad esempio esaminando specificamente i dettagli del log di I/O e uso della memoria del database. Questa analisi può essere utile per individuare potenziali aree in cui ottimizzare e ridurre il consumo di DTU.


## 4.	Ottimizzare il carico di lavoro in base a un livello di prestazioni inferiore
Se l'analisi della cronologia relativa all'uso delle risorse del database indica la necessità di effettuare l'aggiornamento a un livello di prestazioni più costoso del previsto, è possibile esaminare aree in cui può essere utile ottimizzare ulteriormente le prestazioni. 

Sulla base della propria conoscenza dei dettagli dell'applicazione, se l'uso delle risorse risulta notevolmente elevato rispetto al tipico carico di lavoro previsto, l'applicazione può ancora trarre vantaggio in qualche misura dall'ottimizzazione delle prestazioni.

In realtà, un'ulteriore ottimizzazione delle prestazioni può influire positivamente su tutti i database.

Oltre alle consuete attività di manutenzione volte all'ottimizzazione, tra cui l'analisi di indici, piani di esecuzione e così via, è consigliabile ottimizzare le routine di accesso ai dati in modo che usino il database SQL di Azure come destinazione. 

| Articolo | Descrizione |
|:--|:--|
| [Linee guida sulle prestazioni del database SQL di Azure](http://msdn.microsoft.com/library/dn369873.aspx) | La sezione 'Tuning Your Application' fornisce tecniche e suggerimenti dettagliati per ottimizzare le prestazioni di un database SQL di Azure.|
|[Strumenti per il monitoraggio e l'ottimizzazione delle prestazioni](https://msdn.microsoft.com/library/ms179428.aspx)| Collegamenti e descrizioni degli strumenti disponibili per monitorare gli eventi in SQL Server e ottimizzare la struttura dei database fisici.|
|[Monitoraggio e ottimizzazione delle prestazioni](https://msdn.microsoft.com/library/ms189081.aspx)|Sezione sull'ottimizzazione delle prestazioni di SQL Server 2014 in MSDN.|
| [Risoluzione dei problemi di prestazioni in SQL Server 2008](https://msdn.microsoft.com/library/dd672789.aspx) | White paper meno recente, ma ancora interessante che fornisce istruzioni per la risoluzione dei problemi di prestazioni comuni, tra cui informazioni utili per eliminare i colli di bottiglia a livello di memoria e di CPU.|
|[Risoluzione dei problemi e ottimizzazione delle query con il database SQL di Azure](http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-azure-sql-database.aspx)|Argomento meno recente, ma ancora interessante sulle viste a gestione dinamica e sul relativo uso per la risoluzione dei problemi.|



## 5. Eseguire l'aggiornamento al nuovo livello di prestazioni/livello di servizio
Dopo aver determinato il livello di servizio e il livello di prestazioni appropriati per il database Web o Business, è possibile aggiornare il database al nuovo livello in diversi modi:

| Strumento di gestione | Per cambiare il livello di servizio e il livello di prestazioni di un database|
| :---| :---|
| [Portale di gestione di Azure](https://manage.windowsazure.com) | fare clic sulla scheda **RIDIMENSIONA** nella pagina Dashboard del database. |
| [Azure PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) | usare il cmdlet [Set-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546732.aspx). |
| [API REST di gestione dei servizi](http://msdn.microsoft.com/library/azure/dn505719.aspx) | usare il comando [Update Database](http://msdn.microsoft.com/library/dn505718.aspx).|
| [Transact-SQL](http://msdn.microsoft.com/library/bb510741.aspx) | usare l'istruzione [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx). |

Per informazioni dettagliate, vedere [Modifica dei livelli di servizio e dei livelli di prestazioni di un database](http://msdn.microsoft.com/library/dn369872.aspx)


## 6.	Monitorare l'aggiornamento al nuovo livello di servizio/prestazioni
Il database SQL di Azure fornisce informazioni di stato sulle operazioni di gestione (come CREATE, ALTER, DROP) eseguite su un database nella DMV sys.dm_operation_status nel database master del server logico in cui si trova il database corrente. [Vedere la documentazione su sys.dm_operation_status.](http://msdn.microsoft.com/library/azure/dn270022.aspx) Usare la DMV con lo stato delle operazioni per determinare l'avanzamento dell'operazione di aggiornamento per un database. Questa query di esempio mostra tutte le operazioni di gestione eseguite su un database:

    SELECT o.operation, o.state_desc, o.percent_complete
    , o.error_code, o.error_desc, o.error_severity, o.error_state
    , o.start_time, o.last_modify_time
    FROM sys.dm_operation_status AS o
    WHERE o.resource_type_desc = 'DATABASE'
    and o.major_resource_id = '<database_name>'
    ORDER BY o.last_modify_time DESC;

Se per l'aggiornamento è stato usato il portale di gestione, nel portale è disponibile anche una notifica per l'operazione.

### Cosa accade quando il carico di lavoro del database raggiunge i limiti delle risorse dopo l'aggiornamento?
I livelli di prestazioni vengono calibrati e gestiti per poter fornire le risorse necessarie per eseguire il carico di lavoro del database fino ai limiti massimi consentiti per il livello di servizio/livello di prestazioni selezionato (consumo delle risorse al 100%). Se il carico di lavoro raggiunge uno dei limiti di CPU/IO dati/IO di log, si continuerà a ricevere le risorse al massimo livello consentito, ma probabilmente si noterà un aumento delle latenze per le query. Se viene raggiunto uno di questi limiti, non si verificheranno errori, ma solo un rallentamento nel carico di lavoro, a meno che il rallentamento non diventi così grave da provocare il timeout delle query. ISe si raggiungono i limiti relativi al numero massimo di richieste o di sessioni utente simultanee (thread di lavoro), verrà visualizzato l'[errore 10928 o 10929](http://msdn.microsoft.com/library/azure/dn338078.aspx).


## 7.	Monitorare il database dopo l'aggiornamento
Dopo l'aggiornamento del database Web/Business al nuovo livello, è consigliabile monitorare attivamente il database per verificare che le applicazioni siano in esecuzione con le prestazioni desiderate e ottimizzare l'utilizzo in base alle esigenze. Per monitorare il database, è consigliata la seguente procedura aggiuntiva.


**Dati sul consumo delle risorse:** per i database Basic, Standard e Premium sono disponibili dati più granulari sul consumo delle risorse in una nuova DMV denominata [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) nel database utente. Questa DMV fornisce informazioni sul consumo delle risorse in tempo reale con una granularità di 15 secondi per l'ora precedente di funzionamento. Il consumo percentuale di DTU per un intervallo viene calcolato come consumo percentuale massimo delle dimensioni di CPU, IO e log. Ecco una query per calcolare il consumo percentuale medio di DTU nell'ultima ora:

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

 
Informazioni dettagliate sull'uso di questa DMV, sono disponibili nella [documentazione](http://msdn.microsoft.com/library/dn800981.aspx) specifica.  [Linee guida sulle prestazioni del database SQL di Azure](http://msdn.microsoft.com/library/azure/dn369873.aspx) illustra come monitorare e ottimizzare l'applicazione.


- **Avvisi:** configurare 'Alerts' nel portale di gestione di Azure per ricevere una notifica quando il consumo di DTU per un database aggiornato si avvicina a un determinato livello elevato. Nel portale di gestione di Azure è possibile configurare avvisi di database per diverse metriche delle prestazioni, come DTU, CPU, I/O e log. 

	Ad esempio, è possibile impostare un avviso di posta elettronica sulla percentuale DTU se il valore medio della percentuale DTU supera il 75% negli ultimi 5 minuti. Fare riferimento a [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure](http://msdn.microsoft.com/library/azure/dn306638.aspx) per altre informazioni sulla configurazione di notifiche di avviso.


- **Aggiornamento/Downgrade pianificato del livello di prestazioni:** se ci sono scenari specifici dell'applicazione che richiedono prestazioni maggiori solo in determinati momenti della giornata/settimana, è possibile usare [Automazione di Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx) per pianificare un'operazione per aumentare/ridurre il livello di prestazioni del database..

	Ad esempio, aggiornare il database a un livello di prestazioni più elevato per la durata di un processo batch/di manutenzione settimanale e ridurlo al termine del processo. Questo tipo di pianificazione è utile anche per grandi operazioni a elevato utilizzo di risorse, come il caricamento di dati, la ricompilazione di indici e così via. Tenere presente che il modello di fatturazione del database SQL di Azure si basa sull'utilizzo orario di un livello di servizio/livello di prestazioni. Questa flessibilità consente di programmare o pianificare gli aggiornamenti in modo economicamente più vantaggioso.



## Riepilogo
Il servizio Database SQL di Azure fornisce i dati di telemetria e gli strumenti per valutare i carichi di lavoro dei database Web/Business e di determinare il livello di servizio più adatto per l'aggiornamento. Il processo di aggiornamento è abbastanza semplice e può essere eseguito senza disconnettere il database o perdere dati. I database aggiornati usufruiscono delle prestazioni prevedibili e delle altre funzionalità offerte dai nuovi livelli di servizio.




<!--Image references-->
[1]: ./media/sql-database-upgrade-new-service-tiers/service-tier-features.png
[2]: ./media/sql-database-upgrade-new-service-tiers/portal-dtus.JPG
[3]: ./media/sql-database-upgrade-new-service-tiers/web-business-noisy-neighbor.png
[4]: ./media/sql-database-upgrade-new-service-tiers/resource_consumption.png


<!--HONumber=47-->
