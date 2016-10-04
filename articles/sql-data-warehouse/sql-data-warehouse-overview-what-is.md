<properties
   pageTitle="Che cos'è SQL Data Warehouse di Azure? | Microsoft Azure"
   description="Database distribuito di livello aziendale, in grado di elaborare volumi di dati relazionali e non relazionali anche nell'ordine di petabyte. Si tratta del primo data warehouse basato sul cloud del settore con possibilità di aumento, riduzione e sospensione in pochi secondi."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>  

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/27/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>  


# Che cos'è SQL Data Warehouse di Azure?

Azure SQL Data Warehouse è un database basato sul cloud, con possibilità di aumentare il numero di istanze, che può elaborare volumi massivi di dati sia relazionali che non relazionali. Basato sull'architettura di elaborazione parallela massiva (MPP, Massively Parallel Processing), SQL Data Warehouse può gestire il carico di lavoro dell'organizzazione.

SQL Data Warehouse:

- Combina il database relazionale di SQL Server con le funzionalità di aumento del numero di istanze del cloud di Azure. È possibile aumentare, ridurre, sospendere o riprendere il calcolo in pochi secondi. È possibile ridurre i costi aumentando l'uso della CPU quando occorre e riducendo l'utilizzo negli orari non di punta.
- Sfrutta la piattaforma Azure. È facile da distribuire, può essere mantenuto senza problemi ed è completamente a tolleranza di errore grazie ai backup automatici.
- È complementare all'ecosistema di SQL Server. È possibile sviluppare con il familiare linguaggio Transact-SQL (T-SQL) e gli strumenti di SQL Server.

Questo articolo illustra le funzionalità principali di SQL Data Warehouse.

## Architettura di elaborazione parallela massiva (MPP)

SQL Data Warehouse è un sistema di database distribuito a elaborazione parallela massiva. Suddividendo i dati e le funzionalità di elaborazione tra più nodi, SQL Data Warehouse è in grado di offrire un'enorme scalabilità, ben oltre qualsiasi sistema singolo. In background SQL Data Warehouse distribuisce i dati in risorse di archiviazione in modalità di condivisione "shared-nothing" e in unità di elaborazione diversi. I dati vengono memorizzati nella risorsa di archiviazione con ridondanza locale Premium e collegati ai nodi di calcolo per l'esecuzione di query. Con questa architettura, SQL Data Warehouse adotta un approccio di tipo "dividi e conquista" per l'esecuzione di query T-SQL complesse. Le richieste vengono ricevute dal nodo di controllo, ottimizzate e quindi passate ai nodi di calcolo per svolgere le attività in parallelo.

Combinando l'architettura MPP e le funzionalità di archiviazione di Azure, SQL Data Warehouse può:

- Aumentare o ridurre lo spazio di archiviazione indipendente dal calcolo.
- Aumentare o ridurre il calcolo senza spostare i dati.
- Sospendere la capacità di calcolo mantenendo intatti i dati.
- Riprendere la capacità di calcolo con pochissimo preavviso.

Il diagramma seguente illustra l'architettura in modo più dettagliato.

![Architettura di SQL Data Warehouse][1]


**Nodo di controllo:** il nodo di controllo gestisce e ottimizza le query. È il front-end che interagisce con tutte le applicazioni e le connessioni. In SQL Data Warehouse il nodo di controllo si basa sul database SQL e quando si stabilisce la connessione ha lo stesso aspetto. In background il nodo di controllo coordina lo spostamento e il calcolo di tutti i dati e i calcoli richiesti per l'esecuzione di query parallele sui dati distribuiti. Quando si invia una query T-SQL a SQL Data Warehouse, il nodo di controllo la trasforma in query distinte che saranno eseguite su ogni nodo di calcolo in parallelo.

**Nodi di calcolo:** offrono le potenzialità necessarie per SQL Data Warehouse. Sono database SQL che archiviano i dati ed elaborano le query. Quando si aggiungono dati, SQL Data Warehouse distribuisce le righe ai nodi di calcolo. I nodi di calcolo sono i ruoli di lavoro che eseguono le query parallele sui dati. Dopo l'elaborazione, restituiscono i risultati al nodo del controllo. Per completare la query, il nodo controllo aggrega i risultati e restituisce il risultato finale.

