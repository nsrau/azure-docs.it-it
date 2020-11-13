---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: ffb07220267a2c192b4aad2405185c80bd9abbc0
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523801"
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
  
È possibile calcolare approssimativamente il numero di dischi a utilizzo elevato supportato da un singolo account di archiviazione Standard in base al limite della frequenza di richieste. Per una macchina virtuale di livello Basic, ad esempio, il numero massimo di dischi a utilizzo elevato è pari a circa 66, ovvero 20.000/300 operazioni di I/O al secondo per disco. Il numero massimo di dischi a utilizzo elevato per una macchina virtuale di livello Standard è pari a circa 40, ovvero 20.000/500 operazioni di I/O al secondo per disco. 

**Per gli account di archiviazione Premium:** un account di archiviazione Premium ha una velocità effettiva totale massima di 50 Gbps. La velocità effettiva totale in tutti i dischi della macchina virtuale non deve superare questo limite.

