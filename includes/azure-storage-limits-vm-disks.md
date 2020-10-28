---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 10599b47d55a0b4ea1eb79cda3c31a2479efb7c0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755489"
---
È possibile collegare diversi dischi dati a una macchina virtuale di Azure. In base agli obiettivi di scalabilità e prestazioni per i dischi dati di una macchina virtuale, è possibile determinare il numero e il tipo di disco necessari per soddisfare i requisiti di prestazioni e capacità.

> [!IMPORTANT]
> Per prestazioni ottimali, limitare il numero di dischi a elevato uso collegati alla macchina virtuale per evitare una possibile limitazione. Se i dischi collegati non vengono utilizzati in modo intensivo tutti contemporaneamente, la macchina virtuale può supportarne un numero maggiore.

**Per Azure Managed Disks:**

La tabella seguente illustra i limiti predefiniti e massimi del numero di risorse per area per sottoscrizione. I limiti rimangono invariati indipendentemente dal fatto che i dischi vengano crittografati con chiavi gestite dalla piattaforma o chiavi gestite dal cliente. Non esiste alcun limite per il numero di Managed Disks, snapshot e immagini per gruppo di risorse.  

> | Risorsa | Limite |
> | --- | --- |
> | Dischi gestiti Standard | 50.000 |
> | Dischi gestiti SSD Standard | 50.000 |
> | Dischi gestiti Premium | 50.000 |
> | Snapshot Standard_LRS | 50.000 |
> | Snapshot Standard_ZRS | 50.000 |
> | Immagine gestita | 50.000 |

**Per gli account di archiviazione Standard:** un account di archiviazione Standard con una frequenza totale massima di richieste di 20.000 operazioni di I/O al secondo. Il numero totale di operazioni di I/O al secondo in tutti i dischi della macchina virtuale in un account di archiviazione Standard non può superare questo limite.
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**Per gli account di archiviazione Premium:** un account di archiviazione Premium ha una velocità effettiva totale massima di 50 Gbps. La velocità effettiva totale in tutti i dischi della macchina virtuale non deve superare questo limite.

