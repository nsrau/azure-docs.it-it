---
title: Creazione di database cloud scalabili
description: Crea app di database .NET scalabili con la libreria client dei database elastici.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: bfe5dc00ba0255520c04ea85157f0b8bdc71b590
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84038512"
---
# <a name="building-scalable-cloud-databases"></a>Creazione di database cloud scalabili
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

La scalabilità orizzontale dei database può essere ottenuta facilmente con gli strumenti e le funzionalità scalabili per il database SQL di Azure. In particolare, è possibile usare la **libreria client dei database elastici** per creare e gestire i database con scalabilità orizzontale. Questa funzionalità consente di sviluppare facilmente applicazioni partizionate usando centinaia, o addirittura migliaia, di database nel database SQL di Azure.

Per eseguire il download:

* Per la versione Java della libreria, vedere il [repository centrale Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Per la versione .NET della libreria, vedere [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Documentazione

1. [Inizia a usare gli strumenti di database elastici](elastic-scale-get-started.md)
2. [Funzionalità di database elastico](elastic-scale-introduction.md)
3. [Gestione mappe partizioni](elastic-scale-shard-map-management.md)
4. [Eseguire la migrazione dei database esistenti per ottenere scalabilità orizzontale](elastic-convert-to-use-elastic-tools.md)
5. [Routing dipendente dai dati](elastic-scale-data-dependent-routing.md)
6. [Query su più partizioni](elastic-scale-multishard-querying.md)
7. [Aggiunta di una partizione utilizzando gli strumenti di database elastici](elastic-scale-add-a-shard.md)
8. [Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)
9. [Aggiornare le app della libreria client](elastic-scale-upgrade-client-library.md) 
10. [Panoramica sulle query di database elastico](elastic-query-overview.md)
11. [Glossario sugli strumenti di database elastici](elastic-scale-glossary.md)
12. [Libreria client dei database elastici con Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Libreria client dei database elastici con elegante](elastic-scale-working-with-dapper.md)
14. [Strumento di divisione-unione](elastic-scale-overview-split-and-merge.md)
15. [Contatori delle prestazioni per Gestore mappe partizioni](elastic-database-client-library.md) 
16. [Domande frequenti sugli strumenti di database elastici](elastic-scale-faq.md)

## <a name="client-capabilities"></a>Funzionalità client

La gestione delle applicazioni con scalabilità orizzontale mediante il *partizionamento orizzontale* presenta sfide sia per gli sviluppatori che per gli amministratori. La libreria client semplifica le attività di gestione, fornendo strumenti che consentono sia agli sviluppatori che agli amministratori di gestire più agevolmente i database con scalabilità orizzontale. In un esempio tipico, è necessario gestire molti database, detti anche "partizioni". I clienti si trovano nello stesso database ed è disponibile un database per cliente (modello single-tenant). La libreria client include le seguenti funzionalità:

- **Gestione delle mappe**partizioni: viene creato un database speciale denominato "gestore mappe partizioni". La gestione delle mappe partizioni è la possibilità per un'applicazione di gestire metadati nelle proprie partizioni. Gli sviluppatori possono usare questa funzionalità per registrare i database come partizioni, descrivere i mapping di singole chiavi di partizionamento orizzontale o di intervalli di chiavi per i database, nonché gestire i metadati man mano che il numero e la composizione dei database si evolve, per rispecchiare le modifiche apportate alla capacità. Senza la libreria client dei database elastici, è necessario dedicare molto tempo alla scrittura del codice di gestione durante l'implementazione del partizionamento orizzontale. Per informazioni dettagliate, vedere [Gestione mappe partizioni](elastic-scale-shard-map-management.md).

- **Routing dipendente dai dati**: immaginare una richiesta in arrivo nell'applicazione. L'applicazione individua il database corretto in base al valore della chiave di partizionamento orizzontale della richiesta. Quindi l'applicazione apre una connessione al database per elaborare la richiesta. Il routing dipendente dai dati consente di aprire connessioni con una singola e semplice chiamata alla mappa partizioni dell'applicazione. Il routing dipendente dai dati è un'altra area del codice dell'infrastruttura che ora copre le funzionalità della libreria client dei database elastici. Per informazioni dettagliate, vedere [Routing dipendente dai dati](elastic-scale-data-dependent-routing.md).
- **Query su più partizioni**: l'esecuzione di query su più partizioni opera quando una richiesta include più partizioni o tutte le partizioni. Una query su più partizioni esegue lo stesso codice T-SQL in tutte le partizioni o in un set di partizioni. I risultati restituiti dalle partizioni coinvolte vengono uniti in un set di risultati complessivi mediante la semantica di UNION ALL. La funzionalità come viene esposta tramite la libreria client gestisce numerose attività, tra cui gestione delle connessioni, gestione dei thread, gestione degli errori ed elaborazione dei risultati intermedi. e consente di eseguire query su centinaia di partizioni. Per informazioni dettagliate, vedere [Esecuzione di query su più partizioni](elastic-scale-multishard-querying.md).

In generale, i clienti che usano gli strumenti di database elastici possono prevedere la funzionalità T-SQL completa quando si inviano operazioni locali di partizionamento anziché le operazioni tra partizioni con una semantica personalizzata.



## <a name="next-steps"></a>Passaggi successivi

- Libreria client dei database elastici ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)): per **scaricare** la libreria.

- [Introduzione agli strumenti di database elastici](elastic-scale-get-started.md) : per provare l' **app di esempio** che illustra le funzioni client.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md) o [.NET](https://github.com/Azure/elastic-db-tools)) per contribuire al codice.
- [Panoramica delle query elastiche del database SQL di Azure](elastic-query-overview.md) - per usare le query elastiche.

- [Lo spostamento dei dati tra database cloud di scalabilità orizzontale](elastic-scale-overview-split-and-merge.md): per istruzioni sull'uso dello **strumento di divisione-unione**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

