---
title: Creare contatori delle prestazioni per monitorare le prestazioni del gestore delle mappe partizioni
description: La classe ShardMapManager e i contatori delle prestazioni con routing dipendente dai dati
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 5c6c923c86ea0c5968079188c87ec3988ec30142
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59494919"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Creare contatori delle prestazioni per monitorare le prestazioni del gestore delle mappe partizioni

I contatori delle prestazioni consentono di tenere traccia delle prestazioni [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) operazioni. Questi contatori sono accessibili in Performance Monitor, sotto la categoria "Database elastico: Gestione di partizioni".

È possibile acquisire le prestazioni di un [gestore mappe partizioni](sql-database-elastic-scale-shard-map-management.md), soprattutto quando si usa il [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md). Per creare i contatori si usano i metodi della classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**Per la versione più recente:** passare a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Vedere anche [Aggiornare un'app in modo da usare la libreria client dei database elastici più recente](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Prerequisiti

* Per creare la categoria e i contatori delle prestazioni, l'utente deve far parte del gruppo **Administrators** locale per il computer che ospita l'applicazione.  
* Per creare un'istanza del contatore delle prestazioni e aggiornare i contatori, l'utente deve essere membro del gruppo **Administrators** o **Performance Monitor Users**.

## <a name="create-performance-category-and-counters"></a>Creare una categoria e contatori delle prestazioni

Per creare i contatori, chiamare il metodo CreatePerformanceCategoryAndCounters della [classe ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Solo un amministratore può eseguire il metodo:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

È inoltre possibile utilizzare [questo](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script di PowerShell per eseguire il metodo.
Il metodo crea i contatori delle prestazioni seguenti:  

* **Mapping memorizzati nella cache**: numero di mapping memorizzati nella cache per la mappa partizioni.
* **Operazioni di routing dipendente dai dati al secondo**: frequenza delle operazioni di routing dipendente dai dati per la mappa partizioni. Questo contatore viene aggiornato quando una chiamata a [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) genera una connessione riuscita alla partizione di destinazione.
* **Riscontri di ricerca dei mapping nella cache al secondo**: frequenza delle operazioni di ricerca di mapping della mappa partizioni nella cache che hanno esito positivo.
* **Mancati riscontri di ricerca dei mapping nella cache al secondo**: frequenza delle operazioni di ricerca di mapping della mappa partizioni nella cache che hanno esito negativo.
* **Mapping aggiunti o aggiornati nella cache al secondo**: frequenza con cui i mapping della mappa partizioni vengono aggiunti o aggiornati nella cache.
* **Mapping rimossi dalla cache al secondo**: frequenza con cui i mapping della mappa partizioni vengono rimossi dalla cache.

Vengono creati contatori delle prestazioni per ciascuna mappa partizioni memorizzata nella cache in modalità per processo.  

## <a name="notes"></a>Note

I seguenti eventi attivano la creazione di contatori delle prestazioni:  

* Inizializzazione di [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) con [caricamento eager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), se ShardMapManager contiene mappe partizioni. Sono inclusi i metodi [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) e [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager).
* Ricerca con esito positivo di una mappa partizioni (mediante [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) o [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Creazione di una mappa partizioni mediante CreateShardMap().

I contatori delle prestazioni verranno aggiornati da tutte le operazioni della cache eseguite sulla mappa partizioni e sui mapping. La rimozione della mappa partizioni tramite DeleteShardMap() ha come risultato l'eliminazione dell'istanza dei contatori delle prestazioni.  

## <a name="best-practices"></a>Procedure consigliate

* La creazione della categoria e dei contatori delle prestazioni deve essere eseguita solo una volta prima della creazione dell'oggetto ShardMapManager. Ogni esecuzione del comando CreatePerformanceCategoryAndCounters() cancella i contatori precedenti (con la perdita di tutti i dati segnalati da tutte le istanze) e ne crea di nuovi.  
* Le istanze dei contatori delle prestazioni vengono create in modalità per processo. Un arresto anomalo dell'applicazione o la rimozione di una mappa partizioni dalla cache causa l'eliminazione delle istanze dei contatori delle prestazioni.  

### <a name="see-also"></a>Vedere anche 

[Panoramica sulle funzionalità di database elastico](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
