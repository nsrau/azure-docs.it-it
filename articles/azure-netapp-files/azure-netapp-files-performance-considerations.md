---
title: Considerazioni sulle prestazioni per i file NetApp di Azure Documenti Microsoft
description: Vengono descritte le considerazioni sulle prestazioni per i file NetApp di Azure.Describes performance considerations for Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67454139"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Considerazioni sulle prestazioni per Azure NetApp Files

Il limite di [velocità effettiva](azure-netapp-files-service-levels.md) per un volume è determinato da una combinazione della quota assegnata al volume e del livello di servizio selezionato. Quando si apportano piani di prestazioni sui file NetApp di Azure, è necessario comprendere diverse considerazioni. 

## <a name="quota-and-throughput"></a>Quota e velocità effettiva  

Il limite di velocità effettiva è solo un determinante delle prestazioni effettive che verranno realizzate.  

Considerazioni sulle prestazioni di archiviazione tipiche, tra cui la combinazione di lettura e scrittura, le dimensioni di trasferimento, i modelli casuali o sequenziali e molti altri fattori contribuiranno alle prestazioni totali fornite.  

La velocità effettiva empirica massima osservata nei test è 4.500 MiB/s.  Al livello di archiviazione Premium, una quota di volume di 70.31 TiB eseguirà il provisioning di un limite di velocità effettiva sufficientemente elevato per raggiungere questo livello di prestazioni.  

Se si sta valutando l'assegnazione di importi di quote di volume oltre 70,31 TiB, è possibile assegnare quote aggiuntive a un volume per l'archiviazione di dati aggiuntivi. Tuttavia, la quota aggiunta non comporterà un ulteriore aumento della velocità effettiva.  

Per altre informazioni, vedere Benchmark delle prestazioni per i file NetApp di [Azure.See Performance benchmarks for Azure NetApp Files](azure-netapp-files-performance-benchmarks.md) for additional information.

## <a name="overprovisioning-the-volume-quota"></a>Overprovisioning della quota di volume

Se le prestazioni di un carico di lavoro sono associate al limite di velocità effettiva, è possibile eseguire il overprovisioning della quota di volume per impostare un livello di velocità effettiva più elevato e ottenere prestazioni più elevate.  

Ad esempio, se un volume nel livello di archiviazione Premium ha solo 500 GiB di dati ma richiede 128 MiB/s di velocità effettiva, è possibile impostare la quota su 2 TiB in modo che il livello di velocità effettiva sia impostato di conseguenza (64 MiB/s per TB - 2 TiB - 128 MiB/s).  

Se si esegue costantemente l'overprovisioning di un volume per ottenere una velocità effettiva più elevata, è consigliabile usare invece un livello di servizio più elevato.  Nell'esempio precedente, è possibile ottenere lo stesso limite di velocità effettiva con la metà della quota di volume utilizzando invece il livello di archiviazione Ultra (128 MiB/s per TiB , 1 TiB e 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Aumentare o diminuire dinamicamente la quota di volume

Se i requisiti di prestazioni sono di natura temporanea o se si dispone di maggiori esigenze di prestazioni per un determinato periodo di tempo, è possibile aumentare o ridurre dinamicamente la quota di volume per regolare istantaneamente il limite di velocità effettiva.  Tenere presenti le seguenti considerazioni: 

* La quota di volume può essere aumentata o diminuita senza dover sospendere le i/o e l'accesso al volume non viene interrotto o interessato.  

    È possibile rettificare la quota durante una transazione di I/O attiva rispetto a un volume.  Si noti che la quota del volume non può mai essere ridotta al di sotto della quantità di dati logici archiviati nel volume.

* Quando la quota del volume viene modificata, la modifica corrispondente del limite di velocità effettiva è quasi istantanea. 

    La modifica non interrompe né influisce sull'accesso al volume o sull'I/O.  

* La regolazione della quota di volume richiede una modifica delle dimensioni del pool di capacità.  

    Le dimensioni del pool di capacità possono essere regolate in modo dinamico e senza influire sulla disponibilità del volume o sulle/ dei / o.

## <a name="next-steps"></a>Passaggi successivi

- [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Benchmark delle prestazioni per Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)