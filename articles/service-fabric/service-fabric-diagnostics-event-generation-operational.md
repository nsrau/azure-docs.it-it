---
title: Elenco degli eventi di Azure Service Fabric | Microsoft Docs
description: Elenco completo degli eventi disponibili in Azure Service Fabric per monitorare i cluster.
services: service-fabric
documentationcenter: .net
author: srrengar
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
ms.openlocfilehash: 03dac03405588ba00a0f8ca5b127956c40853e36
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868514"
---
# <a name="list-of-service-fabric-events"></a>Elenco degli eventi di Service Fabric 

Service Fabric espone un set primario di eventi del cluster, sotto forma di [eventi di Service Fabric](service-fabric-diagnostics-events.md), per offrire informazioni sullo stato del cluster. Questi eventi sono basati sulle azioni eseguite da Service Fabric sui nodi e sul cluster oppure sulle decisioni di gestione prese dal proprietario/operatore del cluster e sono accessibili eseguendo query su [EventStore](service-fabric-diagnostics-eventstore.md) nel cluster oppure tramite il canale operativo. Nei computer Windows, il canale operativo è collegato anche al registro eventi ed è quindi possibile visualizzare gli eventi di Service Fabric nel Visualizzatore eventi. 

>[!NOTE]
>Per un elenco degli eventi di Service Fabric per i cluster nelle versioni precedenti alla versione 6.2, vedere la sezione successiva. 

Di seguito è riportato un elenco di tutti gli eventi disponibili nella piattaforma, ordinati in base all'entità a cui sono associati.

## <a name="cluster-events"></a>Eventi del cluster

**Eventi di aggiornamento del cluster**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStarted | È stato avviato un aggiornamento del cluster | CM | Informazioni | 1 |
| 29628 | ClusterUpgradeCompleted | È stato completato un aggiornamento del cluster| CM | Informazioni | 1 |
| 29629 | ClusterUpgradeRollbackStarted | È stato avviato il ripristino dello stato precedente all'aggiornamento del cluster | CM | Informazioni | 1 |
| 29630 | ClusterUpgradeRollbackCompleted | È stato completato il ripristino dello stato precedente all'aggiornamento del cluster | CM | Informazioni | 1 |
| 29631 | ClusterUpgradeDomainCompleted | Un aggiornamento del dominio è stato completato durante l'aggiornamento del cluster | CM | Informazioni | 1 |

## <a name="node-events"></a>Eventi dei nodi

**Eventi relativi al ciclo di vita dei nodi** 

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 18602 | NodeDeactivateCompleted | La disattivazione di un nodo è stata completata | FM | Informazioni | 1 |
| 18603 | NodeUp | Il cluster ha rilevato che un nodo è stato avviato | FM | Informazioni | 1 |
| 18604 | NodeDown | Il cluster ha rilevato che un nodo è stato arrestato |  FM | Informazioni | 1 |
| 18605 | NodeAddedToCluster | Un nuovo nodo è stato aggiunto al cluster | FM | Informazioni | 1 |
| 18606 | NodeRemovedFromCluster | Un nodo è stato rimosso dal cluster | FM | Informazioni | 1 |
| 18607 | NodeDeactivateStarted | La disattivazione di un nodo è stata avviata | FM | Informazioni | 1 |
| 25620 | NodeOpening | Un nodo è in fase di avvio. Prima fase del ciclo di vita del nodo | FabricNode | Informazioni | 1 |
| 25621 | NodeOpenSucceeded | Un nodo è stato avviato correttamente | FabricNode | Informazioni | 1 |
| 25622 | NodeOpenFailed | L'avvio di un nodo non è riuscito | FabricNode | Informazioni | 1 |
| 25623 | NodeClosing | Un nodo è in fase di arresto. Inizio della fase finale del ciclo di vita del nodo | FabricNode | Informazioni | 1 |
| 25624 | NodeClosed | Un nodo è stato arrestato correttamente | FabricNode | Informazioni | 1 |
| 25625 | NodeAborting | È iniziato l'arresto anomalo di un nodo | FabricNode | Informazioni | 1 |
| 25626 | NodeAborted | Un nodo è stato arrestato in modo anomalo | FabricNode | Informazioni | 1 |

## <a name="application-events"></a>Eventi dell'applicazione

**Eventi relativi al ciclo di vita delle applicazioni**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 29620 | ApplicationCreated | È stata creata una nuova applicazione | CM | Informazioni | 1 |
| 29625 | ApplicationDeleted | Un'applicazione esistente è stata eliminata | CM | Informazioni | 1 |
| 23083 | ApplicationProcessExited | Un processo all'interno di un'applicazione è stato terminato | Hosting | Informazioni | 1 |

**Eventi di aggiornamento delle applicazioni**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 29621 | ApplicationUpgradeStarted | È stato avviato un aggiornamento dell'applicazione | CM | Informazioni | 1 |
| 29622 | ApplicationUpgradeCompleted | È stato completato un aggiornamento dell'applicazione | CM | Informazioni | 1 |
| 29623 | ApplicationUpgradeRollbackStarted | È stato avviato il ripristino dello stato precedente all'aggiornamento dell'applicazione |CM | Informazioni | 1 |
| 29624 | ApplicationUpgradeRollbackCompleted | È stato completato il ripristino dello stato precedente all'aggiornamento dell'applicazione | CM | Informazioni | 1 |
| 29626 | ApplicationUpgradeDomainCompleted | Un aggiornamento del dominio è stato completato durante l'aggiornamento dell'applicazione | CM | Informazioni | 1 |

