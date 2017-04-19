---
title: "Che cos&quot;è SQL Data Warehouse di Azure? | Documentazione Microsoft"
description: "Database distribuito di livello aziendale, in grado di elaborare volumi di dati relazionali e non relazionali anche nell&quot;ordine di petabyte. Si tratta del primo data warehouse basato sul cloud del settore con possibilità di aumento, riduzione e sospensione in pochi secondi."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 2/28/2017
ms.author: jrj;mausher;kevin;barbkess;elbutter
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: fd35b0e5c57e47c5e79fe926f24fb21089cbf89a
ms.lasthandoff: 04/18/2017


---
# <a name="what-is-azure-sql-data-warehouse"></a>Che cos'è SQL Data Warehouse di Azure?
Azure SQL Data Warehouse è un database relazionale MPP (Massively Parallel Processing) basato sul cloud, con possibilità di aumentare il numero di istanze, in grado di elaborare volumi massivi di dati. 

SQL Data Warehouse:

* Combina il database relazionale di SQL Server con le funzionalità di aumento del numero di istanze del cloud di Azure. 
* Separa l'archiviazione dal calcolo.
* Consente l'aumento, la riduzione, la sospensione o la ripresa del calcolo. 
* È integrabile nella piattaforma Azure.
* Usa Transact-SQL (T-SQL) e gli strumenti di SQL Server.
* È conforme a vari requisiti di sicurezza legali e aziendali, ad esempio SOC e ISO.

Questo articolo illustra le funzionalità principali di SQL Data Warehouse.

## <a name="massively-parallel-processing-architecture"></a>Architettura di elaborazione parallela massiva (MPP)
SQL Data Warehouse è un sistema di database distribuito a elaborazione parallela massiva. In background SQL Data Warehouse distribuisce i dati in risorse di archiviazione in modalità di condivisione "shared-nothing" e in unità di elaborazione diversi. I dati vengono archiviati in un livello di archiviazione con ridondanza locale Premium, sopra il quale i nodi di calcolo collegati in modo dinamico eseguono le query. SQL Data Warehouse adotta un approccio di tipo "dividi e conquista" per l'esecuzione di carichi e query complesse. Le richieste vengono ricevute dal nodo di controllo, ottimizzate per la distribuzione e quindi passate ai nodi di calcolo per svolgere le attività in parallelo.

Grazie alla separazione tra archiviazione e calcolo, SQL Data Warehouse consente di:

* Aumentare o ridurre le dimensioni dello spazio di archiviazione indipendentemente dal calcolo.
* Aumentare o ridurre la potenza senza spostare i dati.
* Sospendere la capacità di calcolo mantenendo intatti i dati e pagando solo per l'archiviazione.
* Riprendere le capacità di calcolo durante l'orario operativo.

Il diagramma seguente illustra l'architettura in modo più dettagliato.

![Architettura di SQL Data Warehouse][1]

**Nodo di controllo:** il nodo di controllo gestisce e ottimizza le query. È il front-end che interagisce con tutte le applicazioni e le connessioni. In SQL Data Warehouse il nodo di controllo si basa sul database SQL e quando si stabilisce la connessione ha lo stesso aspetto. In background, il nodo di controllo coordina lo spostamento e il calcolo di tutti i dati e i calcoli richiesti per l'esecuzione di query parallele sui dati distribuiti. Quando si invia una query T-SQL a SQL Data Warehouse, il nodo di controllo la trasforma in query distinte che saranno eseguite su ogni nodo di calcolo in parallelo.

**Nodi di calcolo:** offrono le potenzialità necessarie per SQL Data Warehouse. Sono database SQL che archiviano i dati ed elaborano le query. Quando si aggiungono dati, SQL Data Warehouse distribuisce le righe ai nodi di calcolo. I nodi di calcolo sono i ruoli di lavoro che eseguono le query parallele sui dati. Dopo l'elaborazione, restituiscono i risultati al nodo del controllo. Per completare la query, il nodo controllo aggrega i risultati e restituisce il risultato finale.

**Archiviazione** : i dati vengono memorizzati nell'archivio BLOB di Azure. Quando i nodi di calcolo interagiscono con i dati, scrivono e leggono direttamente da e nell'archiviazione BLOB. Dato che l'archiviazione di Azure si espande in modo trasparente ed esteso, SQL Data Warehouse può fare altrettanto. Poiché elaborazione e archiviazione sono indipendenti, SQL Data Warehouse può ridimensionare automaticamente le risorse di archiviazione separatamente dal ridimensionamento delle risorse di calcolo e viceversa. Anche l'archivio BLOB di Azure è completamente a tolleranza di errore e semplifica il processo di backup e ripristino.

