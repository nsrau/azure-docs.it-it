---
title: Monitoraggio delle prestazioni di Azure Service Fabric | Microsoft Docs
description: Informazioni sui contatori delle prestazioni per il monitoraggio e la diagnostica dei cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: dekapur
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: ac019d5393e9e9b7edb7960aa50edd5af3b49ebc
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---

# <a name="performance-metrics"></a>Metriche delle prestazioni

Per comprendere le prestazioni del cluster e delle applicazioni in esecuzione al suo interno è necessario raccogliere alcune metriche. Per i cluster di Service Fabric è consigliabile raccogliere i contatori delle prestazioni seguenti.

## <a name="nodes"></a>Nodi

Per i computer presenti nel cluster è opportuno raccogliere i contatori delle prestazioni seguenti per comprendere meglio il carico di ogni computer e prendere decisioni appropriate sulla scalabilità del cluster.

| Categoria contatore | Nome contatore |
| --- | --- |
| PhysicalDisk(Total) | Avg. Disk Read Queue Length |
| PhysicalDisk(Total) | Avg. Disk Write Queue Length |
| PhysicalDisk(Total) | Letture disco/sec  |
| PhysicalDisk(Total) | Byte letti da disco/sec  |
| PhysicalDisk(Total) | Scritture disco/sec |
| PhysicalDisk(Total) | Byte scritti su disco/sec |
| PhysicalDisk(per Disk) | Avg. Disk Read Queue Length |
| PhysicalDisk(per Disk) | Avg. Disk Write Queue Length |
| PhysicalDisk(per Disk) | Avg. Disk sec/Read |
| PhysicalDisk(per Disk) | Avg. Disk sec/Write |
| PhysicalDisk(per Disk) | Letture disco/sec  |
| PhysicalDisk(per Disk) | Byte letti da disco/sec  |
| PhysicalDisk(per Disk) | Scritture disco/sec |
| PhysicalDisk(per Disk) | Byte scritti su disco/sec |
| Memoria | MByte disponibili |
| PagingFile | % Usage |
| Process(Total) | % di tempo processore |
| Process (per service) | % di tempo processore |
| Process (per service) | ID Process |
| Process (per service) | Private Bytes |
| Process (per service) | Thread Count |
| Process (per service) | Virtual Bytes |
| Process (per service) | Working Set |
| Process (per service) | Working Set - Private |

## <a name="net-applications-and-services"></a>Applicazioni e servizi .NET

Se si distribuiscono servizi .NET nel cluster, raccogliere i contatori seguenti. 

| Categoria contatore | Nome contatore |
| --- | --- |
| .NET CLR Memory (per service) | ID di processo |
| .NET CLR Memory (per service) | # Total committed Bytes |
| .NET CLR Memory (per service) | # Total reserved Bytes |
| .NET CLR Memory (per service) | # Bytes in all Heaps |
| .NET CLR Memory (per service) | # Gen 0 Collections |
| .NET CLR Memory (per service) | # Gen 1 Collections |
| .NET CLR Memory (per service) | # Gen 2 Collections |
| .NET CLR Memory (per service) | Percentuale tempo in GC |

### <a name="service-fabrics-custom-performance-counters"></a>Contatori delle prestazioni personalizzati di Service Fabric

Service Fabric genera una quantità significativa di contatori delle prestazioni personalizzati. Se è installato l'SDK, è possibile visualizzare l'elenco completo disponibile nel computer Windows mediante l'applicazione Performance Monitor (Start > Performance Monitor). 

Se si usa Reliable Actors, nelle applicazioni che si sta distribuendo nel cluster aggiungere i contatori delle categorie `Service Fabric Actor` e `Service Fabric Actor Method` (vedere [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)).

Analogamente, se si usa Reliable Services, è necessario raccogliere i contatori delle categorie `Service Fabric Service` e `Service Fabric Service Method`. 

Se si usa Reliable Collection, infine, è consigliabile aggiungere il contatore `Avg. Transaction ms/Commit` della categoria `Service Fabric Transactional Replicator` per raccogliere la latenza di commit media per ogni transazione.


## <a name="next-steps"></a>Passaggi successivi

* Acquisire altre informazioni sulla [generazione di eventi a livello di infrastruttura](service-fabric-diagnostics-event-generation-infra.md) in Service Fabric
* Raccogliere metriche delle prestazioni tramite [Diagnostica di Azure](service-fabric-diagnostics-event-aggregation-wad.md)

