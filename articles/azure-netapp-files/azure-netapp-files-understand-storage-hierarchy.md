---
title: Informazioni sulla gerarchia di archiviazione di Azure NetApp Files | Microsoft Docs
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
ms.topic: get-started-article
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: e6792d994c01fb77739ad7a044bc7193e350e173
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053790"
---
# <a name="understand-the-storage-hierarchy-of-azure-netapp-files"></a>Informazioni sulla gerarchia di archiviazione di Azure NetApp Files

Prima di creare un volume in Azure NetApp Files, è necessario acquistare e configurare un pool di capacità di cui è stato effettuato il provisioning.  Per configurare un pool di capacità, è necessario un account di NetApp. Conoscere la gerarchia di archiviazione è utile per configurare e gestire le risorse di Azure NetApp Files.

## <a name="azure_netapp_files_account"></a>Account di NetApp

- Un account di NetApp funge da raggruppamento amministrativo dei pool di capacità che lo costituiscono.  
- Un account di NetApp non equivale all'account di archiviazione di Azure generale. 
- Un account di NetApp ha un ambito a livello di area.   
- È possibile avere più account di NetApp in un'area, ma ogni account di NetApp è associato a una singola area.

## <a name="capacity_pools"></a>Pool di capacità

- Un pool di capacità viene misurato in base alla rispettiva capacità di cui è stato effettuato il provisioning.  
- Il provisioning della capacità viene effettuato in base agli SKU fissi acquistati, ad esempio, una capacità di 4 TiB.
- La dimensione minima di un singolo pool di capacità è 4 TiB e la dimensione massima è di 500 TiB. 
- Un pool di capacità può avere un solo livello di servizio.  
  Attualmente è disponibile solo il livello di servizio Premium.
- Ogni pool di capacità può appartenere a un solo account di NetApp. Tuttavia, è possibile avere più pool di capacità all'interno di un account di NetApp.  
- Un pool di capacità non può essere spostato tra account di NetApp.   
  Ad esempio, nel [diagramma concettuale della gerarchia di archiviazione](#conceptual_diagram_of_storage_hierarchy) di seguito, il pool di capacità 1 non può essere spostato dall'account di NetApp di Stati Uniti orientali all'account di NetApp di Stati Uniti occidentali 2.  

## <a name="volumes"></a>Volumi

- Un volume viene misurato in base all'utilizzo della capacità logica ed è scalabile. La dimensione minima di un singolo volume è 100 GiB e la dimensione massima è di 92 TiB.
- L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool.
-   È possibile avere un massimo di 100 volumi per ogni sottoscrizione di Azure in ogni area. 
- Ogni volume appartiene a un solo pool, ma un pool può contenere più volumi. 
- Nello stesso account di NetApp è possibile spostare un volume tra i pool.    
  Ad esempio, nel [diagramma concettuale della gerarchia di archiviazione](#conceptual_diagram_of_storage_hierarchy) di seguito, è possibile spostare i volumi dal pool di capacità 1 al pool di capacità 2.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Diagramma concettuale della gerarchia di archiviazione 
L'esempio seguente mostra le relazioni tra sottoscrizione di Azure, account di NetApp, pool di capacità e volumi.   

![Diagramma concettuale della gerarchia di archiviazione](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Passaggi successivi

- [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Effettuare la registrazione ad Azure NetApp Files](azure-netapp-files-register.md)
