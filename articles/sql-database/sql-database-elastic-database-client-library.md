<properties
    pageTitle="Database SQL di Azure - libreria client"
    description="Compilare app di database .NET scalabili"
    services="sql-database"
    documentationCenter=""
    manager="jeffreyg"
    authors="sidneyh"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2015"
    ms.author="sidneyh"/>

# Panoramica della libreria client dei database elastici

La **libreria client dei database elastici** aiuta a sviluppare facilmente applicazioni partizionate usando centinaia, o anche migliaia, di database SQL di Azure ospitati in Microsoft Azure. Tali progettazioni vengono comunemente usate per le applicazioni SaaS (Software as a Service), in genere architetture single-tenant, dove ogni tenant è dotato di un database. La compilazione e la gestione di un'applicazione è un obiettivo della libreria.  La libreria client è una libreria di .NET Framework che può essere installata in un progetto di applicazione tramite [Visual Studio](sql-database-elastic-scale-add-references-visual-studio.md) e [Nuget](http://go.microsoft.com/?linkid=9862605). La libreria client fa parte degli strumenti di database elastici che sono specificatamente una [funzionalità di database elastici](sql-database-elastic-scale-introduction.md).

## Funzionalità client

Le attività di sviluppo, ridimensionamento e gestione delle applicazioni con scalabilità orizzontale mediante il *partizionamento orizzontale* esaminate di seguito presentano difficoltà sia per gli sviluppatori che per gli amministratori. La libreria client semplifica le attività per entrambi i ruoli. I numeri nella figura seguente descrivono le funzionalità principali fornite dalla libreria client dei database elastici e lo strumento di suddivisione-unione. Nella figura viene illustrato un ambiente con molti database e ogni database corrisponde a una partizione. Gli strumenti semplificano lo sviluppo di applicazioni dei database SQL di Azure tramite le seguenti caratteristiche specifiche:

Per le definizioni dei termini usati in questo argomento, vedere il [Glossario degli strumenti dei database elastici](sql-database-elastic-scale-glossary.md).

![Funzionalità di scalabilità elastica][1]

1.  **Gestione mappe partizioni**: per gestire una raccolta di partizioni, viene creato un database speciale denominato "gestore mappa partizioni". la gestione delle mappe partizioni è la possibilità per un'applicazione di gestire diversi metadati relativi alle partizioni. Gli sviluppatori possono usare questa funzionalità per registrare i database come partizioni, descrivere i mapping di singole chiavi di partizionamento orizzontale o di intervalli di chiavi per i database, nonché gestire i metadati man mano che il numero e la composizione dei database si evolve, per rispecchiare le modifiche apportate alla capacità. Senza la libreria client dei database elastici, è necessario dedicare molto tempo alla scrittura di codice di gestione durante l'implementazione del partizionamento orizzontale. Per informazioni dettagliate, vedere [Gestione mappe partizioni](sql-database-elastic-scale-shard-map-management.md).

* **Routing dipendente dai dati**: immaginare una richiesta in arrivo nell'applicazione. In base al valore della chiave di partizionamento orizzontale della richiesta, l'applicazione deve stabilire il database corretto che contiene i dati per la chiave e quindi aprire una connessione ad esso per elaborare la richiesta. Il routing dipendente dai dati consente di aprire connessioni con una singola e semplice chiamata alla mappa partizioni dell'applicazione. Il routing dipendente dai dati è un'altra area del codice dell'infrastruttura ora coperta dalle funzionalità della libreria client dei database elastici. Per informazioni dettagliate, vedere [Routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md).

* **Query su più partizioni**: l'esecuzione di query su più partizioni opera quando una richiesta include più partizioni o tutte le partizioni. Una query su più partizioni esegue lo stesso codice T-SQL in tutte le partizioni o in un set di partizioni. I risultati restituiti dalle partizioni coinvolte vengono uniti in un set di risultati complessivi mediante la semantica di UNION ALL. La funzionalità come viene esposta tramite la libreria client gestisce numerose attività, tra cui gestione delle connessioni, gestione dei thread, gestione degli errori ed elaborazione dei risultati intermedi e consente di eseguire query su centinaia di partizioni. Per informazioni dettagliate, vedere [Esecuzione di query su più partizioni](sql-database-elastic-scale-multishard-querying.md).

In generale, i clienti che utilizzano gli strumenti dei database elastici quando inviano operazioni locali della partizione anziché operazioni tra più partizioni con la propria semantica, possono aspettarsi di ottenere funzionalità T-SQL complete.

## Passaggi successivi

Provare l’[app di esempio](sql-database-elastic-scale-get-started.md)che illustra le funzioni client.

Per installare la libreria, andare su [Libreria client di database elastici](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Per istruzioni sull'uso dello strumento di suddivisione-unione, vedere [Panoramica sullo strumento di suddivisione-unione](sql-database-elastic-scale-overview-split-and-merge.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-database-client-library/glossary.png

<!---HONumber=August15_HO6-->