---
title: Gestire le risorse create durante il processo di spostamento della macchina virtuale in Azure Resource Mover
description: Informazioni su come gestire le risorse create durante il processo di spostamento della macchina virtuale in Azure Resource Mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 6d6f56fb47bfdaadc6704e2a13ebcf0e893b8b1c
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602354"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Gestire le risorse create per lo spostamento della macchina virtuale

Questo articolo descrive come gestire le risorse create in modo esplicito da [Azure Resource Mover](overview.md) per facilitare il processo di spostamento della macchina virtuale. 

Dopo aver spostato le macchine virtuali in più aree, è necessario pulire manualmente alcune risorse create da Resource Mover.

## <a name="delete-resources-created-for-vm-move"></a>Elimina le risorse create per lo spostamento di macchine virtuali

Eliminare manualmente la raccolta di spostamento e Site Recovery risorse create per lo spostamento della macchina virtuale.

1. Esaminare le risorse nel gruppo di risorse ```ResourceMoverRG-<sourceregion>-<target-region>``` .
2. Verificare che la macchina virtuale e tutte le altre risorse di origine nella raccolta di spostamento siano state spostate o eliminate. per assicurarsi che non vi siano risorse in sospeso che le usano.
2. Eliminare queste risorse.

    - Il nome della raccolta di spostamento è ```movecollection-<sourceregion>-<target-region>```.
    - Il nome dell'account di archiviazione cache è ```resmovecache<guid>```.
    - Il nome dell'insieme di credenziali è ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## <a name="next-steps"></a>Passaggi successivi

Provare a [trasferire una macchina virtuale](tutorial-move-region-virtual-machines.md) in un'altra area con il motore di risorse.