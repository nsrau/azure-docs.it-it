---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334768"
---
È possibile collegare un numero di dischi dati a una macchina virtuale di Azure.You can attach a number of data disks to an Azure virtual machine. In base agli obiettivi di scalabilità e prestazioni per i dischi dati di una macchina virtuale, è possibile determinare il numero e il tipo di disco necessari per soddisfare i requisiti di prestazioni e capacità.

> [!IMPORTANT]
> Per prestazioni ottimali, limitare il numero di dischi a elevato uso collegati alla macchina virtuale per evitare una possibile limitazione. Se tutti i dischi collegati non sono altamente utilizzati contemporaneamente, la macchina virtuale può supportare un numero maggiore di dischi.

**Per i dischi gestiti di Azure:For Azure managed disks:**

Nella tabella seguente vengono illustrati i limiti predefiniti e massimi del numero di risorse per area per sottoscrizione. Non esiste alcun limite per il numero di dischi gestiti, snapshot e immagini per gruppo di risorse.  

> | Risorsa | Limite |
> | --- | --- |
> | Dischi gestiti Standard | 50.000 |
> | Dischi gestiti SSD standard | 50.000 |
> | Dischi gestiti Premium | 50.000 |
> | Standard_LRS istantanee | 50.000 |
> | Standard_ZRS istantanee | 50.000 |
> | Immagine gestita | 50.000 |

* **Per gli account di archiviazione standard:For Standard storage accounts:** Un account di archiviazione Standard ha una frequenza di richieste totali massima di 20.000 IOPS. Il numero totale di operazioni di I/O al secondo in tutti i dischi della macchina virtuale in un account di archiviazione Standard non deve superare questo limite.
  
    È possibile calcolare approssimativamente il numero di dischi altamente utilizzati supportati da un singolo account di archiviazione Standard in base al limite di frequenza delle richieste. Ad esempio, per una macchina virtuale di livello Basic, il numero massimo di dischi altamente utilizzati è di circa 66, ovvero 20.000/300 IOPS per disco. Il numero massimo di dischi altamente utilizzati per una macchina virtuale di livello Standard è di circa 40, ovvero 20.000/500 IOPS per disco. 

* **Per gli account di archiviazione Premium:For Premium storage accounts:** Un account di archiviazione Premium ha una velocità effettiva totale massima di 50 Gbps. La velocità effettiva totale in tutti i dischi della macchina virtuale non deve superare questo limite.

