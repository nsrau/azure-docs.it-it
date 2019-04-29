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
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: c2086eb0c5529d8e570a545e35fc716f70c7541f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691054"
---
# <a name="service-levels-for-azure-netapp-files"></a>Livelli di servizio per Azure NetApp Files
Azure NetApp Files supporta due livelli di servizio: Premium e Standard. 

## <a name="Premium"></a>Archiviazione Premium

L'archiviazione *Premium* offre fino a 64 MiB/s per TiB di velocità effettiva. Le prestazioni di velocità effettiva vengono indicizzate rispetto alla quota del volume. Ad esempio, un volume dell'archiviazione Premium con 2 TiB di quota sottoposta a provisioning (indipendentemente dall'utilizzo effettivo) ha una velocità effettiva di 128 MiB/s.

## <a name="Standard"></a>Archiviazione standard

L'archiviazione *Standard* offre fino a 16 MiB/s per TiB di velocità effettiva. Le prestazioni di velocità effettiva vengono indicizzate rispetto alla quota del volume. Ad esempio, un volume dell'archiviazione Standard con 2 TiB di quota sottoposta a provisioning (indipendentemente dall'utilizzo effettivo) ha una velocità effettiva di 32 MiB/s.

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [pagina dei prezzi di Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) per il prezzo dei diversi livelli di servizio
- [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
