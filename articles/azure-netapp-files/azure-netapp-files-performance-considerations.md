---
title: Considerazioni sulle prestazioni per Azure NetApp Files | Microsoft Docs
description: Informazioni sulle prestazioni per Azure NetApp Files, tra cui la relazione di quota e limite di velocità effettiva e come aumentare o ridurre dinamicamente la quota del volume.
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
ms.openlocfilehash: 5f88b4755c7b4c0b20f27065cf9de2351251bc1c
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513877"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Considerazioni sulle prestazioni per Azure NetApp Files

Il [limite di velocità effettiva](azure-netapp-files-service-levels.md) per un volume è determinato da una combinazione della quota assegnata al volume e del livello di servizio selezionato. Quando si apportano piani di prestazioni relativi Azure NetApp Files, è necessario comprendere alcune considerazioni. 

## <a name="quota-and-throughput"></a>Quota e velocità effettiva  

Il limite di velocità effettiva è solo un fattore determinante delle prestazioni effettive che verranno realizzate.  

Considerazioni tipiche relative alle prestazioni di archiviazione, tra cui la combinazione di lettura e scrittura, la dimensione del trasferimento, i modelli casuali o sequenziali e molti altri fattori contribuiscono alle prestazioni totali fornite.  

La velocità effettiva empirica massima osservata nel testing è 4.500 MiB/s.  Al livello di archiviazione Premium, una quota di volumi di 70,31 TiB effettuerà il provisioning di un limite di velocità effettiva sufficientemente elevato per ottenere questo livello di prestazioni.  

Se si sta valutando di assegnare gli importi di quota del volume oltre 70,31 TiB, è possibile assegnare una quota aggiuntiva a un volume per l'archiviazione di dati aggiuntivi. Tuttavia, la quota aggiunta non comporterà un ulteriore aumento della velocità effettiva effettiva.  

## <a name="overprovisioning-the-volume-quota"></a>Overprovisioning della quota del volume

Se le prestazioni di un carico di lavoro sono limitate al limite di velocità effettiva, è possibile eseguire l'overprovisioning della quota del volume per impostare un livello di velocità effettiva superiore e ottenere prestazioni migliori.  

Ad esempio, se un volume del livello di archiviazione Premium ha solo 500 GiB di dati, ma richiede 128 MiB/s di velocità effettiva, è possibile impostare la quota su 2 TiB in modo che il livello di velocità effettiva venga impostato di conseguenza (64 MiB/s per TB * 2 TiB = 128 MiB/s).  

Se si esegue costantemente il provisioning di un volume per ottenere una velocità effettiva più elevata, è consigliabile usare invece un livello di servizio superiore.  Nell'esempio precedente, è possibile ottenere lo stesso limite di velocità effettiva con metà della quota del volume usando il livello di archiviazione Ultra (128 MiB/s per TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Aumento o diminuzione dinamica della quota del volume

Se i requisiti di prestazioni sono di natura temporanea o se si hanno esigenze di prestazioni più elevate per un periodo di tempo fisso, è possibile aumentare o diminuire dinamicamente la quota del volume per regolare immediatamente il limite di velocità effettiva.  Tenere presenti le seguenti considerazioni: 

* La quota del volume può essere aumentata o diminuita senza la necessità di sospendere l'i/o e l'accesso al volume non viene interrotto o interessato.  

    È possibile modificare la quota durante una transazione di I/O attiva su un volume.  Si noti che la quota del volume non può mai essere ridotta al di sotto della quantità di dati logici archiviati nel volume.

* Quando la quota del volume viene modificata, la modifica corrispondente nel limite di velocità effettiva è quasi istantanea. 

    La modifica non interrompe né influisca sull'accesso al volume o sull'I/O.  

* Per regolare la quota del volume è necessario modificare le dimensioni del pool di capacità.  

    Le dimensioni del pool di capacità possono essere modificate dinamicamente e senza alcun effetto sulla disponibilità dei volumi o sull'I/O.

## <a name="next-steps"></a>Passaggi successivi

- [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Benchmark delle prestazioni per Linux](performance-benchmarks-linux.md)