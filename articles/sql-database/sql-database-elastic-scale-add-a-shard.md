---
title: Aggiunta di una partizione utilizzando gli strumenti di database elastici | Documentazione Microsoft
description: "Informazioni su come impostare API di scalabilità elastica per aggiungere nuove partizioni a un set di partizioni."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 62a349db-bebe-406f-a120-2f1986f2b286
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: ddove
ms.openlocfilehash: c3abbae20772cfc37fb8c58dc97209ac42dde358
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Aggiunta di una partizione utilizzando gli strumenti di database elastici
## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Per aggiungere una partizione per un nuovo intervallo o una nuova chiave
Per una mappa partizioni già esistente, le applicazioni devono spesso aggiungere nuove partizioni per gestire i dati previsti dalle nuove chiavi o dai nuovi intervalli di chiavi. Ad esempio, è possibile che un'applicazione partizionata dall'ID tenant debba eseguire il provisioning di una nuova partizione per un nuovo tenant, oppure è possibile che i dati partizionati ogni mese richiedano il provisioning di una nuova partizione prima dell'inizio di ogni nuovo mese. 

Se il nuovo intervallo di valori di chiave non è già incluso in una mappa esistente, l'aggiunta della nuova partizione e l'associazione della nuova chiave o del nuovo intervallo alla partizione risulteranno semplici. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Esempio: aggiungere una partizione e il relativo intervallo a una mappa partizioni esistente
Questo esempio usa i metodi ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.trygetshard), [.NET](https://msdn.microsoft.com/library/azure/dn823929.aspx)) CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.createshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)), CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.createrangemapping), [.NET](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping\(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0}\))) e crea un'istanza della classe ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base._shard_location), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.)). Nell'esempio seguente un database denominato **sample_shard_2** e tutti gli oggetti di schema necessari in esso contenuti sono stati creati per contenere l'intervallo [300, 400).  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added. 
Shard shard2 = null; 

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
{ 
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
} 

// Create the mapping and associate it with the new shard 
sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 
```

Per la versione .NET, è anche possibile usare PowerShell come alternativa per creare un nuovo gestore delle mappe partizioni. Un esempio è disponibile [qui](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Per aggiungere una partizione per una parte vuota di un intervallo esistente
In alcuni casi è possibile che pur avendo già eseguito il mapping di un intervallo con una partizione e avere inserito parzialmente i dati, si desideri che i dati futuri vengano indirizzati a una partizione diversa. Si consideri ad esempio uno scenario in cui è stato eseguito il partizionamento per intervallo di giorni e sono stati già allocati 50 giorni a una partizione, ma a partire dal giorno 24 si desidera che i dati futuri vengano indirizzati a una partizione diversa. Lo [strumento di suddivisione-unione](sql-database-elastic-scale-overview-split-and-merge.md) dei database elastici può eseguire questa operazione, ma se non è necessario lo spostamento di dati (ad esempio, i dati per l'intervallo di giorni [25, 50], ossia dal giorno 25 incluso al giorno 50 escluso, non esistono ancora) è possibile eseguire l'intera operazione usando direttamente le API di gestione delle mappe partizioni.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Esempio: suddividere un intervallo e assegnare la parte vuota a una partizione appena aggiunta
È stato creato un database denominato "sample_shard_2" contenente tutti gli oggetti di schema necessari.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move 
Shard shard2 = null; 

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
{ 
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
} 

// Split the Range holding Key 25 
sm.SplitMapping(sm.GetMappingForKey(25), 25); 

// Map new range holding [25-50) to different shard: 
// first take existing mapping offline 
sm.MarkMappingOffline(sm.GetMappingForKey(25)); 

// now map while offline to a different shard and take online 
RangeMappingUpdate upd = new RangeMappingUpdate(); 
upd.Shard = shard2; 
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 
```

**Importante**: usare questa tecnica solo se si è certi che l'intervallo per il mapping aggiornato sia vuoto.  Poiché i metodi precedenti non controllano i dati dell'intervallo da spostare, è consigliabile includere i controlli nel codice.  Se esistono righe nell'intervallo da spostare, la distribuzione dei dati effettivi non corrisponderà alla mappa partizioni aggiornata. In questi casi utilizzare invece lo [strumento di suddivisione-unione](sql-database-elastic-scale-overview-split-and-merge.md) per eseguire l'operazione.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