**Data Movement Service:** (DMS) sposta i dati tra i nodi. DMS fornisce ai nodi di calcolo l'accesso ai dati necessario per eseguire join e aggregazioni. DMS non è un servizio di Azure. È un servizio di Windows che viene eseguito insieme al database SQL in tutti i nodi. DMS è un processo in background con il quale non è possibile interagire direttamente. È comunque possibile esaminare i piani di query per scoprire quando vengono eseguite le operazioni DMS, perché è necessario lo spostamento dei dati per eseguire ogni query in parallelo.

## <a name="optimized-for-data-warehouse-workloads"></a>Ottimizzato per carichi di lavoro di data warehouse
L'approccio MPP è supportato da varie ottimizzazioni delle prestazioni specifiche per il data warehousing, tra le quali:

* Un'utilità di ottimizzazione delle query distribuita e un set di statistiche complesse su tutti i dati. Usando le informazioni sulla distribuzione e sulle dimensioni dei dati, il servizio è in grado di ottimizzare le query in base a una valutazione del costo di operazioni di query distribuite specifiche.
* Tecniche e algoritmi avanzati integrati nel processo di spostamento dei dati per spostare in modo efficiente i dati tra le diverse risorse di calcolo, secondo le esigenze, per eseguire la query. Queste operazioni di spostamento dei dati sono predefinite e tutte le ottimizzazioni di Data Movement Service vengono eseguite automaticamente.
* Indici **columnstore** cluster per impostazione predefinita. Usando una risorsa di archiviazione basata su colonne, SQL Data Warehouse raggiunge una compressione cinque volte superiore all'archiviazione tradizionale basata su righe e prestazioni delle query fino a dieci volte o maggiori. Le query di analisi che devono eseguire l'analisi di un numero elevato di righe offrono prestazioni migliori con indici columnstore.


## <a name="predictable-and-scalable-performance-with-data-warehouse-units"></a>Prestazioni prevedibili e scalabili con Unità Data Warehouse (DWU)
SQL Data Warehouse è realizzato con tecnologie simili a quelle usate per Database SQL e questo significa che gli utenti possono aspettarsi prestazioni coerenti e prevedibili per le query analitiche. Gli utenti possono aspettarsi un incremento o una riduzione lineare delle prestazioni con l'aggiunta o la rimozione di nodi di calcolo. L'allocazione di risorse per SQL Data Warehouse viene misurata in Unità Data Warehouse (DWU). Le DWU sono una misura delle risorse sottostanti, ad esempio CPU, memoria, operazioni di I/O al secondo, allocate a SQL Data Warehouse. Se si aumenta il numero di DWU, si aumentano anche le risorse e le prestazioni. In particolare, si usano le DWU per poter eseguire queste operazioni:

* Ridimensionare il data warehouse senza doversi preoccupare dei componenti hardware o software sottostanti.
* È possibile stimare i miglioramenti delle prestazioni per un livello di DWU prima di modificare le capacità di calcolo del data warehouse.
* Modificare o spostare i componenti hardware e software sottostanti dell'istanza senza compromettere le prestazioni del carico di lavoro.
* Per Microsoft, migliorare l'architettura sottostante del servizio senza influire sulle prestazioni del carico di lavoro.
* Per Microsoft, migliorare rapidamente le prestazioni in SQL Data Warehouse, in modo scalabile e con un impatto uniforme sul sistema.

Le Unità Data Warehouse offrono una misura di tre metriche strettamente correlate alle prestazioni del carico di lavoro di data warehousing. Il ridimensionamento delle metriche chiave del carico di lavoro seguenti è lineare in relazione alle unità DWU.

**Analisi/aggregazione:** una query di data warehousing standard che esamina un numero elevato di righe e quindi esegue un'aggregazione complessa. Questa è un'operazione con uso intensivo di I/O e CPU.

**Carico:** capacità di inserire dati nel servizio. Si ottengono prestazioni ottimali per i carichi con PolyBase da BLOB del servizio di archiviazione di Azure o Azure Data Lake. Questa metrica è progettata per evidenziare gli aspetti relativi a rete e CPU del servizio.

**Create Table As Select (CTAS):** misura la possibilità di creare una copia di una tabella. Ciò comporta la lettura dei dati dalla risorsa di archiviazione, la relativa distribuzione tra tutti i nodi dell'appliance e la riscrittura nella risorsa di archiviazione. È un'operazione con uso intensivo di CPU, I/O e rete.

## <a name="built-on-sql-server"></a>Basato su SQL Server
SQL Data Warehouse si basa sul motore di database relazionale di SQL Server e include molte delle funzionalità che ci si aspetta da un data warehouse aziendale. Se già si conosce T-SQL, sarà facile trasferire le proprie conoscenze in SQL Data Warehouse. Per gli utenti avanzati e per chi non ha molta familiarità, gli esempi disponibili nella documentazione saranno utili per iniziare. In generale, è possibile considerare il modo in cui sono costruiti gli elementi del linguaggio di SQL Data Warehouse come segue:

