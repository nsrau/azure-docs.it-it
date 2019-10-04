---
title: Considerazioni sulle prestazioni per file di Azure NetApp | Microsoft Docs
description: Descrive le considerazioni sulle prestazioni per file di Azure NetApp.
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
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454139"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Considerazioni sulle prestazioni per Azure NetApp Files

Il [limite di velocità effettiva](azure-netapp-files-service-levels.md) per un volume è determinato da una combinazione della quota assegnata al volume e il servizio di livello selezionato. Quando si eseguono i piani di prestazioni sui file di Azure NetApp, è necessario comprendere alcune considerazioni. 

## <a name="quota-and-throughput"></a>Quote e velocità effettiva  

Il limite di velocità effettiva è determinante solo una delle effettive prestazioni che verranno realizzata.  

Considerazioni sulle prestazioni di archiviazione standard, tra cui lettura e scrittura combinazione, le dimensioni di trasferimento, i modelli casuali o sequenziali e molti altri fattori verranno conteggiato nel totale delle prestazioni recapitate.  

La velocità effettiva empirical massima che è stata rilevata nel test è 4.500 MiB/s.  Al livello di archiviazione Premium una quota di volume di TiB 70.31 verrà effettuato il provisioning di un limite di velocità effettiva è sufficientemente elevato per ottenere questo livello di prestazioni.  

Se si sta considerando l'assegnazione di volume gli importi delle quote oltre TiB 70.31, quota aggiuntiva è possibile assegnare a un volume per archiviare dati aggiuntivi. Tuttavia, la quota di aggiunta non comporterà un ulteriore aumento di velocità effettiva.  

Visualizzare [benchmark delle prestazioni per file di Azure NetApp](azure-netapp-files-performance-benchmarks.md) per altre informazioni.

## <a name="overprovisioning-the-volume-quota"></a>Ricorrere all'overprovisioning la quota di volume

Se le prestazioni del carico di lavoro sono associati limite di velocità effettiva, è possibile overprovision la quota di volume per impostare un livello di velocità effettiva superiore e ottenere prestazioni più elevate.  

Ad esempio, se un volume nel livello di archiviazione Premium presenta solo 500 GiB di dati, ma richiede 128 MiB/s di velocità effettiva, è possibile impostare la quota a 2 TiB in modo che il livello di velocità effettiva viene impostato di conseguenza (64 MiB/s per ogni TB * 2 TiB = 128 MiB/s).  

Se si overprovision in modo coerente un volume per ottenere una velocità effettiva superiore, è consigliabile usare invece un livello di servizio superiore.  Nell'esempio precedente, è possibile ottenere lo stesso limite di velocità effettiva con metà della quota di volume usando invece il livello di archiviazione extra (128 MiB/s per TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>In modo dinamico aumentando o riducendo la quota del volume

Se i requisiti di prestazioni sono di natura temporanei, o se è stata incrementata esigenze di prestazioni per un periodo di tempo fisso, è possibile incrementare o diminuire dinamicamente la quota del volume per istantaneamente regolare il limite di velocità effettiva.  Tenere presente le considerazioni seguenti: 

* La quota del volume può essere aumentata o diminuita senza che sia necessario sospendere i/o e l'accesso al volume non è interrotto o interessato.  

    È possibile modificare la quota durante una transazione attiva dei / o su un volume.  Si noti che la quota del volume non può essere ridotto mai inferiore alla quantità di dati logico che viene archiviati nel volume.

* Quando viene modificata la quota del volume, la modifica corrispondente nel limite di velocità effettiva è quasi istantanea. 

    La modifica non interrompere o compromettere l'accesso al volume o i/o.  

* Regolando la quota del volume richiede una modifica nelle dimensioni del pool di capacità.  

    È possibile regolare le dimensioni del pool di capacità e in modo dinamico senza influire sulla disponibilità del volume o i/o.

## <a name="next-steps"></a>Passaggi successivi

- [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Benchmark delle prestazioni per file di Azure NetApp](azure-netapp-files-performance-benchmarks.md)