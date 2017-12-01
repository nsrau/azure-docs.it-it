---
title: Creazione di database cloud scalabili | Documentazione Microsoft
description: Creare applicazioni di database .NET scalabili con la libreria client di database elastici
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 1f11c52d-13c1-4994-b9b1-5b1ae2f9255f
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: ddove
ms.openlocfilehash: 721b87c67aa5e8002f21faca5a10fe41b8958e1e
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="building-scalable-cloud-databases"></a>Creazione di database cloud scalabili
La scalabilità orizzontale dei database può essere ottenuta facilmente con gli strumenti e le funzionalità scalabili per il database SQL di Azure. In particolare, è possibile usare la **libreria client dei database elastici** per creare e gestire i database con scalabilità orizzontale. Questa funzionalità consente di sviluppare con facilità applicazioni partizionate usando centinaia o anche migliaia di database SQL Azure. [processi elastici](sql-database-elastic-jobs-powershell.md) possono quindi essere usati per facilitare la gestione di questi database.

Per eseguire il download:
* la versione .NET della libreria, vedere [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).
* la versione Java della libreria, vedere [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).

## <a name="documentation"></a>Documentazione
1. [Iniziare a utilizzare gli strumenti di database elastici](sql-database-elastic-scale-get-started.md)
2. [Funzionalità di database elastico](sql-database-elastic-scale-introduction.md)
3. [Gestione mappe partizioni](sql-database-elastic-scale-shard-map-management.md)
4. [Eseguire la migrazione dei database esistenti per ottenere scalabilità orizzontale](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Routing dipendente dei dati](sql-database-elastic-scale-data-dependent-routing.md)
6. [Query su più partizioni](sql-database-elastic-scale-multishard-querying.md)
7. [Aggiunta di una partizione utilizzando gli strumenti di database elastici](sql-database-elastic-scale-add-a-shard.md)
8. [Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Aggiornare le app della libreria client](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Panoramica sulle query di database elastico](sql-database-elastic-query-overview.md)
11. [Glossario sugli strumenti di database elastici](sql-database-elastic-scale-glossary.md)
12. [Libreria client dei database elastici con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Uso della libreria client dei database elastici con Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Strumento di divisione-unione](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Contatori delle prestazioni per Gestore mappe partizioni](sql-database-elastic-database-client-library.md) 
16. [Domande frequenti sugli strumenti di database elastici](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Funzionalità client
La gestione delle applicazioni con scalabilità orizzontale mediante il *partizionamento orizzontale* presenta sfide sia per gli sviluppatori che per gli amministratori. La libreria client semplifica le attività di gestione, fornendo strumenti che consentono sia agli sviluppatori che agli amministratori di gestire più agevolmente i database con scalabilità orizzontale. In un esempio tipico, è necessario gestire molti database, detti anche "partizioni". I clienti si trovano nello stesso database ed è disponibile un database per cliente (modello single-tenant). La libreria client include le seguenti funzionalità:

- **Gestione mappe partizioni**: viene creato un database speciale denominato "gestore mappe partizioni". La gestione delle mappe partizioni è la possibilità per un'applicazione di gestire metadati nelle proprie partizioni. Gli sviluppatori possono usare questa funzionalità per registrare i database come partizioni, descrivere i mapping di singole chiavi di partizionamento orizzontale o di intervalli di chiavi per i database, nonché gestire i metadati man mano che il numero e la composizione dei database si evolve, per rispecchiare le modifiche apportate alla capacità. Senza la libreria client dei database elastici, è necessario dedicare molto tempo alla scrittura di codice di gestione durante l'implementazione del partizionamento orizzontale. Per informazioni dettagliate, vedere [Gestione mappe partizioni](sql-database-elastic-scale-shard-map-management.md).

- **Routing dipendente dai dati**: immaginare una richiesta in arrivo nell'applicazione. L'applicazione individua il database corretto in base al valore della chiave di partizionamento orizzontale della richiesta. Quindi l'applicazione apre una connessione al database per elaborare la richiesta. Il routing dipendente dai dati consente di aprire connessioni con una singola e semplice chiamata alla mappa partizioni dell'applicazione. Il routing dipendente dai dati è un'altra area del codice dell'infrastruttura ora coperta dalle funzionalità della libreria client dei database elastici. Per informazioni dettagliate, vedere [Routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md).
- **Query su più partizioni**: l'esecuzione di query su più partizioni opera quando una richiesta include più partizioni o tutte le partizioni. Una query su più partizioni esegue lo stesso codice T-SQL in tutte le partizioni o in un set di partizioni. I risultati restituiti dalle partizioni coinvolte vengono uniti in un set di risultati complessivi mediante la semantica di UNION ALL. La funzionalità come viene esposta tramite la libreria client gestisce numerose attività, tra cui gestione delle connessioni, gestione dei thread, gestione degli errori ed elaborazione dei risultati intermedi. e consente di eseguire query su centinaia di partizioni. Per informazioni dettagliate, vedere [Esecuzione di query su più partizioni](sql-database-elastic-scale-multishard-querying.md).

In generale, i clienti che utilizzano gli strumenti dei database elastici quando inviano operazioni locali della partizione anziché operazioni tra più partizioni con la propria semantica, possono aspettarsi di ottenere funzionalità T-SQL complete.



## <a name="next-steps"></a>Passaggi successivi

- Libreria client di database elastici ([.NET](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22)) - per **scaricare** la libreria.

- [Iniziare a usare gli strumenti di database elastici](sql-database-elastic-scale-get-started.md) per provare l'**app di esempio** che illustra le funzioni del client.

- GitHub ([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md)) - per contribuire al codice.
- [Panoramica delle query elastiche del database SQL di Azure](sql-database-elastic-query-overview.md) - per usare le query elastiche.

- [Lo spostamento dei dati tra database cloud di scalabilità orizzontale](sql-database-elastic-scale-overview-split-and-merge.md): per istruzioni sull'uso dello **strumento di divisione-unione**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