## <a name="service-events"></a>Eventi dei servizi

**Eventi relativi al ciclo di vita dei servizi**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | È stato creato un nuovo servizio | FM | Informazioni | 1 |
| 18658 | ServiceDeleted | È stato eliminato un servizio esistente | FM | Informazioni | 1 |

## <a name="partition-events"></a>Eventi delle partizioni

**Eventi di spostamento delle partizioni**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | È stata completata la riconfigurazione di una partizione | RA | Informazioni | 1 |

## <a name="container-events"></a>Eventi dei contenitori

**Eventi relativi al ciclo di vita dei contenitori** 

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Un contenitore è stato avviato | Hosting | Informazioni | 1 |
| 23075 | ContainerDeactivated | Un contenitore è stato arrestato | Hosting | Informazioni | 1 |
| 23082 | ContainerExited | Un contenitore è stato terminato - controllare il flag UnexpectedTermination | Hosting | Informazioni | 1 |

## <a name="health-reports"></a>Report sull'integrità

**Eventi dei report sull'integrità del cluster**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | È disponibile un nuovo report sull'integrità del cluster | HM | Informazioni | 1 |
| 54437 | ClusterHealthReportExpired | Un report esistente sull'integrità del cluster è scaduto | HM | Informazioni | 1 |

**Eventi dei report sull'integrità dei nodi**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | È disponibile un nuovo report sull'integrità dei nodi | HM | Informazioni | 1 |
| 54432 | NodeHealthReportExpired | Un report esistente sull'integrità dei nodi è scaduto | HM | Informazioni | 1 |

**Eventi dei report sull'integrità delle applicazioni**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | È stato creato un nuovo report sull'integrità delle applicazioni. È valido per le applicazioni non distribuite | HM | Informazioni | 1 |
| 54426 | DeployedApplicationNewHealthReport | È stato creato un nuovo report sull'integrità delle applicazioni distribuite | HM | Informazioni | 1 |
| 54427 | DeployedServicePackageNewHealthReport | È stato creato un nuovo report sull'integrità dei servizi distribuiti | HM | Informazioni | 1 |
| 54434 | ApplicationHealthReportExpired | Un report esistente sull'integrità delle applicazioni è scaduto | HM | Informazioni | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Un report esistente sull'integrità delle applicazioni distribuite è scaduto | HM | Informazioni | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Un report esistente sull'integrità dei servizi distribuiti è scaduto | HM | Informazioni | 1 |

**Eventi dei report sull'integrità dei servizi**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | È stato creato un nuovo report sull'integrità dei servizi | HM | Informazioni | 1 |
| 54433 | ServiceHealthReportExpired | Un report esistente sull'integrità dei servizi è scaduto | HM | Informazioni | 1 |

**Eventi dei report sull'integrità delle partizioni**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | È stato creato un nuovo report sull'integrità delle partizioni | HM | Informazioni | 1 |
| 54431 | PartitionHealthReportExpired | Un report esistente sull'integrità delle partizioni è scaduto | HM | Informazioni | 1 |

**Eventi dei report sull'integrità delle repliche**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | È stato creato un report sull'integrità delle repliche con stato | HM | Informazioni | 1 |
| 54430 | StatelessInstanceNewHealthReport | È stato creato un nuovo report sull'integrità delle istanze senza stato | HM | Informazioni | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Un report esistente sull'integrità delle repliche con stato è scaduto | HM | Informazioni | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Un report esistente sull'integrità delle istanze senza stato è scaduto | HM | Informazioni | 1 |

## <a name="chaos-testing-events"></a>Eventi di chaos testing 

**Eventi di sessioni di chaos testing**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | È stata avviata una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 50023 | ChaosStopped | È stata arrestata una sessione di chaos testing | Testabilità | Informazioni | 1 |

**Eventi dei nodi relativi a Chaos**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | È stato pianificato il riavvio di un nodo nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 50087 | ChaosNodeRestartCompleted | Il riavvio di un nodo è stato completato nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |

**Eventi delle applicazioni relativi a Chaos**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | È stato pianificato il riavvio di un pacchetto di codice durante una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Il riavvio di un pacchetto di codice è stato completato durante una sessione di chaos testing | Testabilità | Informazioni | 1 |

**Eventi delle partizioni relativi a Chaos**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | È stato pianificato lo spostamento di una partizione primaria nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | È stato pianificato lo spostamento di una partizione secondaria nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | È disponibile l'analisi più approfondita dello spostamento della partizione primaria | Testabilità | Informazioni | 1 |

**Eventi delle repliche relativi a Chaos**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | È stato pianificato il riavvio di una replica nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 50051 | ChaosReplicaRemovalScheduled | È stata pianificata la rimozione di una replica nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 50093 | ChaosReplicaRemovalCompleted | È stata completata la rimozione di una replica nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |

## <a name="other-events"></a>Altri eventi

**Eventi di correlazione**

| EventId | NOME | DESCRIZIONE |Origine (attività) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | È stata rilevata una correlazione | Testabilità | Informazioni | 1 |

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