**Archiviazione**: i dati vengono memorizzati nell'archivio BLOB di Azure. Quando i nodi di calcolo interagiscono con i dati, scrivono e leggono direttamente da e nell'archiviazione BLOB. Dato che l'archiviazione di Azure si espande in modo trasparente ed esteso, SQL Data Warehouse può fare altrettanto. Poiché elaborazione e archiviazione sono indipendenti, SQL Data Warehouse può ridimensionare automaticamente le risorse di archiviazione separatamente dal ridimensionamento delle risorse di calcolo e viceversa. Anche l'archivio BLOB di Azure è completamente a tolleranza di errore e semplifica il processo di backup e ripristino.

**Data Movement Service:** (DMS) sposta i dati tra i nodi. DMS fornisce ai nodi di calcolo l'accesso ai dati necessario per eseguire join e aggregazioni. DMS non è un servizio di Azure. È un servizio di Windows che viene eseguito insieme al database SQL in tutti i nodi. Poiché DMS viene eseguito in background, l'utente non interagisce direttamente con esso. Quando tuttavia si esaminano i piani di query, si noterà che includono alcune operazioni DMS perché lo spostamento dei dati è necessario per eseguire ogni query in parallelo.


## Ottimizzato per carichi di lavoro di data warehouse

L'approccio MPP è supportato da numerose ottimizzazioni delle prestazioni specifiche per il data warehousing:

- Un'utilità di ottimizzazione delle query distribuita e un set di statistiche complesse su tutti i dati. Usando le informazioni sulla distribuzione e sulle dimensioni dei dati, il servizio è in grado di ottimizzare le query in base a una valutazione del costo di operazioni di query distribuite specifiche.

- Tecniche e algoritmi avanzati integrati nel processo di spostamento dei dati per spostare in modo efficiente i dati tra le diverse risorse di calcolo, secondo le esigenze, per eseguire la query. Queste operazioni di spostamento dei dati sono predefinite e tutte le ottimizzazioni di Data Movement Service vengono eseguite automaticamente.

- Indici **columnstore** cluster per impostazione predefinita. Usando una risorsa di archiviazione basata su colonne, SQL Data Warehouse raggiunge una compressione cinque volte superiore all'archiviazione tradizionale basata su righe e prestazioni delle query fino a dieci volte o maggiori. Query di analisi che devono eseguire l'analisi di un numero elevato di righe forniscono prestazioni eccezionali sugli indici columnstore.


## Prestazioni prevedibili e scalabili

SQL Data Warehouse separa le risorse di archiviazione e calcolo consentendo il ridimensionamento di entrambe in modo indipendente. SQL Data Warehouse può essere ridimensionato in modo rapido e semplice per aggiungere risorse di calcolo in qualsiasi momento. L'uso dell'archivio BLOB di Azure è complementare a queste funzionalità. I BLOB offrono non solo un'archiviazione stabile e replicata, ma anche l'infrastruttura per un'espansione semplice e a basso costo. Usando questa combinazione di archiviazione con scalabilità cloud e funzionalità di calcolo di Azure, SQL Data Warehouse consente di pagare per un'archiviazione con le prestazioni di query quando sono necessarie. La modifica della quantità di risorse di calcolo è semplice, basta spostare a sinistra o a destra un dispositivo di scorrimento nel portale di Azure. In alternativa, può anche essere pianificata con T-SQL e PowerShell.

Oltre alla possibilità di controllare completamente la quantità del calcolo in modo indipendente dall'archiviazione, SQL Data Warehouse consente di sospendere completamente il data warehouse e ciò significa non dover pagare le risorse di calcolo quando non sono necessarie. Conservando lo spazio di archiviazione, tutto il calcolo viene rilasciato nel pool principale di Azure, riducendo i costi. Quando è necessario, basta riprendere semplicemente il calcolo e i dati e il calcolo saranno disponibili per il carico di lavoro.

## Unità Data Warehouse

L'allocazione di risorse per SQL Data Warehouse viene misurata in Unità Data Warehouse (DWU). Le DWU sono una misura delle risorse sottostanti, ad esempio CPU, memoria, operazioni di I/O al secondo, allocate a SQL Data Warehouse. Se si aumenta il numero di DWU, si aumentano anche le risorse e le prestazioni. In particolare, si usano le DWU per poter eseguire queste operazioni:

- Ridimensionare il data warehouse facilmente senza doversi preoccupare dei componenti hardware o software sottostanti.

- È possibile stimare i miglioramenti delle prestazioni per un livello di DWU prima di modificare le dimensioni del data warehouse.

