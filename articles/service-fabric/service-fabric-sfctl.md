---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando di Service Fabric sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: b6e3c769307f037c9f31dbb3f1a86675145ec1f6
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495408"
---
# <a name="sfctl"></a>sfctl
Comandi per la gestione di cluster ed entità di Service Fabric. Questa versione è compatibile con il runtime di Service Fabric 6.3.

I comandi seguono il modello sostantivo-verbo. Per altre informazioni, vedere Sottogruppi.

## <a name="subgroups"></a>Sottogruppi
|Sottogruppo|DESCRIZIONE|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | Consente di creare, eliminare e gestire le applicazioni e i tipi di applicazioni. |
| [chaos](service-fabric-sfctl-chaos.md) | Consente di avviare, arrestare e creare report nel servizio di test chaos. |
| [cluster](service-fabric-sfctl-cluster.md) | Selezionare, gestire e usare dei cluster di Service Fabric. |
| [compose](service-fabric-sfctl-compose.md) | Creare, eliminare e gestire applicazioni Docker Compose. |
| [container](service-fabric-sfctl-container.md) | Esegue i comandi relativi al contenitore in un nodo del cluster. |
| [is](service-fabric-sfctl-is.md) | Consente di eseguire query e inviare comandi al servizio di infrastruttura. |
| [node](service-fabric-sfctl-node.md) | Consente di gestire i nodi che formano un cluster. |
| [partition](service-fabric-sfctl-partition.md) | Consente di eseguire una query e gestire partizioni per qualsiasi servizio. |
| [property](service-fabric-sfctl-property.md) | Consente di archiviare proprietà ed eseguire query su di esse con i nomi di Service Fabric. |
| [replica](service-fabric-sfctl-replica.md) | Consente di gestire le repliche che appartengono alle partizioni del servizio. |
| [rpm](service-fabric-sfctl-rpm.md) | Consente di eseguire query e inviare comandi al servizio di gestione di ripristino. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Gestisce i cluster di Service Fabric autonomi. |
| [service](service-fabric-sfctl-service.md) | Creare, eliminare e gestire servizi, tipi di servizio e pacchetti di servizi. |
| [store](service-fabric-sfctl-store.md) | Esegue operazioni base a livello di file nell'archivio immagini del cluster. |

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).