---
title: Livelli di servizio per Azure NetApp Files | Microsoft Docs
description: Vengono descritte le prestazioni di velocità effettiva per i livelli di servizio di Azure NetApp Files.
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
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 639f1e09fdb5603965209e5b5ee6c224ad238b76
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533122"
---
# <a name="service-levels-for-azure-netapp-files"></a>Livelli di servizio per Azure NetApp Files
I livelli di servizio sono un attributo di un pool di capacità. I livelli di servizio sono definiti e differenziati dalla velocità effettiva massima consentita per un volume nel pool di capacità in base alla quota assegnata al volume.

## <a name="supported-service-levels"></a>Livelli di servizio supportati

Azure NetApp Files supporta tre livelli di servizio: *ultra*, *Premium*e *standard*. 

* <a name="Ultra"></a>Archiviazione Ultra

    Il livello di archiviazione Ultra fornisce fino a 128 MiB/s di velocità effettiva per 1 TiB della quota del volume assegnata. 

* <a name="Premium"></a>Archiviazione Premium

    Il livello di archiviazione Premium offre fino a 64 MiB/s di velocità effettiva per 1 TiB della quota del volume assegnata. 

* <a name="Standard"></a>Archiviazione standard

    Il livello di archiviazione standard prevede fino a 16 MiB/s di velocità effettiva per 1 TiB della quota del volume assegnata.

## <a name="throughput-limits"></a>Limiti di velocità effettiva

Il limite di velocità effettiva per un volume è determinato dalla combinazione dei fattori seguenti:
* Livello di servizio del pool di capacità a cui appartiene il volume
* Quota assegnata al volume  

Questo concetto è illustrato nel diagramma seguente:

![Illustrazione del livello di servizio](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

Nell'esempio 1 precedente, a un volume di un pool di capacità con il livello di archiviazione Premium assegnato 2 TiB della quota verrà assegnato un limite di velocità effettiva pari a 128 MiB/s (2 TiB * 64 MiB/s). Questo scenario si applica indipendentemente dalle dimensioni del pool di capacità o dal consumo effettivo del volume.

Nell'esempio 2 precedente, a un volume di un pool di capacità con il livello di archiviazione Premium assegnato 100 GiB della quota verrà assegnato un limite di velocità effettiva pari a 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Questo scenario si applica indipendentemente dalle dimensioni del pool di capacità o dal consumo effettivo del volume.

## <a name="next-steps"></a>Passaggi successivi

- [Pagina dei prezzi di Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Modello di costi per Azure NetApp Files](azure-netapp-files-cost-model.md) 
- [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
- [Contratto di servizio (SLA) per Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Modificare dinamicamente il livello di servizio di un volume](dynamic-change-volume-service-level.md) 