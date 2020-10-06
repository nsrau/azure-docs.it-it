---
title: Gerarchia di archiviazione di Azure NetApp Files | Microsoft Docs
description: Descrive la gerarchia di archiviazione, inclusi gli account, i pool di capacità e i volumi di Azure NetApp Files.
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
ms.topic: overview
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 435d74e771a9d887c87c9d10e6b525ac77cf97e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278329"
---
# <a name="storage-hierarchy-of-azure-netapp-files"></a>Gerarchia di archiviazione di Azure NetApp Files

Prima di creare un volume in Azure NetApp Files, è necessario acquistare e configurare un pool di capacità di cui è stato effettuato il provisioning.  Per configurare un pool di capacità, è necessario un account di NetApp. Conoscere la gerarchia di archiviazione è utile per configurare e gestire le risorse di Azure NetApp Files.

> [!IMPORTANT] 
> Azure NetApp Files attualmente non supporta la migrazione delle risorse tra sottoscrizioni.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>Account di NetApp

- Un account di NetApp funge da raggruppamento amministrativo dei pool di capacità che lo costituiscono.  
- Un account di NetApp non equivale all'account di archiviazione di Azure generale. 
- Un account di NetApp ha un ambito a livello di area.   
- È possibile avere più account di NetApp in un'area, ma ogni account di NetApp è associato a una singola area.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Pool di capacità

Comprendendo il funzionamento dei pool di capacità, sarà possibile scegliere i tipi di pool di capacità appropriati per le esigenze di archiviazione. 

### <a name="general-rules-of-capacity-pools"></a>Regole generali dei pool di capacità

- Un pool di capacità viene misurato in base alla rispettiva capacità di cui è stato effettuato il provisioning.   
    Per altre informazioni, vedere [Tipi QoS](#qos_types).  
- Il provisioning della capacità viene effettuato in base agli SKU fissi acquistati, ad esempio, una capacità di 4 TiB.
- Un pool di capacità può avere un solo livello di servizio.  
- Ogni pool di capacità può appartenere a un solo account di NetApp. Tuttavia, è possibile avere più pool di capacità all'interno di un account di NetApp.  
- Un pool di capacità non può essere spostato tra account di NetApp.   
  Ad esempio, nel [diagramma concettuale della gerarchia di archiviazione](#conceptual_diagram_of_storage_hierarchy) di seguito, il pool di capacità 1 non può essere spostato dall'account di NetApp di Stati Uniti orientali all'account di NetApp di Stati Uniti occidentali 2.  
- Non è possibile eliminare un pool di capacità prima che siano stati eliminati tutti i volumi in esso contenuti.

### <a name="quality-of-service-qos-types-for-capacity-pools"></a><a name="qos_types"></a>Tipi di Qualità del servizio (QoS) per i pool di capacità

Il tipo QoS è un attributo di un pool di capacità. Azure NetApp Files fornisce due tipi QoS dei pool di capacità. 

- Tipo QoS *automatico*  

    Quando si crea un pool di capacità, il tipo QoS predefinito è automatico.

    In un pool di capacità QoS automatico la velocità effettiva viene assegnata automaticamente ai volumi nel pool, proporzionalmente alla quota di dimensioni assegnata ai volumi. 

    La velocità effettiva massima allocata a un volume dipende dal livello di servizio del pool di capacità e dalla quota di dimensioni del volume. Per un calcolo di esempio, vedere [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md).

- Tipo QoS <a name="manual_qos_type"></a>*manuale*  

     > [!IMPORTANT] 
     > Per usare il tipo QoS manuale per un pool di capacità è necessaria la registrazione.  Vedere [Gestire un pool di capacità QoS manuale](manage-manual-qos-capacity-pool.md).  

    È possibile usare il tipo QoS manuale per un pool di capacità.

    In un pool di capacità QoS manuale è possibile assegnare la capacità e la velocità effettiva per un volume in modo indipendente. La velocità effettiva totale di tutti i volumi creati con un pool di capacità QoS manuale è limitata dalla velocità effettiva totale del pool.  Il suo valore è determinato dalla combinazione della velocità effettiva del livello di servizio e delle dimensioni del pool. 

    Un pool con capacità di 4 TiB con il livello di servizio Ultra ha ad esempio una capacità di velocità effettiva totale di 512 MiB/s (4 TiB x 128 MiB/s/TiB) disponibile per i volumi.


## <a name="volumes"></a><a name="volumes"></a>Volumi

- Un volume viene misurato in base all'utilizzo della capacità logica ed è scalabile. 
- L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool.
- L'utilizzo della velocità effettiva di un volume concorre al calcolo della velocità effettiva disponibile del pool. Vedere [Tipo QoS manuale](#manual_qos_type).
- Ogni volume appartiene a un solo pool, ma un pool può contenere più volumi. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Diagramma concettuale della gerarchia di archiviazione 
L'esempio seguente mostra le relazioni tra sottoscrizione di Azure, account di NetApp, pool di capacità e volumi.   

![Diagramma concettuale della gerarchia di archiviazione](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Passaggi successivi

- [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Effettuare la registrazione ad Azure NetApp Files](azure-netapp-files-register.md)
- [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Considerazioni sulle prestazioni per Azure NetApp Files](azure-netapp-files-performance-considerations.md)
- [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
- [Gestire un pool di capacità QoS manuale](manage-manual-qos-capacity-pool.md)
