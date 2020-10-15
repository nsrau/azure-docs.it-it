---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 9805f53d5901226fc9e32b24a323256cd1da6844
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88655017"
---
Di seguito vengono indicate le dimensioni degli oggetti Azure che possono essere scritti. Verificare che tutti i file caricati siano conformi a questi limiti.

| Tipo di oggetto di Azure | Limite predefinito                                             |
|-------------------|-----------------------------------------------------------|
| BLOB in blocchi        | Circa 4,75 TiB                                                 |
| BLOB di pagine         | 8 TiB <br> Le dimensioni di tutti i file caricati nel formato BLOB di pagine devono essere multipli integrali di 512 byte per garantire che il caricamento venga completato in modo corretto. <br> I file VHD e VHDX sono allineati a 512 byte. |
| File di Azure        | 1 TiB                                                      |
| Dischi gestiti     | 4 TiB <br> Per ulteriori informazioni sulle dimensioni e sui limiti, vedere: <li>[Obiettivi di scalabilità di SSD standard](../articles/virtual-machines/disks-types.md#standard-ssd)</li><li>[Obiettivi di scalabilità di unità SSD Premium](../articles/virtual-machines/disks-types.md#standard-hdd)</li><li>[Obiettivi di scalabilità di HDD standard](../articles/virtual-machines/disks-types.md#premium-ssd)</li><li>[Prezzi e fatturazione di Managed Disks](../articles/virtual-machines/disks-types.md#billing)</li>  
