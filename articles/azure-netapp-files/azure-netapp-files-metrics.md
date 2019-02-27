---
title: Metriche per Azure NetApp Files | Microsoft Docs
description: Questo articolo descrive le metriche per Azure NetApp Files.
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 866aa808f4706fa3bce72495dc56f438d567ecb2
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430945"
---
# <a name="metrics-for-azure-netapp-files"></a>Metriche per Azure NetApp Files

Azure NetApp Files fornisce metriche relative a spazio di archiviazione allocato, utilizzo effettivo delle risorse di archiviazione, velocità effettiva del volume, operazioni di I/O al secondo e latenza. L'analisi di queste metriche consente di comprendere meglio il modello di utilizzo e le prestazioni del volume degli account NetApp.  

## <a name="capacity_pools"></a>Metriche di utilizzo per i pool di capacità

- *Dimensioni allocate del pool di volumi*  
    Dimensioni (GiB) del pool di capacità di cui è stato effettuato il provisioning.  
- *Dimensioni usate del pool di volumi*  
    Totale della quota del volume (GiB) in un determinato pool di capacità (ossia, totale delle dimensioni dei volumi di cui è stato effettuato il provisioning nel pool di capacità). Corrisponde alle dimensioni selezionate durante la creazione del volume.  
- *Dimensioni logiche totale del pool di volumi*  
    Totale dello spazio logico (GiB) usato nei diversi volumi in un pool di capacità.  
- *Dimensioni totali degli snapshot del pool di volumi*  
    Totale dello spazio logico incrementale usato dagli snapshot.  

## <a name="volumes"></a>Metriche di utilizzo per i volumi

- *Dimensioni allocate del volume*   
    Dimensioni del volume (quota) di cui è stato effettuato il provisioning in GiB.  
- *Dimensioni logiche del volume*   
    Totale dello spazio logico usato in un volume (GiB). Queste dimensioni includono lo spazio logico usato dai file system e gli snapshot attivi.  
- *Dimensioni dello snapshot del volume*   
    Spazio logico incrementale usato dagli snapshot in un volume.  

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
* [Creare un volume per Azure NetApp Files](azure-netapp-files-create-volumes.md)
