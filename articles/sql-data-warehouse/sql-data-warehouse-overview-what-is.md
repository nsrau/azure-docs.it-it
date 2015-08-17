<properties
   pageTitle="Che cos'è SQL Data Warehouse | Microsoft Azure"
   description="Database distribuito di livello aziendale, disponibile nel cloud di Azure e in grado di elaborare volumi di dati relazionali e non relazionali anche nell'ordine di petabyte. Si tratta del primo data warehouse basato sul cloud del settore con possibilità di aumento, riduzione e sospensione in pochi secondi."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>


<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/23/2015"
   ms.author="barbkess;JRJ@BigBangData.co.uk;"/>


# Che cos'è SQL Data Warehouse?

SQL Data Warehouse è un database distribuito di livello aziendale, disponibile nel cloud di Azure e in grado di elaborare volumi di dati relazionali e non relazionali anche nell'ordine di petabyte. Si tratta del primo data warehouse basato sul cloud del settore con possibilità di aumento, riduzione e sospensione in pochi secondi.

SQL Data Warehouse coniuga il meglio della qualità e affidabilità dei database relazionali aziendali di SQL Server con il cloud computing. Leggere altre informazioni relative ai principali componenti di progettazione di SQL Data Warehouse.

## Aumentare, ridurre o sospendere le risorse di calcolo
Con SQL Data Warehouse, i dati vengono archiviati in un archivio con scalabilità cloud e il calcolo viene scalato in modo indipendente, consentendo di pagare i costi relativi alle prestazioni delle query quando necessario. È ora possibile distribuire, aumentare, ridurre e persino sospendere il calcolo in modo dinamico, nonché sfruttare al meglio l'archiviazione con scalabilità cloud e applicare il calcolo delle query in base alle esigenze del periodo specifico. Quando il calcolo viene sospeso, si paga soltanto l'archiviazione.

> [AZURE.NOTE]Le unità data warehouse (DWU) sono l'unità di scala per le risorse di calcolo in SQL Data Warehouse.

- Quando sono necessari risultati più rapidi, è possibile aumentare le DWU e pagare per questo numero di unità più elevato; quando tale potenza di calcolo non è necessaria, è possibile ridurre le DWU e tornare a pagare per il numero di unità più limitato. La potenza di calcolo aumenta in proporzione al numero di DWU, pertanto raddoppiando le DWU si raddoppiano le risorse di calcolo. 
- Quando non è necessario eseguire query, ad esempio la sera o nei week-end, è possibile sospendere le risorse di calcolo per annullare tutte le query in esecuzione e rimuovere tutte le DWU allocate al data warehouse. L'archivio dati resta inalterato, ma non viene prodotto alcun addebito per le risorse di calcolo. Quando è necessario iniziare a eseguire query, ad esempio il lunedì mattina, è possibile riprendere le risorse di calcolo. 

## Elaborazione parallela massiva e indici columnstore per prestazioni all'avanguardia
SQL Data Warehouse usa l'architettura di elaborazione parallela massiva (MPP) Microsoft e la tecnologia degli indici columnstore di SQL Server per offrire prestazioni all'avanguardia.

> [AZURE.NOTE]MPP si basa su un approccio di tipo "divide et impera" per risolvere i problemi di dati di grande entità mediante il calcolo parallelo. I dati vengono infatti suddivisi e distribuiti tra numerose risorse di calcolo, ognuna delle quali opera sulla propria porzione dei dati in parallelo.

