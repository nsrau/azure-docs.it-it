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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 1f9c427045c9d42f6a11cc4bcc798cfc47a4428c
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523114"
---
# <a name="service-levels-for-azure-netapp-files"></a>Livelli di servizio per Azure NetApp Files
I livelli di servizio sono un attributo di un pool di capacità. I livelli di servizio sono definiti e si differenzino solo per la velocità effettiva massima consentita per un volume nel pool di capacità basate sulla quota assegnata al volume.

## <a name="supported-service-levels"></a>Livelli di servizio supportati

File di NetApp Azure supporta tre livelli di servizio: *Extra*, *Premium*, e *Standard*. 

* <a name="Ultra"></a>Archiviazione Ultra

    Il livello di archiviazione extra offre fino a 128 MiB/s di velocità effettiva per ogni 1 TiB di quota di volume assegnata. 

* <a name="Premium"></a>Archiviazione Premium

    Il livello di archiviazione Premium offre fino a 64 MiB/s di velocità effettiva per ogni 1 TiB di quota di volume assegnata. 

* <a name="Standard"></a>Archiviazione standard

    Il livello di archiviazione Standard offre fino a 16 MiB/s di velocità effettiva per ogni 1 TiB di quota di volume assegnata.

## <a name="throughput-limits"></a>Limiti di velocità effettiva

Il limite di velocità effettiva per un volume è determinato dalla combinazione dei fattori seguenti:
* Il livello di servizio del pool di capacità a cui appartiene il volume
* La quota assegnata al volume  

Questo concetto è illustrato nel diagramma seguente:

![Figura a livello di servizio](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

Esempio 1 precedente, un volume da un pool di capacità con il livello di archiviazione Premium che viene assegnato a 2 TiB di quota verrà assegnato un limite di velocità effettiva pari a 128 MiB/s (TiB 2 * 64 MiB/s). Questo scenario si applica indipendentemente dalle dimensioni del pool di capacità o il consumo di volume effettivo.

Nell'esempio 2 precedente, un volume da un pool di capacità con il livello di archiviazione Premium assegnato 100 GiB di quota verrà assegnato un limite di velocità effettiva di 6,25 MiB/s (TiB 0.09765625 * 64 MiB/s). Questo scenario si applica indipendentemente dalle dimensioni del pool di capacità o il consumo di volume effettivo.

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [pagina dei prezzi di Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) per il prezzo dei diversi livelli di servizio
- Visualizzare [modello di costo per i file di Azure NetApp](azure-netapp-files-cost-model.md) per il calcolo del consumo di capacità in un pool di capacità 
- [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)