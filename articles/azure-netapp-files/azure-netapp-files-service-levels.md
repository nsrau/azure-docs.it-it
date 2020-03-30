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
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832575"
---
# <a name="service-levels-for-azure-netapp-files"></a>Livelli di servizio per Azure NetApp Files
I livelli di servizio sono un attributo di un pool di capacità. I livelli di servizio vengono definiti e differenziati in base alla velocità effettiva massima consentita per un volume nel pool di capacità in base alla quota assegnata al volume.

## <a name="supported-service-levels"></a>Livelli di servizio supportati

File NetApp di Azure supporta tre livelli di servizio: *Ultra*, *Premium*e *Standard*. 

* <a name="Ultra"></a>Ultra storage

    Il livello di archiviazione Ultra fornisce fino a 128 MiB/s di velocità effettiva per 1 TiB della quota di volume assegnata. 

* <a name="Premium"></a>Archiviazione premium

    Il livello di archiviazione Premium fornisce fino a 64 MiB/s di velocità effettiva per 1 TiB della quota di volume assegnata. 

* <a name="Standard"></a>Archiviazione standard

    Il livello di archiviazione Standard fornisce fino a 16 MiB/s di velocità effettiva per 1 TiB della quota di volume assegnata.

## <a name="throughput-limits"></a>Limiti di velocità effettiva

Il limite di velocità effettiva per un volume è determinato dalla combinazione dei seguenti fattori:
* Il livello di servizio del pool di capacità a cui appartiene il volume
* La quota assegnata al volume  

Questo concetto è illustrato nel diagramma seguente:This concept is illustrated in the diagram below:

![Illustrazione del livello di servizio](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

Nell'esempio 1 precedente, a un volume di un pool di capacità con il livello di archiviazione Premium assegnato 2 TiB della quota verrà assegnato un limite di velocità effettiva di 128 MiB/s (2 TiB e 64 MiB/s). Questo scenario si applica indipendentemente dalle dimensioni del pool di capacità o dal consumo effettivo di volumi.

Nell'esempio 2 precedente, a un volume di un pool di capacità con il livello di archiviazione Premium assegnato 100 GiB di quota verrà assegnato un limite di velocità effettiva di 6,25 MiB/s (0,09765625 TiB e 64 MiB/s). Questo scenario si applica indipendentemente dalle dimensioni del pool di capacità o dal consumo effettivo di volumi.

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [pagina dei prezzi di Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) per il prezzo dei diversi livelli di servizio
- Vedere Modello di [costo per i file NetApp](azure-netapp-files-cost-model.md) di Azure per il calcolo del consumo di capacità in un pool di capacitàSee Cost model for Azure NetApp Files for the calculation of the capacity consumption in a capacity pool 
- [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
- Vedere Contratto di [servizio (SLA) per i file NetApp di AzureSee Service Level Agreement (SLA) for Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)