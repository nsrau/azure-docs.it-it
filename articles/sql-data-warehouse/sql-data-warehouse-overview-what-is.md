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
   ms.date="06/07/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# Che cos'è SQL Data Warehouse di Azure?

Azure SQL Data Warehouse è un database basato sul cloud, con possibilità di aumentare il numero di istanze, che può elaborare volumi massivi di dati sia relazionali che non relazionali. Basato sull'architettura di elaborazione parallela massiva (MPP, Massively Parallel Processing), SQL Data Warehouse può gestire il carico di lavoro dell'organizzazione.

SQL Data Warehouse:

- Combina il database relazionale di SQL Server con le funzionalità di aumento del numero di istanze del cloud di Azure. È possibile aumentare, ridurre, sospendere o riprendere il calcolo in pochi secondi. È possibile ridurre i costi aumentando l'uso della CPU quando occorre e riducendo l'utilizzo negli orari non di punta.
- Sfrutta la piattaforma Azure. È facile da distribuire, può essere mantenuto senza problemi ed è completamente a tolleranza di errore grazie ai backup automatici.
- È complementare all'ecosistema di SQL Server. È possibile sviluppare con il familiare linguaggio Transact-SQL (T-SQL) e gli strumenti di SQL Server.

Questo articolo illustra le funzionalità principali di SQL Data Warehouse.

## Architettura di elaborazione parallela massiva (MPP)

SQL Data Warehouse usa l'architettura di elaborazione parallela massiva (MPP) di Microsoft progettata per eseguire alcuni dei più grandi data warehouse locali del mondo.

L'architettura MPP distribuisce attualmente i dati tra 60 unità di elaborazione e di archiviazione in modalità di condivisione "shared-nothing". I dati vengono memorizzati nella risorsa di archiviazione con ridondanza locale Premium e collegati ai nodi di calcolo per l'esecuzione di query. Con questa architettura, SQL Data Warehouse adotta un approccio di tipo "dividi e conquista" per l'esecuzione di query T-SQL complesse. Durante l'elaborazione, il nodo di controllo ottimizza ogni query e quindi passa il lavoro ai nodi di calcolo, ognuno dei quali esegue la relativa parte dei dati in parallelo.

Combinando l'architettura MPP e le funzionalità di archiviazione di Azure, SQL Data Warehouse può:

- Aumentare o ridurre lo spazio di archiviazione indipendente dal calcolo.
- Aumentare o ridurre il calcolo senza spostare i dati.
- Sospendere la capacità di calcolo mantenendo intatti i dati.
- Riprendere la capacità di calcolo con pochissimo preavviso.

Il diagramma seguente illustra l'architettura in modo più dettagliato.

![Architettura di SQL Data Warehouse][1]


- **Nodo di controllo:** il nodo di controllo gestisce e ottimizza le query. È il front-end che interagisce con tutte le applicazioni e le connessioni. In SQL Data Warehouse il nodo di controllo si basa sul database SQL e quando si stabilisce la connessione ha lo stesso aspetto. In background il nodo di controllo coordina lo spostamento e il calcolo di tutti i dati e i calcoli richiesti per l'esecuzione di query parallele sui dati distribuiti. Quando si invia una query T-SQL a SQL Data Warehouse, il nodo di controllo la trasforma in query distinte che saranno eseguite su ogni nodo di calcolo in parallelo.

- **Nodi di calcolo:** offrono le potenzialità necessarie per SQL Data Warehouse. Sono database SQL che archiviano i dati ed elaborano le query. Quando si aggiungono dati, SQL Data Warehouse distribuisce le righe ai nodi di calcolo. I nodi di calcolo sono i ruoli di lavoro che eseguono le query parallele sui dati. Dopo l'elaborazione, restituiscono i risultati al nodo del controllo. Per completare la query, il nodo controllo aggrega i risultati e restituisce il risultato finale.

