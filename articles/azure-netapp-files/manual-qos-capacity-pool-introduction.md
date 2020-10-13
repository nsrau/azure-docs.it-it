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
ms.date: 10/12/2020
ms.author: b-juche
ms.openlocfilehash: 239b985bf45942afbff8ca49d7e15ead24456304
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993982"
---
# <a name="manual-qos-capacity-pool"></a>Pool di capacità QoS manuale

Questo articolo fornisce un'introduzione alla funzionalità del pool di capacità di qualità del servizio (QoS) manuale.

## <a name="how-manual-qos-differs-from-auto-qos"></a>Differenze tra QoS manuale e QoS automatica

Il [tipo QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) è un attributo di un pool di capacità. Azure NetApp Files fornisce due tipi QoS di pool di capacità, ovvero auto (impostazione predefinita) e manuale.  

In un pool di capacità QoS *manuale* è possibile assegnare la capacità e la velocità effettiva per un volume in modo indipendente. La velocità effettiva totale di tutti i volumi creati con un pool di capacità QoS manuale è limitata dalla velocità effettiva totale del pool. Il suo valore è determinato dalla combinazione della velocità effettiva del livello di servizio e delle dimensioni del pool. 

In un pool di capacità QoS *automatica* , la velocità effettiva viene assegnata automaticamente ai volumi nel pool, proporzionale alla quota di dimensioni assegnata ai volumi.  

Vedere la [gerarchia di archiviazione di Azure NetApp files](azure-netapp-files-understand-storage-hierarchy.md) e [considerazioni sulle prestazioni per Azure NetApp files](azure-netapp-files-performance-considerations.md) per considerazioni sui tipi QoS.

## <a name="example-of-using-manual-qos"></a>Esempio di uso di QoS manuale

Quando si usa un pool di capacità QoS manuale con, ad esempio, un sistema di SAP HANA, un database Oracle o altri carichi di lavoro che richiedono più volumi, è possibile usare il pool di capacità per creare questi volumi dell'applicazione.  Ogni volume può fornire le singole dimensioni e la velocità effettiva per soddisfare i requisiti dell'applicazione.  Per informazioni dettagliate sui vantaggi, vedere [esempi di limiti di velocità effettiva dei volumi in un pool di capacità QoS manuale](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool) .  

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
