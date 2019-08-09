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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 7cf382f511d2ba8452d77bf207f36b749cb31e94
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848798"
---
# <a name="metrics-for-azure-netapp-files"></a>Metriche per Azure NetApp Files

Azure NetApp Files fornisce metriche relative a spazio di archiviazione allocato, utilizzo effettivo delle risorse di archiviazione, velocità effettiva del volume, operazioni di I/O al secondo e latenza. L'analisi di queste metriche consente di comprendere meglio il modello di utilizzo e le prestazioni del volume degli account NetApp.  

## <a name="capacity_pools"></a>Metriche di utilizzo per i pool di capacità

<!-- 
- *Volume pool allocated size*  
    The size (GiB) of the provisioned capacity pool  
--> 
- *Dimensioni usate del pool di volumi*  
    Totale della quota del volume (GiB) in un determinato pool di capacità, ovvero il totale delle dimensioni di provisioning dei volumi nel pool di capacità.  
    Corrisponde alle dimensioni selezionate durante la creazione del volume.  
- *Dimensioni logiche totale del pool di volumi*  
    Totale dello spazio logico (GiB) usato tra i volumi in un pool di capacità  
<!-- 
- *Volume pool total snapshot size*  
    The total of incremental logical space used by the snapshots  
-->

## <a name="volumes"></a>Metriche di utilizzo per i volumi

<!-- 
- *Volume allocated size*   
    The volume size (quota) provisioned in GiB  
--> 
- *Dimensioni logiche del volume*   
    Lo spazio logico totale usato in un volume (GiB)  
    Queste dimensioni includono lo spazio logico usato dai file system e gli snapshot attivi.  
- *Dimensioni dello snapshot del volume*   
   Lo spazio logico incrementale usato dagli snapshot in un volume  

## <a name="performance-metrics-for-volumes"></a>Metriche delle prestazioni per i volumi

- *AverageReadLatency*   
    Tempo medio per le letture dal volume in millisecondi
- *AverageWriteLatency*   
    Tempo medio per le scritture dal volume in millisecondi
- *ReadIops*   
    Numero di letture al volume al secondo
- *WriteIops*   
    Numero di scritture nel volume al secondo

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
* [Creare un volume per Azure NetApp Files](azure-netapp-files-create-volumes.md)
