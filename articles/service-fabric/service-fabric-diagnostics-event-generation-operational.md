---
title: Elenco degli eventi di Azure Service Fabric | Microsoft Docs
description: Elenco completo degli eventi disponibili in Azure Service Fabric per monitorare i cluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 529df0147d2563c62c4a9578e47184bd98b01761
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212960"
---
# <a name="list-of-service-fabric-events"></a>Elenco degli eventi di Service Fabric 

Service Fabric espone un set primario di eventi del cluster, sotto forma di [eventi di Service Fabric](service-fabric-diagnostics-events.md), per offrire informazioni sullo stato del cluster. Questi eventi sono basati sulle azioni eseguite da Service Fabric sui nodi e sul cluster oppure sulle decisioni di gestione prese dal proprietario/operatore del cluster e sono accessibili eseguendo query su [EventStore](service-fabric-diagnostics-eventstore.md) nel cluster oppure tramite il canale operativo. Nei computer Windows, il canale operativo è collegato anche al registro eventi ed è quindi possibile visualizzare gli eventi di Service Fabric nel Visualizzatore eventi. 

>[!NOTE]
>Per un elenco degli eventi di Service Fabric per i cluster nelle versioni precedenti alla versione 6.2, vedere la sezione successiva. 

Di seguito è riportato un elenco di tutti gli eventi disponibili nella piattaforma, ordinati in base all'entità a cui sono associati.

## <a name="cluster-events"></a>Eventi del cluster

**Eventi di aggiornamento del cluster**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | CM | Informazioni | 1 |
| 29628 | ClusterUpgradeCompleteOperational | CM | Informazioni | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | CM | Informazioni | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | CM | Informazioni | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | CM | Informazioni | 1 |

**Eventi dei report sull'integrità del cluster**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | Informazioni | 1 |
| 54437 | ExpiredClusterEventOperational | HM | Informazioni | 1 |

**Eventi relativi al servizio Chaos**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | Testabilità | Informazioni | 1 |
| 50023 | ChaosStoppedEvent | Testabilità | Informazioni | 1 |

## <a name="node-events"></a>Eventi dei nodi

**Eventi relativi al ciclo di vita dei nodi** 

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | Informazioni | 1 |
| 18603 | NodeUpOperational | FM | Informazioni | 1 |
| 18604 | NodeDownOperational | FM | Informazioni | 1 |
| 18605 | NodeAddedOperational | FM | Informazioni | 1 |
| 18606 | NodeRemovedOperational | FM | Informazioni | 1 |
| 18607 | DeactivateNodeStartOperational | FM | Informazioni | 1 |
| 25620 | NodeOpening | FabricNode | Informazioni | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | Informazioni | 1 |
| 25622 | NodeOpenedFailed | FabricNode | Informazioni | 1 |
| 25623 | NodeClosing | FabricNode | Informazioni | 1 |
| 25624 | NodeClosed | FabricNode | Informazioni | 1 |
| 25625 | NodeAborting | FabricNode | Informazioni | 1 |
| 25626 | NodeAborted | FabricNode | Informazioni | 1 |

**Eventi dei report sull'integrità dei nodi**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | Informazioni | 1 |
| 54432 | ExpiredNodeEventOperational | HM | Informazioni | 1 |

**Eventi dei nodi relativi a Chaos**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | Testabilità | Informazioni | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | Testabilità | Informazioni | 1 |

## <a name="application-events"></a>Eventi dell'applicazione

**Eventi relativi al ciclo di vita delle applicazioni**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | CM | Informazioni | 1 |
| 29625 | ApplicationDeletedOperational | CM | Informazioni | 1 |
| 23083 | ProcessExitedOperational | Hosting | Informazioni | 1 |

**Eventi di aggiornamento delle applicazioni**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | CM | Informazioni | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | CM | Informazioni | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | CM | Informazioni | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | CM | Informazioni | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | CM | Informazioni | 1 |

**Eventi dei report sull'integrità delle applicazioni**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | Informazioni | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | Informazioni | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | Informazioni | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | Informazioni | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | Informazioni | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | Informazioni | 1 |

**Eventi delle applicazioni relativi a Chaos**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | Testabilità | Informazioni | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | Testabilità | Informazioni | 1 |

## <a name="service-events"></a>Eventi dei servizi

**Eventi relativi al ciclo di vita dei servizi**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | Informazioni | 1 |
| 18658 | ServiceDeletedOperational | FM | Informazioni | 1 |

**Eventi dei report sull'integrità dei servizi**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | Informazioni | 1 |
| 54433 | ExpiredServiceEventOperational | HM | Informazioni | 1 |

## <a name="partition-events"></a>Eventi delle partizioni

**Eventi di spostamento delle partizioni**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | RA | Informazioni | 1 |

**Eventi dei report sull'integrità delle partizioni**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | Informazioni | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | Informazioni | 1 |

**Eventi delle partizioni relativi a Chaos**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | Testabilità | Informazioni | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | Testabilità | Informazioni | 1 |

**Eventi di analisi delle partizioni**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | Testabilità | Informazioni | 1 |

## <a name="replica-events"></a>Eventi delle repliche

**Eventi dei report sull'integrità delle repliche**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | Informazioni | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | Informazioni | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | Informazioni | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | Informazioni | 1 |

**Eventi delle repliche relativi a Chaos**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | Testabilità | Informazioni | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | Testabilità | Informazioni | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | Testabilità | Informazioni | 1 |

## <a name="container-events"></a>Eventi dei contenitori

**Eventi relativi al ciclo di vita dei contenitori** 

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | Hosting | Informazioni | 1 |
| 23075 | ContainerDeactivatedOperational | Hosting | Informazioni | 1 |
| 23082 | ContainerExitedOperational | Hosting | Informazioni | 1 |

## <a name="other-events"></a>Altri eventi

**Eventi di correlazione**

| EventId | NOME | Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | Testabilità | Informazioni | 1 |

## <a name="events-prior-to-version-62"></a>Eventi precedenti alla versione 6.2

Di seguito è riportato un elenco completo degli eventi disponibili in Service Fabric prima della versione 6.2.

| EventId | NOME | Origine (attività) | Level |
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