- Modificare o spostare i componenti hardware e software sottostanti dell'istanza senza compromettere le prestazioni del carico di lavoro.

- Per Microsoft, apportare modifiche all'architettura sottostante del servizio senza influire sulle prestazioni del carico di lavoro.

- Per Microsoft, migliorare rapidamente le prestazioni in SQL Data Warehouse, in modo scalabile e con un impatto uniforme sul sistema.

Le Unità Data Warehouse offrono una misura di tre metriche precise strettamente correlate alle prestazioni del carico di lavoro di data warehousing. L'obiettivo è che queste metriche chiave del carico di lavoro vengano ridimensionate in modo lineare con le DWU scelte per il data warehouse.

**Analisi/aggregazione:** questa metrica del carico di lavoro accetta una query di data warehousing query standard che esegue l'analisi di un numero elevato di righe e quindi esegue un'aggregazione complessa. Questa è un'operazione con uso intensivo di I/O e CPU.

**Carico:** questa metrica consente di inserire dati nel servizio. I carichi vengono completati con PolyBase che carica un set di dati rappresentativo dall'archivio BLOB di Azure. Questa metrica è progettata per evidenziare gli aspetti relativi a rete e CPU del servizio.

**Create Table As Select (CTAS):** misura la possibilità di creare una copia di una tabella. Ciò comporta la lettura dei dati dalla risorsa di archiviazione, la relativa distribuzione tra tutti i nodi del dispositivo e la riscrittura in una risorsa di archiviazione. È un'operazione con uso intensivo di CPU, I/O e rete.

## Sospendere e ridimensionare su richiesta

Quando sono necessari risultati più rapidi, aumentare le DWUs e pagare il costo di prestazioni più elevate. Quando è richiesta una minore quantità di potenza di calcolo, diminuire le DWU e pagare solo per le prestazioni necessarie Si può pensare di modificare le DWU in questi scenari:

- Quando è necessario eseguire query, ad esempio la sera o nei fine settimana, disattivare le query. Sospendere quindi le risorse di calcolo per evitare di pagare per le DWU quando non sono necessarie.

- Quando la richiesta per il sistema è bassa, considerare la possibilità di ridurre le dimensioni della DWU. È comunque possibile accedere ai dati, ma con una significativa riduzione dei costi.

- Quando si esegue un'operazione di caricamento o trasformazione di quantità di dati elevate, è possibile aumentare le prestazioni in modo che i dati siano disponibili più rapidamente.

Per comprendere il valore di DWU ideale, provare ad aumentarlo e diminuirlo ed eseguire alcune query dopo il caricamento dei dati. Poiché il ridimensionamento è rapido, è possibile provare diversi livelli di prestazioni in un'ora o meno. Tenere presente che SQL Data Warehouse è progettato per elaborare grandi quantità di dati e per vedere le reali funzionalità per la scalabilità, soprattutto nelle implementazioni su vasta scala offerte da Microsoft, è consigliabile usare un set di dati di grandi dimensioni di circa 1 TB o superiore.


## Basato su SQL Server

SQL Data Warehouse si basa sul motore di database relazionale di SQL Server e include molte delle funzionalità che ci si aspetta da un data warehouse aziendale. Se già si conosce T-SQL, sarà facile trasferire le proprie conoscenze in SQL Data Warehouse. Per gli utenti avanzati e per chi non ha molta familiarità, gli esempi disponibili nella documentazione saranno utili per iniziare. In generale, è possibile considerare il modo in cui sono costruiti gli elementi del linguaggio di SQL Data Warehouse come segue:

- SQL Data Warehouse usa la sintassi T-SQL per molte operazioni. Supporta anche un'ampia gamma di costrutti SQL tradizionali, ad esempio stored procedure, funzioni definite dall'utente, partizionamento delle tabelle, indici e regole di confronto.

- SQL Data Warehouse include anche una serie di funzionalità recenti di SQL Server, tra cui gli indici **columnstore** cluster, l'integrazione di PolyBase e il controllo dei dati, inclusa la valutazione delle minacce.

- Alcuni elementi del linguaggio T-SQL meno comuni per i carichi di lavoro di data warehousing o più recenti in SQL Server potrebbero non essere disponibili al momento. Per altre informazioni, vedere la [documentazione sulla migrazione][].