- L'elemento chiave è rappresentato dalla tecnologia per query distribuite Microsoft. SQL Data Warehouse usa un Query Optimizer avanzato che determina come ottimizzare le query distribuite in base a una valutazione del costo delle operazioni di query. Prevede anche tecniche e algoritmi sofisticati in grado di spostare in modo efficiente i dati tra le diverse risorse di calcolo come necessario per eseguire la query.
- Gli indici columnstore sono fondamentali per ottenere elevate prestazioni con le query su data warehouse. Usando un'archiviazione basata sulle colonne, gli indici columnstore consentono di avere una compressione cinque volte superiore all'archiviazione tradizionale basata sulle righe e di avere prestazioni fino a dieci volte maggiori per le query. Le query del data warehouse funzionano in modo ottimale sugli indici columnstore perché analizzano spesso l'intera tabella o un'intera partizione di una tabella. Le query OLTP invece operano al meglio sugli indici ad albero binario perché eseguono la ricerca fino a righe specifiche nella tabella.


## Cloud ibrido con l'esperienza di livello aziendale di SQL Server
SQL Data Warehouse si basa sul comprovato motore di database relazionale di SQL Server e include le funzionalità che ci si aspetta da un data warehouse aziendale, tra cui stored procedure, funzioni definite dall'utente, partizionamento delle tabelle, indici e regole di confronto.

> [AZURE.NOTE]Se già si conosce Transact-SQL, sarà facile trasferire le proprie conoscenze in SQL Data Warehouse. Gli esempi della documentazione saranno utili per iniziare sia per gli utenti esperti sia per quelli alle prime armi.

- SQL Data Warehouse usa le tecnologie PolyBase, degli indici columnstore e Transact-SQL di SQL Server con l'architettura di elaborazione parallela massiva (MPP) di Analytic Platform System (APS) per creare tale esperienza unica e integrata di data warehouse come piattaforma distribuita come servizio (PaaS).  

- Grazie a Transact-SQL e alle funzionalità in comune tra SQL Server, SQL Data Warehouse, il database SQL e Analytics Platform System, è possibile sviluppare una soluzione adatta alle esigenze dei dati di cui si dispone. È possibile decidere dove mantenere i dati in base ai requisiti di prestazioni, sicurezza e scalabilità e quindi trasferirli secondo le necessità tra l'ambiente locale e il cloud.


## Query su dati relazionali e non relazionali
Polybase consente di eseguire query su dati non relazionali presenti nell'archivio BLOB di Azure o in un file system di Hadoop (HDFS) come se si trattasse di una normale tabella. Usare Polybase per eseguire query su dati non relazionali o per importare dati non relazionali in SQL Data Warehouse.

> [AZURE.NOTE]Polybase è facile da usare e consente di avvalersi dei dati provenienti da origini diverse eseguendo gli stessi comandi T-SQL che già si conoscono. Non è necessario imparare a usare HiveQL o altri linguaggi per sfruttare i vantaggi di Hadoop.

- Polybase è agnostico nella relativa integrazione, pertanto espone le stesse caratteristiche e funzionalità a tutte le distribuzioni di Hadoop supportate. I dati letti da Polybase possono essere in vari formati, inclusi i file delimitati o i file ORC.
- PolyBase usa tabelle esterne per accedere ai dati non relazionali. Le definizioni di tabella vengono archiviate in SQL Data Warehouse e i dati vengono archiviati esternamente in Hadoop o nell'archivio BLOB di Azure.


## Distribuzione sicura dell'infrastruttura senza costi di manutenzione
SQL Data Warehouse è facile da distribuire e la procedura richiede pochi secondi. Questo servizio è un'offerta completamente gestita che evita le perdite di tempo necessarie per la manutenzione e l'applicazione di patch per il software. SQL Data Warehouse dispone di backup incorporati per supportare il ripristino self-service. Il servizio infatti esegue automaticamente il backup dei dati in Archiviazione di Azure mentre acquisisce lo snapshot dei punti di ripristino del database.


## Passaggi successivi
Scoprire cosa si intende per [carico di lavoro del data warehouse]. Eseguire il[Provisioning]e caricare[dati di esempio]per iniziare.

<!--Image references-->

<!--Article references-->
[carico di lavoro del data warehouse]: ./sql-data-warehouse-overview-workload.md
[dati di esempio]: ./sql-data-warehouse-get-started-load-samples.md
[Provisioning]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=August15_HO6-->