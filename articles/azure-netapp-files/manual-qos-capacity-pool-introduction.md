---
title: Pool di capacità QoS manuale di Azure NetApp Files | Microsoft Docs
description: Viene fornita un'introduzione al pool di capacità e ai riferimenti manuali di QoS per informazioni aggiuntive.
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
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 14b905c69f0dab933159b414028db3e985d314a3
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935761"
---
# <a name="manual-qos-capacity-pool"></a>Pool di capacità QoS manuale

Questo articolo fornisce un'introduzione alla funzionalità del pool di capacità di qualità del servizio (QoS) manuale.

## <a name="how-manual-qos-differs-from-auto-qos"></a>Differenze tra QoS manuale e QoS automatica

Il [tipo QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) è un attributo di un pool di capacità. Azure NetApp Files fornisce due tipi QoS di pool di capacità, ovvero auto (impostazione predefinita) e manuale.  

In un pool di capacità QoS *manuale* è possibile assegnare la capacità e la velocità effettiva per un volume in modo indipendente. La velocità effettiva totale di tutti i volumi creati con un pool di capacità QoS manuale è limitata dalla velocità effettiva totale del pool. Il suo valore è determinato dalla combinazione della velocità effettiva del livello di servizio e delle dimensioni del pool. 

In un pool di capacità QoS *automatica* , la velocità effettiva viene assegnata automaticamente ai volumi nel pool, proporzionale alla quota di dimensioni assegnata ai volumi.  

Vedere la [gerarchia di archiviazione di Azure NetApp files](azure-netapp-files-understand-storage-hierarchy.md) e [considerazioni sulle prestazioni per Azure NetApp files](azure-netapp-files-performance-considerations.md) per considerazioni sui tipi QoS.

## <a name="how-to-specify-the-manual-qos-type"></a>Come specificare il tipo QoS manuale

Quando si [Crea un pool di capacità](azure-netapp-files-set-up-capacity-pool.md), è possibile specificare affinché il pool di capacità usi il tipo QoS manuale.  È anche possibile [modificare un pool di capacità esistente](manage-manual-qos-capacity-pool.md#change-to-qos) per usare il tipo QoS manuale. 

L'impostazione del tipo di capacità su QoS manuale è una modifica permanente. Non è possibile convertire uno strumento di capacità del tipo QoS manuale in un pool di capacità QoS automatica. 

L'utilizzo del tipo QoS manuale richiede [la registrazione della funzionalità](manage-manual-qos-capacity-pool.md#register-the-feature).  

## <a name="next-steps"></a>Passaggi successivi

* [Gestire un pool di capacità QoS manuale](manage-manual-qos-capacity-pool.md)
* [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
* [Gerarchia di archiviazione](azure-netapp-files-understand-storage-hierarchy.md) 
* [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Considerazioni sulle prestazioni per Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Modello di costi per Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Creare un volume NFS](azure-netapp-files-create-volumes.md)
* [Creare un volume SMB](azure-netapp-files-create-volumes-smb.md)
* [Creare un volume con doppio protocollo](create-volumes-dual-protocol.md)
* [Metriche per Azure NetApp Files](azure-netapp-files-metrics.md)
* [Risolvere i problemi del pool di capacità](troubleshoot-capacity-pools.md)