Grazie a Transact-SQL e alle funzionalità in comune tra SQL Server, SQL Data Warehouse, il database SQL e Analytics Platform System, è possibile sviluppare una soluzione adatta alle esigenze dei dati di cui si dispone. È possibile decidere dove mantenere i dati in base ai requisiti di prestazioni, sicurezza e scalabilità e quindi trasferirli secondo le necessità tra i diversi sistemi.

## Protezione dati

SQL Data Warehouse archivia tutti i dati nella risorsa di archiviazione con ridondanza locale Premium di Azure. Nel data center locale vengono mantenute più copie sincrone dei dati per garantire una protezione trasparente degli stessi in caso di problemi localizzati. SQL Data Warehouse esegue anche il backup automatico dei database attivi (non in pausa) a intervalli regolari usando gli snapshot di Archiviazione di Azure. Per altre informazioni sul funzionamento del backup e ripristino, vedere la [panoramica su backup e ripristino][].

## Integrato con strumenti di Microsoft

SQL Data Warehouse si integra anche con molti degli strumenti di SQL Server con cui gli utenti possono avere familiarità, inclusi i seguenti:

**Strumenti tradizionali di SQL Server:** SQL Data Warehouse è completamente integrato con SQL Server Analysis Services, Integration Services e Reporting Services.

**Strumenti basati sul cloud:** SQL Data Warehouse può essere usato insieme a numerosi nuovi strumenti di Azure, tra cui Data factory, Analisi di flusso, Machine Learning e Power BI. Per un elenco più completo, vedere la [panoramica degli strumenti integrati][].

**Strumenti di terze parti:** numerosi provider di strumenti di terze parti hanno certificato l'integrazione dei propri strumenti con SQL Data Warehouse. Per l'elenco completo, vedere [Partner di soluzioni per SQL Data Warehouse][].

## Scenari di origini dati ibride

L'uso di SQL Data Warehouse con PolyBase offre agli utenti funzionalità senza precedenti per spostare dati nel proprio ecosistema, consentendo di configurare scenari ibridi avanzati con origini dati locali e non relazionali.

Polybase consente di sfruttare i dati provenienti da origini diverse eseguendo i comandi T-SQL familiari. Polybase consente di eseguire query su dati non relazionali presenti nell'archivio BLOB di Azure come se si trattasse di una normale tabella. Usare Polybase per eseguire query su dati non relazionali o per importare dati non relazionali in SQL Data Warehouse.

- PolyBase usa tabelle esterne per accedere ai dati non relazionali. Le definizioni di tabella vengono archiviate in SQL Data Warehouse e sono accessibili a SQL e agli strumenti come lo sono i normali dati relazionali.

- Polybase è agnostico nella relativa integrazione, quindi espone le stesse caratteristiche e funzionalità a tutte le origini supportate. I dati letti da Polybase possono essere in vari formati, inclusi i file delimitati o i file ORC.

- È possibile usare PolyBase per accedere all'archivio BLOB che viene usato anche come risorsa di archiviazione per un cluster HDInsight. Ciò consente di accedere agli stessi dati con strumenti relazionali e non relazionali.

## Passaggi successivi

Dopo aver appreso alcune informazioni su SQL Data warehouse, vedere come [Creare un Azure SQL Data Warehouse][] rapidamente e [Caricare i dati di esempio in SQL Data Warehouse][]. Se non si ha familiarità con Azure, il [glossario di Azure][] può essere utile quando si incontrano termini nuovi. In alternativa, vedere alcune delle risorse seguenti per SQL Data Warehouse.

- [Casi di successo dei clienti]
- [Blog]
- [Richieste di funzionalità]
- [Video]
- [Blog Customer Advisory Team]
- [Creare un ticket di supporto]
- [Forum MSDN]
- [Forum su Stack Overflow]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Creare un ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Caricare i dati di esempio in SQL Data Warehouse]: ./sql-data-warehouse-load-sample-databases.md
[Creare un Azure SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[documentazione sulla migrazione]: ./sql-data-warehouse-overview-migrate.md
[Partner di soluzioni per SQL Data Warehouse]: ./sql-data-warehouse-partner-business-intelligence.md
[panoramica degli strumenti integrati]: ./sql-data-warehouse-overview-integrate.md
[panoramica su backup e ripristino]: ./sql-data-warehouse-restore-database-overview.md
[glossario di Azure]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Casi di successo dei clienti]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog Customer Advisory Team]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=AzureSQLDataWarehouse
[Forum su Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0928_2016-->