- **Archiviazione**: i dati vengono memorizzati nell'archivio BLOB di Azure. Quando i nodi di calcolo interagiscono con i dati, scrivono e leggono direttamente da e nell'archiviazione BLOB. Poiché l'archiviazione di Azure si espande in modo trasparente e senza limiti, SQL Data Warehouse può fare la stessa cosa. Poiché elaborazione e archiviazione sono indipendenti, SQL Data Warehouse può ridimensionare automaticamente le risorse di archiviazione separatamente dal ridimensionamento delle risorse di calcolo e viceversa. Anche l'archivio BLOB di Azure è completamente a tolleranza di errore e semplifica il processo di backup e ripristino.

- **Data Movement Service:** Data Movement Service (DMS) sposta i dati tra i nodi. DMS fornisce ai nodi di calcolo l'accesso ai dati necessario per eseguire join e aggregazioni. DMS non è un servizio di Azure. È un servizio di Windows che viene eseguito insieme al database SQL in tutti i nodi. Poiché DMS viene eseguito in background, l'utente non interagisce direttamente con esso. Quando tuttavia si esaminano i piani di query, si noterà che includono alcune operazioni DMS perché lo spostamento dei dati è necessario per eseguire ogni query in parallelo.


## Prestazioni delle query ottimizzate

L'approccio MPP è supportato da numerose ottimizzazioni delle prestazioni specifiche per il data warehousing:

- Un'utilità di ottimizzazione delle query distribuita e un set di statistiche complesse su tutti i dati. Usando le informazioni sulla distribuzione e sulle dimensioni dei dati, il servizio è in grado di ottimizzare le query in base a una valutazione del costo di operazioni di query distribuite specifiche.

- Tecniche e algoritmi avanzati integrati nel processo di spostamento dei dati per spostare in modo efficiente i dati tra le diverse risorse di calcolo, secondo le esigenze, per eseguire la query. Queste operazioni di spostamento dei dati sono integrate e tutte le ottimizzazioni di Data Movement Service vengono eseguite automaticamente.

- Indici **columnstore** cluster per impostazione predefinita. Usando un'archiviazione basata su colonne, SQL Data Warehouse raggiunge una compressione cinque volte superiore all'archiviazione tradizionale basata su righe e prestazioni delle query fino a dieci volte maggiori. Query di analisi che devono eseguire l'analisi di un numero elevato di righe forniscono prestazioni eccezionali sugli indici columnstore.

## Archiviazione e calcolo scalabili

L'architettura di SQL Data Warehouse introduce risorse di archiviazione e calcolo separate in grado di permettere il ridimensionamento di ogni archiviazione in mondo indipendente. La struttura di distribuzione semplice e rapida del database SQL consente di avere a disposizione risorse di calcolo aggiuntive con pochissimo preavviso. L'uso dell'archivio BLOB di Azure è complementare a queste funzionalità. I BLOB offrono non solo un'archiviazione stabile e replicata, ma anche l'infrastruttura per un'espansione semplice e a basso costo. Usando questa combinazione di archiviazione con scalabilità cloud e funzionalità di calcolo di Azure, SQL Data Warehouse consente di pagare per un'archiviazione con le prestazioni di query necessarie, solo quando è necessario. La modifica della quantità di risorse di calcolo è semplice, basta spostare a sinistra o a destra un dispositivo di scorrimento nel portale di Azure, ma può anche essere pianificata con T-SQL e PowerShell.

Oltre alla possibilità di controllare completamente la quantità del calcolo in modo indipendente dall'archiviazione, SQL Data Warehouse consente di sospendere completamente il data warehouse. Conservando lo spazio di archiviazione, tutto il calcolo viene rilasciato nel pool principale di Azure, riducendo immediatamente i costi. Quando è necessario, basta riprendere semplicemente il calcolo e i dati e il calcolo saranno disponibili per il carico di lavoro.

## Unità Data Warehouse

L'utilizzo del calcolo in SQL Data Warehouse viene misurato in Unità Data Warehouse (DWU). Le DWU sono una misura della potenza sottostante del data warehouse e sono progettate per assicurarsi di avere una quantità di prestazioni standard associata al warehouse in qualsiasi momento. In particolare, si usano le DWU per poter eseguire queste operazioni:

- Ridimensionare il data warehouse facilmente senza doversi preoccupare dei componenti hardware o software sottostanti.

- Stimare i miglioramenti delle prestazioni per un livello di DWU prima di modificare le dimensioni del data warehouse.

