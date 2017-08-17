---
title: Canale operativo di Service Fabric di Azure | Microsoft Docs
description: Un elenco completo dei log generati da Service Fabric nel canale operativo dei cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: dekapur
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 934719868ab9968db352db2b440014d35dbc0274
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="operational-channel"></a>Canale operativo 

Il canale operativo è costituito da log di azioni di alto livello eseguite da Service Fabric sui nodi e il cluster. Quando l'opzione "Diagnostica" è abilitata per un cluster, l'agente di Diagnostica di Azure viene distribuito nel cluster e per impostazione predefinita viene configurato per la lettura nei log dal canale operativo. Altre informazioni sulla configurazione dell'[agente di Diagnostica di Azure](service-fabric-diagnostics-event-aggregation-wad.md) per modificare la configurazione della diagnostica del cluster in modo da prelevare più log o contatori delle prestazioni. 

## <a name="operational-channel-logs"></a>Log del canale operativo 

Di seguito è riportato un elenco completo dei log forniti da Service Fabric nel canale operativo. 

| EventId | Nome | Origine (attività) | Level |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informazioni |
| 25621 | NodeOpenedSuccess | FabricNode | Informazioni |
| 25622 | NodeOpenedFailed | FabricNode | Informazioni |
| 25623 | NodeClosing | FabricNode | Informazioni |
| 25624 | NodeClosed | FabricNode | Informazioni |
| 25625 | NodeAborting | FabricNode | Informazioni |
| 25626 | NodeAborted | FabricNode | Informazioni |
| 29627 | ClusterUpgradeStart | CM | Informazioni |
| 29628 | ClusterUpgradeComplete | CM | Informazioni |
| 29629 | ClusterUpgradeRollback | CM | Informazioni |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informazioni |
| 29631 | ClusterUpgradeDomainComplete | CM | Informazioni |
| 23074 | ContainerActivated | Hosting | Informazioni |
| 23075 | ContainerDeactivated | Hosting | Informazioni |
| 29620 | ApplicationCreated | CM | Informazioni |
| 29621 | ApplicationUpgradeStart | CM | Informazioni |
| 29622 | ApplicationUpgradeComplete | CM | Informazioni |
| 29623 | ApplicationUpgradeRollback | CM | Informazioni |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informazioni |
| 29625 | ApplicationDeleted | CM | Informazioni |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informazioni |
| 18566 | ServiceCreated | FM | Informazioni |
| 18567 | ServiceDeleted | FM | Informazioni |

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul processo globale di [generazione di eventi a livello piattaforma](service-fabric-diagnostics-event-generation-infra.md) in Service Fabric
* Modifica della configurazione di [Diagnostica di Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md) per raccogliere più log
* [Configurazione di Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) per visualizzare i log del canale operativo

