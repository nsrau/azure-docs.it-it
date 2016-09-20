<properties
    pageTitle="Creazione di database cloud scalabili | Microsoft Azure"
    description="Creare applicazioni di database .NET scalabili con la libreria client di database elastici"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove"/>  

# Creazione di database cloud scalabili

La scalabilità orizzontale dei database può essere ottenuta facilmente con gli strumenti e le funzionalità scalabili per il database SQL di Azure. In particolare, è possibile usare la **libreria client dei database elastici** per creare e gestire i database con scalabilità orizzontale. Questa funzionalità consente di sviluppare con facilità applicazioni partizionate usando centinaia o anche migliaia di database SQL Azure. I [processi elastici](sql-database-elastic-jobs-powershell.md) possono quindi essere usati per facilitare la gestione di questi database.

Per installare la libreria, visitare [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## Documentazione
1. [Iniziare a utilizzare gli strumenti di database elastici](sql-database-elastic-scale-get-started.md)
* [Funzionalità di database elastico](sql-database-elastic-scale-introduction.md)
* [Gestione mappe partizioni](sql-database-elastic-scale-shard-map-management.md)
* [Migrate existing databases to scale-out (Eseguire la migrazione di database esistenti per la scalabilità orizzontale)](sql-database-elastic-convert-to-use-elastic-tools.md)
* [Routing dipendente dei dati](sql-database-elastic-scale-data-dependent-routing.md)
* [Query su più partizioni](sql-database-elastic-scale-multishard-querying.md)
* [Aggiunta di una partizione utilizzando gli strumenti di database elastici](sql-database-elastic-scale-add-a-shard.md)
* [Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga](sql-database-elastic-tools-multi-tenant-row-level-security.md)
* [Aggiornare le app della libreria client](sql-database-elastic-scale-upgrade-client-library.md)
* [Panoramica sulle query di database elastico](sql-database-elastic-query-overview.md)
* [Glossario sugli strumenti di database elastici](sql-database-elastic-scale-glossary.md)
* [Libreria client dei database elastici con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
* [Uso della libreria client dei database elastici con Dapper](sql-database-elastic-scale-working-with-dapper.md)
* [Strumento di divisione-unione](sql-database-elastic-scale-overview-split-and-merge.md)
* [Contatori delle prestazioni per Gestore mappe partizioni](sql-database-elastic-database-client-library.md)
* [Domande frequenti sugli strumenti di database elastici](sql-database-elastic-scale-faq.md)

## Funzionalità client

La gestione delle applicazioni con scalabilità orizzontale mediante il *partizionamento orizzontale* presenta sfide sia per gli sviluppatori che per gli amministratori. La libreria client semplifica le attività di gestione, fornendo strumenti che consentono sia agli sviluppatori che agli amministratori di gestire più agevolmente i database con scalabilità orizzontale. In un esempio tipico, è necessario gestire molti database, detti anche "partizioni". I clienti si trovano nello stesso database ed è disponibile un database per cliente (modello single-tenant). La libreria client include le seguenti funzionalità:

1.  **Gestione mappe partizioni**: viene creato un database speciale denominato "gestore mappe partizioni". La gestione delle mappe partizioni è la possibilità per un'applicazione di gestire metadati nelle proprie partizioni. Gli sviluppatori possono usare questa funzionalità per registrare i database come partizioni, descrivere i mapping di singole chiavi di partizionamento orizzontale o di intervalli di chiavi per i database, nonché gestire i metadati man mano che il numero e la composizione dei database si evolve, per rispecchiare le modifiche apportate alla capacità. Senza la libreria client dei database elastici, è necessario dedicare molto tempo alla scrittura di codice di gestione durante l'implementazione del partizionamento orizzontale. Per informazioni dettagliate, vedere [Gestione mappe partizioni](sql-database-elastic-scale-shard-map-management.md).

* **Routing dipendente dai dati**: immaginare una richiesta in arrivo nell'applicazione. L'applicazione individua il database corretto in base al valore della chiave di partizionamento orizzontale della richiesta. Quindi l'applicazione apre una connessione al database per elaborare la richiesta. Il routing dipendente dai dati consente di aprire connessioni con una singola e semplice chiamata alla mappa partizioni dell'applicazione. Il routing dipendente dai dati è un'altra area del codice dell'infrastruttura ora coperta dalle funzionalità della libreria client dei database elastici. Per informazioni dettagliate, vedere [Routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md).

* **Query su più partizioni**: l'esecuzione di query su più partizioni opera quando una richiesta include più partizioni o tutte le partizioni. Una query su più partizioni esegue lo stesso codice T-SQL in tutte le partizioni o in un set di partizioni. I risultati restituiti dalle partizioni coinvolte vengono uniti in un set di risultati complessivi mediante la semantica di UNION ALL. La funzionalità come viene esposta tramite la libreria client gestisce numerose attività, tra cui gestione delle connessioni, gestione dei thread, gestione degli errori ed elaborazione dei risultati intermedi e consente di eseguire query su centinaia di partizioni. Per informazioni dettagliate, vedere [Esecuzione di query su più partizioni](sql-database-elastic-scale-multishard-querying.md).

In generale, i clienti che utilizzano gli strumenti dei database elastici quando inviano operazioni locali della partizione anziché operazioni tra più partizioni con la propria semantica, possono aspettarsi di ottenere funzionalità T-SQL complete.

## Passaggi successivi

Provare l’[app di esempio](sql-database-elastic-scale-get-started.md)che illustra le funzioni client.

Per installare la libreria, andare su [Libreria client di database elastici](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Per istruzioni sull'uso dello strumento di suddivisione-unione, vedere [Panoramica sullo strumento di suddivisione-unione](sql-database-elastic-scale-overview-split-and-merge.md).

[La libreria client dei database elastici è ora open source!](https://azure.microsoft.com/blog/elastic-database-client-library-is-now-open-sourced/)

Usare le [query del database elastico](sql-database-elastic-query-overview.md).

La libreria è disponibile come software open source in [GitHub](https://github.com/Azure/elastic-db-tools).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->  
<!--Image references-->  
[1]: ./media/sql-database-elastic-database-client-library/glossary.png

<!---HONumber=AcomDC_0907_2016-->