---
title: Metriche per Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files fornisce la metrica sull'archiviazione allocata, sull'utilizzo effettivo dello spazio di archiviazione, sul volume IOPS e sulla latenza. Usare queste metriche per comprendere l'utilizzo e le prestazioni.
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
ms.date: 09/10/2020
ms.author: b-juche
ms.openlocfilehash: 1690a844ff700a2975be8e972fd90ba71eeb937c
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707782"
---
# <a name="metrics-for-azure-netapp-files"></a>Metriche per Azure NetApp Files

Azure NetApp Files fornisce la metrica sull'archiviazione allocata, sull'utilizzo effettivo dello spazio di archiviazione, sul volume IOPS e sulla latenza. L'analisi di queste metriche consente di comprendere meglio il modello di utilizzo e le prestazioni del volume degli account NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metriche di utilizzo per i pool di capacità

- *Dimensioni allocate pool*   
    Dimensioni del pool di cui è stato effettuato il provisioning.

- *Pool allocato alle dimensioni del volume*  
    Totale della quota del volume (GiB) in un determinato pool di capacità, ovvero il totale delle dimensioni di provisioning dei volumi nel pool di capacità.  
    Questa dimensione corrisponde alla dimensione selezionata durante la creazione del volume.  

- *Dimensioni utilizzate pool*  
    Totale dello spazio logico (GiB) usato tra i volumi in un pool di capacità.  

- *Dimensioni totali dello snapshot del pool*    
    Somma delle dimensioni dello snapshot di tutti i volumi nel pool.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metriche di utilizzo per i volumi

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Dimensioni utilizzate del volume*   
    Lo spazio logico totale usato in un volume (GiB).  
    Queste dimensioni includono lo spazio logico usato dai file system e gli snapshot attivi.  
- *Dimensioni snapshot del volume*   
   Lo spazio logico incrementale usato dagli snapshot in un volume.  

## <a name="performance-metrics-for-volumes"></a>Metriche delle prestazioni per i volumi

- *Latenza di lettura media*   
    Tempo medio per le letture dal volume in millisecondi.
- *Latenza di scrittura media*   
    Tempo medio per le scritture dal volume in millisecondi.
- *IOPS lettura*   
    Numero di letture del volume al secondo.
- *IOPS di scrittura*   
    Numero di scritture nel volume al secondo.

## <a name="volume-replication-metrics"></a><a name="replication"></a>Metriche di replica del volume

- *Stato replica del volume integro*   
    Condizione della relazione di replica. 

- *Trasferimento della replica del volume*    
    Indica se lo stato della replica del volume è' Transfer '. 
 
- *Tempo di ritardo della replica del volume*   
    Quantità di tempo, in secondi, in base alla quale i dati nel mirror sono in ritardo rispetto all'origine. 

- *Durata ultimo trasferimento replica volume*   
    Quantità di tempo in secondi impiegato per il completamento dell'ultimo trasferimento. 

- *Dimensioni ultimo trasferimento replica volume*    
    Numero totale di byte trasferiti come parte dell'ultimo trasferimento. 

- *Stato della replica del volume*    
    Quantità totale di dati trasferiti per l'operazione di trasferimento corrente. 

- *Trasferimento totale replica volume*   
    Byte cumulativi trasferiti per la relazione. 

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
* [Creare un volume per Azure NetApp Files](azure-netapp-files-create-volumes.md)
