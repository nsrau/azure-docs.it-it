<properties title="Azure Elastic Scale Glossary" pageTitle="Glossario di Scalabilità elastica di Azure" description="Explanation of terms used for Elastic Scale feature of Azure SQL Database" metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Glossario di Scalabilità elastica
Di seguito sono elencate le definizioni dei termini usati nella funzionalità Scalabilità elastica del database SQL di Azure.

![Elastic Scale terms][1]

**Database**: un database SQL di Azure. 

**Routing dipendente dai dati**: la funzionalità che consente a un'applicazione di connettersi a una partizione in base a una specifica chiave di partizionamento orizzontale. Confrontare con **Query su più partizioni**.

**Mappa globale partizioni**: il set di mapping tra le chiavi di partizionamento orizzontale e le rispettivi partizioni all'interno di un **set di partizioni**. La mappa globale partizioni è archiviata nel **Gestore mappe partizioni**. Confrontare con la **mappa locale partizioni**.

**Mappa partizioni di tipo elenco**: una mappa partizioni in cui le chiavi di partizionamento orizzontale vengono mappate singolarmente. Confrontare con la **mappa partizioni di tipo intervallo**.   

**Mappa locale partizioni**: archiviata in una partizione, la mappa locale partizioni contiene i mapping per gli shardlet che risiedono nella partizione.

**Query su più partizioni**: la possibilità di eseguire una query su più partizioni; i set di risultati vengono restituiti usando la semantica di UNION ALL (nota anche come "query di tipo fan-out"). Confrontare con **Routing dipendente dai dati**.

**Mappa partizioni di tipo intervallo**: una mappa partizioni in cui la strategia di distribuzione delle partizioni è basata su più intervalli di valori contigui. 

**Tabelle di riferimento**: tabelle che non vengono partizionate, ma vengono replicate tra le partizioni. 

**Partizione**: un database SQL di Azure che archivia i dati da un set di dati partizionato. 

**Elasticità di partizionamento**: la possibilità di eseguire il **ridimensionamento orizzontale** e il **ridimensionamento verticale**.

**Tabelle partizionate**: tabelle che vengono partizionate, ovvero i cui dati vengono distribuiti tra le partizioni in base ai valori della chiave di partizionamento orizzontale. 

**Chiave di partizionamento orizzontale**: un valore di colonna che determina la modalità di distribuzione dei dati tra le partizioni. Il tipo valore può essere uno dei seguenti: int, bigint, varbinary o uniqueidentifier. 

**Set di partizioni**: la raccolta di partizioni attribuite alla stessa mappa partizioni nel gestore delle mappe partizioni.  

**Shardlet**: il complesso dei dati associati a un singolo valore di una chiave di partizionamento orizzontale in una partizione. Uno shardlet è la più piccola unità di spostamento dei dati possibile quando si ridistribuiscono tabelle partizionate. 

**Mappa partizioni**: il set di mapping tra le chiavi di partizionamento orizzontale e le rispettive partizioni.

**Gestore mappe partizioni**: un archivio di dati e oggetti di gestione che contiene le mappe partizioni, i percorsi delle partizioni e i mapping per uno o più set di partizioni.


##Verbi

**Scalare orizzontalmente**: aumentare o ridurre le dimensioni di una raccolta di partizioni aggiungendo o rimuovendo partizioni a una mappa partizioni.

**Unire**: spostare shardlet da due partizioni a una partizione e aggiornare la mappa partizioni di conseguenza.

**Spostare shardlet**: spostare un singolo shardlet in una partizione diversa. 

**Partizionare**: eseguire il partizionamento orizzontale di dati strutturati in modo identico in più database in base a una chiave di partizionamento orizzontale.

**Dividere**: spostare più shardlet da una partizione a un'altra (in genere nuova). Come punto di divisione viene usata una chiave di partizionamento orizzontale fornita dall'utente.

**Scalare verticalmente**: aumentare o ridurre il livello di prestazioni di una singola partizione. Ad esempio, modificare una partizione da Standard a Premium (come necessario per motivi di prestazioni). 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