* SQL Data Warehouse usa la sintassi T-SQL per molte operazioni. Supporta anche un'ampia gamma di costrutti SQL tradizionali, ad esempio stored procedure, funzioni definite dall'utente, partizionamento delle tabelle, indici e regole di confronto.
* SQL Data Warehouse include anche varie funzionalità recenti di SQL Server, tra cui gli indici **columnstore** cluster, l'integrazione di PolyBase e il controllo dei dati, inclusa la valutazione delle minacce.
* Alcuni elementi del linguaggio T-SQL meno comuni per i carichi di lavoro di data warehousing o più recenti in SQL Server potrebbero non essere disponibili al momento. Per altre informazioni, vedere la [documentazione sulla migrazione][Migration documentation].

Grazie a Transact-SQL e alle funzionalità in comune tra SQL Server, SQL Data Warehouse, il database SQL e Analytics Platform System, è possibile sviluppare una soluzione adatta alle esigenze dei dati di cui si dispone. È possibile decidere dove mantenere i dati in base ai requisiti di prestazioni, sicurezza e scalabilità e quindi trasferirli secondo le necessità tra i diversi sistemi.

## <a name="data-protection"></a>Protezione dati
SQL Data Warehouse archivia tutti i dati nella risorsa di archiviazione con ridondanza locale Premium di Azure. Nel data center locale vengono mantenute più copie sincrone dei dati per garantire una protezione trasparente degli stessi da problemi localizzati. SQL Data Warehouse esegue anche il backup automatico dei database attivi (non in pausa) a intervalli regolari usando gli snapshot di Archiviazione di Azure. Per altre informazioni sul funzionamento del backup e ripristino, vedere la [panoramica su backup e ripristino][Backup and restore overview].

## <a name="integrated-with-microsoft-tools"></a>Integrato con strumenti di Microsoft
SQL Data Warehouse si integra anche con molti degli strumenti di SQL Server con cui gli utenti possono avere familiarità, Questi strumenti comprendono:

**Strumenti tradizionali di SQL Server:** SQL Data Warehouse è completamente integrato con SQL Server Analysis Services, Integration Services e Reporting Services.

**Strumenti basati sul cloud:** SQL Data Warehouse può essere integrato con vari servizi in Azure, tra cui Data factory, Analisi di flusso, Machine Learning e Power BI. Per un elenco più completo, vedere la [panoramica degli strumenti integrati][Integrated tools overview].

**Strumenti di terze parti:** molti provider di strumenti di terze parti hanno certificato l'integrazione dei propri strumenti con SQL Data Warehouse. Per l'elenco completo, vedere [Partner di soluzioni per SQL Data Warehouse][SQL Data Warehouse solution partners].

## <a name="hybrid-data-sources-scenarios"></a>Scenari di origini dati ibride
Polybase consente di sfruttare i dati provenienti da origini diverse eseguendo i comandi T-SQL familiari. Polybase consente di eseguire query su dati non relazionali presenti nell'archivio BLOB di Azure come se si trattasse di una normale tabella. Usare Polybase per eseguire query su dati non relazionali o per importare dati non relazionali in SQL Data Warehouse.

* PolyBase usa tabelle esterne per accedere ai dati non relazionali. Le definizioni di tabella vengono archiviate in SQL Data Warehouse e sono accessibili a SQL e agli strumenti come lo sono i normali dati relazionali.
* Polybase è agnostico nella relativa integrazione, quindi espone le stesse caratteristiche e funzionalità a tutte le origini supportate. I dati letti da Polybase possono essere in vari formati, inclusi file delimitati o file ORC.
* È possibile usare PolyBase per accedere all'archivio BLOB che viene usato anche come risorsa di archiviazione per un cluster HDInsight. Ciò consente di accedere agli stessi dati con strumenti relazionali e non relazionali.

## <a name="sla"></a>Contratto di servizio
SQL Data Warehouse offre un contratto di servizio a livello di prodotto come parte del Contratto di servizio di Microsoft Online Services. Per altre informazioni, vedere [Contratto di servizio per SQL Data Warehouse][SLA for SQL Data Warehouse]. Per informazioni sul contratto di servizio di tutti gli altri prodotti, visitare la pagina di Azure [Contratti di servizio] o eseguirne il download nella pagina [Contratti multilicenza][Volume Licensing]. 

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso alcune informazioni su SQL Data Warehouse, vedere come [creare un SQL Data Warehouse][create a SQL Data Warehouse] rapidamente e [caricare i dati di esempio][load sample data]. Se non si ha familiarità con Azure, il [glossario di Azure][Azure glossary] può essere utile quando si incontrano termini nuovi. Oppure vedere alcune delle altre risorse disponibili per SQL Data Warehouse.  

* [Casi di successo dei clienti]
* [Blog]
* [Richieste di funzionalità]
* [Video]
* [Blog Customer Advisory Team]
* [Creare un ticket di supporto]
* [Forum MSDN]
* [Forum su Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Creare un ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Casi di successo dei clienti]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog Customer Advisory Team]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Forum su Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Contratti di servizio]: https://azure.microsoft.com/en-us/support/legal/sla/

