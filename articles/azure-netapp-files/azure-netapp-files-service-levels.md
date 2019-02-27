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
ms.topic: concepts
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: a10ac319fe362011531e00f832bb8e471fd56fdf
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56431075"
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