- Modificare o spostare i componenti hardware e software sottostanti dell'istanza senza compromettere le prestazioni del carico di lavoro.

- Per Microsoft, apportare modifiche all'architettura sottostante del servizio senza influire sulle prestazioni del carico di lavoro.

- Per Microsoft, migliorare rapidamente le prestazioni in SQL Data Warehouse, avendo la certezza che ciò avvenga in modo scalabile e con un impatto uniforme sul sistema.

Le Unità Data Warehouse offrono nello specifico una misura di tre metriche precise strettamente correlate alle prestazioni del carico di lavoro di data warehousing. L'obiettivo di Microsoft è che, per la disponibilità generale, queste metriche chiave del carico di lavoro vengano ridimensionate in modo lineare con le DWU scelte per il data warehouse.

**Analisi/aggregazione:** questa metrica del carico di lavoro accetta una query di data warehousing query standard che esegue l'analisi di un numero elevato di righe e quindi esegue un'aggregazione complessa. Questa è un'operazione con uso intensivo di I/O e CPU.

**Carico:** questa metrica consente di inserire dati nel servizio. I carichi vengono completati con PolyBase che carica un set di dati rappresentativo dall'archivio BLOB di Azure. Questa metrica è progettata per evidenziare gli aspetti relativi a rete e CPU del servizio.

**Create Table As Select (CTAS):** CTAS misura la possibilità di creare una copia di una tabella. Ciò comporta la lettura dei dati dall'archiviazione, la relativa distribuzione tra tutti i nodi del dispositivo e la riscrittura in un'archiviazione. È un'operazione con uso intensivo della CPU e della rete.

## Sospendere e ridimensionare su richiesta

Quando sono necessari risultati più rapidi, aumentare le DWUs e pagare il costo di prestazioni più elevate. Quando è richiesta una minore quantità di potenza di calcolo, diminuire le DWU e pagare solo per le prestazioni necessarie Si può pensare di modificare le DWU in questi scenari:

- Quando è necessario eseguire query, ad esempio la sera o nei fine settimana, disattivare le query. Sospendere quindi le risorse di calcolo per evitare di pagare per le DWU quando non sono necessarie.

- Quando la richiesta per il sistema è bassa, considerare la possibilità di ridurre le dimensioni della DWU. È comunque possibile accedere ai dati, ma con una significativa riduzione dei costi.

- Quando si esegue un'operazione di caricamento o trasformazione di quantità di dati elevate, è possibile aumentare le prestazioni in modo che i dati siano disponibili più rapidamente.

- Per comprendere il valore di DWU ideale, provare ad aumentarlo e diminuirlo ed eseguire alcune query dopo il caricamento dei dati. Poiché il ridimensionamento è rapido, è possibile provare diversi livelli di prestazioni in un'ora o meno.

> [AZURE.NOTE] A causa dell'architettura di SQL Data Warehouse, il ridimensionamento delle prestazioni previsto potrebbe non essere evidente con volumi di dati inferiori. Iniziare con volumi di dati minimi di 1 TB per ottenere indicazioni effettive dei vantaggi per le prestazioni.

## Basato su SQL Server

SQL Data Warehouse si basa sul motore di database relazionale di SQL Server e include molte delle funzionalità che ci si aspetta da un data warehouse aziendale. Se già si conosce T-SQL, sarà facile trasferire le proprie conoscenze in SQL Data Warehouse. Per gli utenti avanzati e per chi non ha molta familiarità, gli esempi disponibili nella documentazione saranno utili per iniziare. In generale, è possibile considerare il modo in cui sono costruiti gli elementi del linguaggio di SQL Data Warehouse come segue:

- SQL Data Warehouse usa la sintassi T-SQL per molte operazioni. Supporta anche un'ampia gamma di costrutti SQL tradizionali, ad esempio stored procedure, funzioni definite dall'utente, partizionamento delle tabelle, indici e regole di confronto.

- SQL Data Warehouse include anche una serie di funzionalità recenti di SQL Server, inclusi gli indici **columnstore** cluster, l'integrazione di PolyBase e il controllo dei dati, tra cui la valutazione delle minacce.

