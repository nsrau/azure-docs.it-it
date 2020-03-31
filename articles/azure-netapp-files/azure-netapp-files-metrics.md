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
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: c8e3b616dee1ab4e6bb6e77c6a8bab5661d4e20b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460433"
---
# <a name="metrics-for-azure-netapp-files"></a>Metriche per Azure NetApp Files

File NetApp di Azure offre metriche sull'archiviazione allocata, l'utilizzo effettivo dello spazio di archiviazione, le operazioni di I/O al secondo del volume e la latenza. L'analisi di queste metriche consente di comprendere meglio il modello di utilizzo e le prestazioni del volume degli account NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metriche di utilizzo per i pool di capacità

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Pool allocato alle dimensioni del volume*  
    Il totale della quota di volume (GiB) in un determinato pool di capacità, ovvero il totale delle dimensioni dei volumi di cui è stato eseguito il provisioning nel pool di capacità.  
    Questa dimensione è la dimensione selezionata durante la creazione del volume.  
- *Dimensioni del pool utilizzate*  
    Totale dello spazio logico (GiB) utilizzato tra i volumi in un pool di capacità.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metriche di utilizzo per i volumi

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Dimensioni consumate volume*   
    Lo spazio logico totale utilizzato in un volume (GiB).  
    Queste dimensioni includono lo spazio logico usato dai file system e gli snapshot attivi.  
- *Dimensioni dell'istantanea del volume*   
   Spazio logico incrementale utilizzato dagli snapshot in un volume.  

## <a name="performance-metrics-for-volumes"></a>Metriche delle prestazioni per i volumiPerformance metrics for volumes

- *MediaReadLatency*   
    Tempo medio per le letture dal volume in millisecondi.
- *MediaWriteLatency*   
    Tempo medio per le scritture dal volume in millisecondi.
- *Operazioni di Lettura*   
    Numero di letture nel volume al secondo.
- *Operazioni di WriteIop*   
    Numero di scritture nel volume al secondo.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
* [Creare un volume per Azure NetApp Files](azure-netapp-files-create-volumes.md)
