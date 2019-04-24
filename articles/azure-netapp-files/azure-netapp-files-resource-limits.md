---
title: Limiti delle risorse per Azure NetApp Files | Microsoft Docs
description: Descrive i limiti per le risorse di Azure NetApp Files, inclusi i limiti per account NetApp, pool di capacità, volumi, snapshot e la subnet delegata.
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
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 897ca26bcbb05287d33a4fb8e731ca959e39e271
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452637"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limiti delle risorse per Azure NetApp Files

Conoscere i limiti delle risorse per Azure NetApp Files è utile per gestire i volumi.

- Ogni sottoscrizione di Azure può avere fino a 10 account NetApp.
- Ogni account NetApp può avere fino a 25 pool di capacità.
- Ogni pool di capacità può appartenere a un solo account di NetApp.  
- La dimensione minima di un singolo pool di capacità è 4 TiB e la dimensione massima è di 500 TiB. 
- Ogni pool di capacità può avere fino a 500 volumi.
- La dimensione minima di un singolo volume è 100 GiB e la dimensione massima è di 92 TiB.
- Ogni volume può avere fino a 255 snapshot.
- Ogni rete virtuale di Azure può avere una sola subnet delegata ad Azure NetApp Files.

**Passaggi successivi**

[Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
