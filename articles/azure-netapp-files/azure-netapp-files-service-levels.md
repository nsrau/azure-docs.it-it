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
ms.date: 10/06/2020
ms.author: b-juche
ms.openlocfilehash: 1c64bd10b34b61797cb3bf3de0cd7d2aa819e795
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777132"
---
# <a name="service-levels-for-azure-netapp-files"></a>Livelli di servizio per Azure NetApp Files
I livelli di servizio sono un attributo di un pool di capacità. I livelli di servizio sono definiti e differenziati dalla velocità effettiva massima consentita per un volume nel pool di capacità in base alla quota assegnata al volume.

## <a name="supported-service-levels"></a>Livelli di servizio supportati

Azure NetApp Files supporta tre livelli di servizio: *ultra*, *Premium*e *standard*. 

* <a name="Ultra"></a>Archiviazione Ultra

    Il livello di archiviazione Ultra fornisce fino a 128 MiB/s di velocità effettiva per 1 TiB di capacità sottoposta a provisioning. 

* <a name="Premium"></a>Archiviazione Premium

    Il livello di archiviazione Premium offre fino a 64 MiB/s di velocità effettiva per 1 TiB di capacità sottoposta a provisioning. 

* <a name="Standard"></a>Archiviazione standard

    Il livello di archiviazione standard offre fino a 16 MiB/s di velocità effettiva per 1 TiB di capacità sottoposta a provisioning.

## <a name="throughput-limits"></a>Limiti di velocità effettiva

Il limite di velocità effettiva per un volume è determinato dalla combinazione dei fattori seguenti:
* Livello di servizio del pool di capacità a cui appartiene il volume
* Quota assegnata al volume  
* Il tipo QoS (*auto* o *Manual*) del pool di capacità  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>Esempi di limiti di velocità effettiva dei volumi in un pool di capacità QoS automatica

Il diagramma seguente mostra i limiti di velocità effettiva dei volumi in un pool di capacità QoS automatica:

![Illustrazione del livello di servizio](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* Nell'esempio 1 precedente, a un volume di un pool di capacità QoS automatica con il livello di archiviazione Premium assegnato 2 TiB della quota verrà assegnato un limite di velocità effettiva pari a 128 MiB/s (2 TiB * 64 MiB/s). Questo scenario si applica indipendentemente dalle dimensioni del pool di capacità o dal consumo effettivo del volume.

* Nell'esempio 2 precedente, a un volume di un pool di capacità QoS automatica con il livello di archiviazione Premium assegnato 100 GiB della quota verrà assegnato un limite di velocità effettiva pari a 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Questo scenario si applica indipendentemente dalle dimensioni del pool di capacità o dal consumo effettivo del volume.

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>Esempi di limiti di velocità effettiva dei volumi in un pool di capacità QoS manuale 

Se si usa un pool di capacità QoS manuale, è possibile assegnare la capacità e la velocità effettiva per un volume in modo indipendente. Quando si crea un volume in un pool di capacità QoS manuale, è possibile specificare il valore di velocità effettiva (MiB/S). La velocità effettiva totale assegnata ai volumi in un pool di capacità QoS manuale dipende dalle dimensioni del pool e dal livello di servizio. Il limite è limitato dalle dimensioni del pool di capacità in TiB x velocità effettiva/TiB del livello di servizio. Ad esempio, un pool di capacità da 10 TiB con il livello di servizio ultra ha una capacità di velocità effettiva totale di 1280 MiB/s (10 TiB x 128 MiB/s/TiB) disponibile per i volumi.

Per un sistema di SAP HANA, questo pool di capacità può essere utilizzato per creare i volumi seguenti. Ogni volume fornisce le singole dimensioni e la velocità effettiva per soddisfare i requisiti dell'applicazione:

* Volume di dati SAP HANA: dimensioni 4 TiB con fino a 704 MiB/s
* Volume di log SAP HANA: dimensioni 0,5 TiB con fino a 256 MiB/s
* SAP HANA volume condiviso: dimensioni 1 TiB con fino a 64 MiB/s
* Volume di backup SAP HANA: dimensioni 4,5 TiB con fino a 256 MiB/s

Il diagramma seguente illustra gli scenari per i volumi SAP HANA:

![Scenari di volume SAP HANA QoS](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>Passaggi successivi

- [Pagina dei prezzi di Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Modello di costi per Azure NetApp Files](azure-netapp-files-cost-model.md) 
- [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
- [Contratto di servizio (SLA) per Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Modificare dinamicamente il livello di servizio di un volume](dynamic-change-volume-service-level.md) 
- [Obiettivi a livello di servizio per la replica tra aree](cross-region-replication-introduction.md#service-level-objectives)
