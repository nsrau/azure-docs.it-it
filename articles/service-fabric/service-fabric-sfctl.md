---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando di Service Fabric sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: c195d0c4250022102e735cf584370278e354bc41
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60545045"
---
# <a name="sfctl"></a>sfctl
Comandi per la gestione di cluster ed entità di Service Fabric. Questa versione è compatibile con il runtime di Service Fabric 6.4.

I comandi seguono il modello sostantivo-verbo. Per altre informazioni, vedere Sottogruppi.

## <a name="subgroups"></a>Sottogruppi
|Sottogruppo|Descrizione|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | Consente di creare, eliminare e gestire le applicazioni e i tipi di applicazioni. |
| [chaos](service-fabric-sfctl-chaos.md) | Consente di avviare, arrestare e creare report nel servizio di test chaos. |
| [cluster](service-fabric-sfctl-cluster.md) | Selezionare, gestire e usare dei cluster di Service Fabric. |
| [compose](service-fabric-sfctl-compose.md) | Creare, eliminare e gestire applicazioni Docker Compose. |
| [container](service-fabric-sfctl-container.md) | Esegue i comandi relativi al contenitore in un nodo del cluster. |
| [is](service-fabric-sfctl-is.md) | Consente di eseguire query e inviare comandi al servizio di infrastruttura. |
| [mesh](service-fabric-sfctl-mesh.md) | Consente di eliminare e gestire le applicazioni mesh Service Fabric. |
| [node](service-fabric-sfctl-node.md) | Consente di gestire i nodi che formano un cluster. |
| [partition](service-fabric-sfctl-partition.md) | Consente di eseguire una query e gestire partizioni per qualsiasi servizio. |
| [property](service-fabric-sfctl-property.md) | Consente di archiviare proprietà ed eseguire query su di esse con i nomi di Service Fabric. |
| [replica](service-fabric-sfctl-replica.md) | Consente di gestire le repliche che appartengono alle partizioni del servizio. |
| [rpm](service-fabric-sfctl-rpm.md) | Consente di eseguire query e inviare comandi al servizio di gestione di ripristino. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Gestisce i cluster di Service Fabric autonomi. |
| [service](service-fabric-sfctl-service.md) | Creare, eliminare e gestire servizi, tipi di servizio e pacchetti di servizi. |
| [impostazioni](service-fabric-sfctl-settings.md) | Consente di configurare le impostazioni locali per l'istanza di sfctl. |
| [store](service-fabric-sfctl-store.md) | Esegue operazioni base a livello di file nell'archivio immagini del cluster. |

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).