- Alcuni elementi del linguaggio T-SQL meno comuni per i carichi di lavoro di data warehousing o più recenti in SQL Server potrebbero non essere disponibili al momento. Per altre informazioni, vedere la [documentazione sulla migrazione][].

Grazie a Transact-SQL e alle funzionalità in comune tra SQL Server, SQL Data Warehouse, il database SQL e Analytics Platform System, è possibile sviluppare una soluzione adatta alle esigenze dei dati di cui si dispone. È possibile decidere dove mantenere i dati in base ai requisiti di prestazioni, sicurezza e scalabilità e quindi trasferirli secondo le necessità tra i diversi sistemi.

## Integrato con strumenti di Microsoft

SQL Data Warehouse si integra anche con molti degli strumenti di SQL Server con cui gli utenti possono avere familiarità, inclusi i seguenti:

**Strumenti tradizionali di SQL Server:** SQL Data Warehouse è completamente integrato con SQL Server Analysis Services, Integration Services e Reporting Services.

**Strumenti basati sul cloud:** SQL Data Warehouse può essere usato insieme a numerosi nuovi strumenti di Azure, tra cui Data factory, Analisi di flusso, Machine Learning e Power BI. Per un elenco più completo, vedere [Integrated Tools Overview][] (Panoramica degli strumenti integrati).

**Strumenti di terze parti:** numerosi provider di strumenti di terze parti hanno certificato l'integrazione dei propri strumenti con SQL Data Warehouse. Vedere [Partner di soluzioni per SQL Data Warehouse][] per un elenco completo di partner.

## Scenari di origini dati ibride

L'uso di SQL Data Warehouse con PolyBase offre agli utenti capacità senza precedenti per spostare dati nel proprio ecosistema, consentendo di configurare scenari ibridi avanzati con origini dati non relazionali e locali.

Polybase consente di sfruttare i dati provenienti da origini diverse eseguendo i comandi T-SQL familiari. Polybase consente di eseguire query su dati non relazionali presenti nell'archivio BLOB di Azure come se si trattasse di una normale tabella. Usare Polybase per eseguire query su dati non relazionali o per importare dati non relazionali in SQL Data Warehouse.

- PolyBase usa tabelle esterne per accedere ai dati non relazionali. Le definizioni di tabella vengono archiviate in SQL Data Warehouse e sono accessibili a SQL e agli strumenti come lo sono i normali dati relazionali.

- Polybase è agnostico nella relativa integrazione, quindi espone le stesse caratteristiche e funzionalità a tutte le origini supportate. I dati letti da Polybase possono essere in vari formati, inclusi i file delimitati o i file ORC.

- PolyBase può essere usato per accedere all'archivio BLOB usato come risorsa di archiviazione anche per un cluster HDInsight. Ciò consente di accedere agli stessi dati con strumenti relazionali e non relazionali.

## Passaggi successivi

Dopo avere appreso alcune nozioni su SQL Data Warehouse, è possibile ottenere informazioni sul [carico di lavoro del data warehouse], su [come effettuare il provisioning] di SQL Data Warehouse e su [come caricare i dati di esempio]. In alternativa, vedere alcune delle risorse seguenti per SQL Data Warehouse.

- [Blog]
- [Richieste di funzionalità]
- [Video]
- [Blog del team CAT]
- [Creare un ticket di supporto]
- [Forum di MSDN]
- [Forum su Stack Overflow]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Creare un ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[carico di lavoro del data warehouse]: ./sql-data-warehouse-overview-workload.md
[come caricare i dati di esempio]: ./sql-data-warehouse-get-started-load-sample-databases.md
[come effettuare il provisioning]: ./sql-data-warehouse-get-started-provision.md
[documentazione sulla migrazione]: ./sql-data-warehouse-overview-migrate.md
[Partner di soluzioni per SQL Data Warehouse]: ./sql-data-warehouse-integrate-solution-partners.md
[Integrated Tools Overview]: ./sql-data-warehouse-overview-integrate.

<!--MSDN references-->

<!--Other Web references-->
[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog del team CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum di MSDN]: https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureSQLDataWarehouse
[Forum su Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0622_2016-->