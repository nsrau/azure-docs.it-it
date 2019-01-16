---
title: Limiti delle risorse per Azure NetApp Files | Microsoft Docs
description: Descrive i limiti per le risorse di Azure NetApp Files, inclusi i limiti per pool di capacità, volumi e subnet delegata.
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
ms.topic: reference
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: f34afb1df2ae38353f29a80bfb6798c16856dbeb
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54056519"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limiti delle risorse per Azure NetApp Files
Conoscere i limiti delle risorse per Azure NetApp Files è utile per gestire i volumi.

## <a name="capacity_pools"></a>Pool di capacità

- La dimensione minima di un singolo pool di capacità è 4 TiB e la dimensione massima è di 500 TiB. 
- Ogni pool di capacità può appartenere a un solo account di NetApp. Tuttavia, è possibile avere più pool di capacità all'interno di un account di NetApp.  

## <a name="volumes"></a>Volumi

- La dimensione minima di un singolo volume è 100 GiB e la dimensione massima è di 92 TiB.
- È possibile avere un massimo di 100 volumi per ogni sottoscrizione di Azure in ogni area.  

## <a name="delegated_subnet"></a>Subnet delegata 

In ogni rete virtuale di Azure è possibile delegare una sola subnet ad Azure NetApp Files.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
