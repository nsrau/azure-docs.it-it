---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: aa701ada917811382351fee9469a5cfa9a7599b8
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279719"
---
Una macchina virtuale di Azure supporta il collegamento di un numero di dischi dati. In questo articolo vengono descritti gli obiettivi di scalabilità e prestazioni per i dischi dati di una macchina virtuale. Usare questi obiettivi per stabilire il numero e il tipo di dischi necessari per soddisfare i requisiti di capacità e prestazioni. 

> [!IMPORTANT]
> Per prestazioni ottimali, limitare il numero di dischi a elevato uso collegati alla macchina virtuale per evitare una possibile limitazione. Se l'uso di tutti i dischi collegati non è elevato nello stesso momento, la macchina virtuale può supportare un numero maggiore di dischi.

* **Per Azure Managed Disks:** 

> | Risorsa | Limite predefinito | Limite massimo |
> | --- | --- | --- |
> | Managed Disks Standard | 10.000 | 50.000 |
> | Managed Disks - SSD Standard | 10.000 | 50.000 |
> | Managed Disks Premium | 10.000 | 50.000 |
> | Snapshot Standard_LRS | 10.000 | 50.000 |
> | Snapshot Standard_ZRS | 10.000 | 50.000 |
> | Immagine gestita | 10.000 | 50.000 |

* **Per gli account di archiviazione standard:** un account di archiviazione standard con una frequenza totale massima di richieste di 20.000 IOPS. Il numero totale di IOPS in tutti i dischi della macchina virtuale in un account di archiviazione standard non deve superare questo limite.
  
    È possibile calcolare approssimativamente il numero di dischi a elevato utilizzo supportato da un singolo account di archiviazione standard in base al limite della frequenza di richieste. Per una VM di livello Basic, ad esempio, il numero massimo di dischi a elevato uso è pari a circa 66 (20.000/300 IOPS per disco) e per una macchina virtuale di livello Standard è pari a circa 40 (20.000/500 IOPS per disco). 

* **Per gli account di archiviazione Premium:** un account di archiviazione Premium ha una velocità effettiva totale massima di 50 Gbps. La velocità effettiva totale in tutti i dischi della macchina virtuale non deve superare questo limite.

