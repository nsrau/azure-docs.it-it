---
title: Elenco di eventi Service Fabric di Azure
description: Elenco completo degli eventi disponibili in Azure Service Fabric per monitorare i cluster.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258538"
---
# <a name="list-of-service-fabric-events"></a>Elenco degli eventi di Service Fabric 

Service Fabric espone un set primario di eventi del cluster, sotto forma di [eventi di Service Fabric](service-fabric-diagnostics-events.md), per offrire informazioni sullo stato del cluster. Questi eventi sono basati sulle azioni eseguite da Service Fabric sui nodi e sul cluster oppure sulle decisioni di gestione prese dal proprietario/operatore del cluster È possibile accedere a questi eventi configurando in diversi modi, tra cui la configurazione dei [log di monitoraggio di Azure con il cluster](service-fabric-diagnostics-oms-setup.md)o l'esecuzione di query su [EventStore](service-fabric-diagnostics-eventstore.md). Nei computer Windows questi eventi vengono inseriti nel registro eventi ed è quindi possibile visualizzare gli eventi di Service Fabric nel Visualizzatore eventi. 

Ecco alcune caratteristiche di questi eventi
* Ogni evento è associato a un'entità specifica nel cluster, ad esempio applicazione, servizio, nodo, replica.
* Ogni evento contiene un set di campi comuni: EventInstanceId, eventName e Category.
* Ogni evento contiene campi che collegano nuovamente l'evento all'entità a cui è associato. Ad esempio, l'evento ApplicationCreated ha campi che identificano il nome dell'applicazione creata.
* Gli eventi sono strutturati in modo da poter essere usati in un'ampia gamma di strumenti per eseguire altre analisi. Inoltre, i dettagli pertinenti per un evento sono definiti come proprietà separate anziché come stringa lunga. 
* Gli eventi vengono scritti da sottosistemi diversi in Service Fabric e identificati da Source(Task) come illustrato di seguito. Sono disponibili altre informazioni su questi sottosistemi in [Architettura di Service Fabric](service-fabric-architecture.md) e [Panoramica tecnica di Service Fabric](service-fabric-technical-overview.md).

Di seguito è riportato un elenco di eventi di Service Fabric organizzati in base all'entità.

## <a name="cluster-events"></a>Eventi del cluster

**Eventi di aggiornamento del cluster**

Altre informazioni sugli aggiornamenti dei cluster sono disponibili [qui](service-fabric-cluster-upgrade-windows-server.md).

| EventId | Name | Category | Descrizione |Origine (attività) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Aggiornamento | È stato avviato un aggiornamento del cluster | CM | Informazioni |
| 29628 | ClusterUpgradeCompleted | Aggiornamento | È stato completato un aggiornamento del cluster | CM | Informazioni | 
| 29629 | ClusterUpgradeRollbackStarted | Aggiornamento | È stato avviato il ripristino dello stato precedente all'aggiornamento del cluster  | CM | Avviso | 
| 29630 | ClusterUpgradeRollbackCompleted | Aggiornamento | È stato completato il ripristino dello stato precedente all'aggiornamento del cluster | CM | Avviso | 
| 29631 | ClusterUpgradeDomainCompleted | Aggiornamento | Un dominio di aggiornamento ha completato l'aggiornamento durante un aggiornamento del cluster | CM | Informazioni | 

## <a name="node-events"></a>Eventi dei nodi

**Eventi relativi al ciclo di vita dei nodi** 

| EventId | Name | Category | Descrizione |Origine (attività) | Level |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | La disattivazione di un nodo è stata completata | FM | Informazioni | 
| 18603 | NodeUp | StateTransition | Il cluster ha rilevato che un nodo è stato avviato | FM | Informazioni | 
| 18604 | NodeDown | StateTransition | Il cluster ha rilevato che un nodo è stato arrestato. Durante un riavvio di nodo, viene visualizzato un evento NodeDown seguito da un evento NodeUp |  FM | Errore | 
| 18605 | NodeAddedToCluster | StateTransition |  È stato aggiunto un nuovo nodo al cluster e Service Fabric è in grado di distribuire le applicazioni a questo nodo | FM | Informazioni | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Un nodo è stato rimosso dal cluster. Service Fabric non effettuerà più la distribuzione delle applicazioni a questo nodo | FM | Informazioni | 
| 18607 | NodeDeactivateStarted | StateTransition |  La disattivazione di un nodo è stata avviata | FM | Informazioni | 
| 25621 | NodeOpenSucceeded | StateTransition |  Un nodo è stato avviato correttamente | FabricNode | Informazioni | 
| 25622 | NodeOpenFailed | StateTransition |  Un nodo non è in grado di avviarsi e creare un join dell'anello | FabricNode | Errore | 
| 25624 | NodeClosed | StateTransition |  Un nodo è stato arrestato correttamente | FabricNode | Informazioni | 
| 25626 | NodeAborted | StateTransition |  Un nodo è stato arrestato in modo anomalo | FabricNode | Errore | 

## <a name="application-events"></a>Eventi dell'applicazione

**Eventi relativi al ciclo di vita delle applicazioni**

| EventId | Name | Category | Descrizione |Origine (attività) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | LifeCycle | È stata creata una nuova applicazione | CM | Informazioni | 
| 29625 | ApplicationDeleted | LifeCycle | Un'applicazione esistente è stata eliminata | CM | Informazioni | 
| 23083 | ApplicationProcessExited | LifeCycle | Un processo all'interno di un'applicazione è stato terminato | Hosting | Informazioni | 

**Eventi di aggiornamento delle applicazioni**

Altre informazioni sugli aggiornamenti delle applicazioni sono disponibili [qui](service-fabric-application-upgrade.md).

| EventId | Name | Category | Descrizione |Origine (attività) | Level | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Aggiornamento | È stato avviato un aggiornamento dell'applicazione | CM | Informazioni | 
| 29622 | ApplicationUpgradeCompleted | Aggiornamento | È stato completato un aggiornamento dell'applicazione | CM | Informazioni | 
| 29623 | ApplicationUpgradeRollbackStarted | Aggiornamento | È stato avviato il ripristino dello stato precedente all'aggiornamento dell'applicazione |CM | Avviso | 
| 29624 | ApplicationUpgradeRollbackCompleted | Aggiornamento | È stato completato il ripristino dello stato precedente all'aggiornamento dell'applicazione | CM | Avviso | 
| 29626 | ApplicationUpgradeDomainCompleted | Aggiornamento | Un dominio di aggiornamento ha completato l'aggiornamento durante un aggiornamento dell'applicazione | CM | Informazioni | 

## <a name="service-events"></a>Eventi dei servizi

**Eventi relativi al ciclo di vita dei servizi**

| EventId | Name | Category | Descrizione |Origine (attività) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | LifeCycle | È stato creato un nuovo servizio | FM | Informazioni | 
| 18658 | ServiceDeleted | LifeCycle | È stato eliminato un servizio esistente | FM | Informazioni | 

## <a name="partition-events"></a>Eventi delle partizioni

**Eventi di spostamento delle partizioni**

| EventId | Name | Category | Descrizione |Origine (attività) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | LifeCycle | È stata completata la riconfigurazione di una partizione | RA | Informazioni | 

## <a name="replica-events"></a>Eventi delle repliche

**Eventi del ciclo di vita della replica**

| EventId | Name | Category | Descrizione |Origine (attività) | Level |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | LifeCycle | Reliable Dictionary aperto | DistributedDictionary | Informazioni |
| 61702 | ReliableDictionaryClosed | LifeCycle | Reliable Dictionary chiuso | DistributedDictionary | Informazioni |
| 61703 | ReliableDictionaryCheckpointRecovered | LifeCycle | Reliable Dictionary ha recuperato il Checkpoint | DistributedDictionary | Informazioni |
| 61704 | ReliableDictionaryCheckpointFilesSent | LifeCycle | La replica ha inviato i file del checkpoint del dizionario Reliable | DistributedDictionary | Informazioni |
| 61705 | ReliableDictionaryCheckpointFilesReceived | LifeCycle | La replica ha ricevuto i file del checkpoint del dizionario Reliable | DistributedDictionary | Informazioni |
| 61963 | ReliableQueueOpened | LifeCycle | Coda affidabile aperta | DistributedQueue | Informazioni |
| 61964 | ReliableQueueClosed | LifeCycle | Coda affidabile chiusa | DistributedQueue | Informazioni |
| 61965 | ReliableQueueCheckpointRecovered | LifeCycle | Il checkpoint della coda affidabile è stato recuperato | DistributedQueue | Informazioni |
| 61966 | ReliableQueueCheckpointFilesSent | LifeCycle | La replica ha inviato i file del checkpoint della coda affidabile | DistributedQueue | Informazioni |
| 63647 | ReliableQueueCheckpointFilesReceived | LifeCycle | La replica ha ricevuto i file del checkpoint della coda affidabile | DistributedQueue | Informazioni |
| 63648 | ReliableConcurrentQueueOpened | LifeCycle | Coda simultanea affidabile aperta | ReliableConcurrentQueue | Informazioni |
| 63649 | ReliableConcurrentQueueClosed | LifeCycle | Coda simultanea affidabile chiusa | ReliableConcurrentQueue | Informazioni |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | LifeCycle | Il checkpoint della coda simultanea affidabile è stato recuperato | ReliableConcurrentQueue | Informazioni |
| 61687 | TStoreError | Operazioni non riuscite | La raccolta Reliable Collection ha ricevuto un errore imprevisto | TStore | Errore |
| 63831 | PrimaryFullCopyInitiated | LifeCycle | La replica primaria ha avviato una copia completa | TReplicator | Informazioni |
| 63832 | PrimaryPartialCopyInitiated | LifeCycle | La replica primaria ha avviato una copia parziale | TReplicator | Informazioni |
| 16831 | BuildIdleReplicaStarted | LifeCycle | La replica primaria ha iniziato a compilare la replica inattiva | Replica | Informazioni |
| 16832 | BuildIdleReplicaCompleted | LifeCycle | La replica primaria ha completato la compilazione della replica inattiva | Replica | Informazioni |
| 16833 | BuildIdleReplicaFailed | LifeCycle | La replica primaria non è riuscita a compilare la replica inattiva | Replica | Avviso |
| 16834 | PrimaryReplicationQueueFull | Integrità | Coda di replica della replica primaria piena | Replica | Avviso |
| 16835 | PrimaryReplicationQueueWarning | Integrità | Coda di replica della replica primaria quasi completa | Replica | Avviso |
| 16836 | PrimaryReplicationQueueWarningMitigated | Integrità | La coda di replica della replica primaria è accettabile | Replica | Informazioni |
| 16837 | SecondaryReplicationQueueFull | Integrità | Coda di replica della replica secondaria piena | Replica | Avviso |
| 16838 | SecondaryReplicationQueueWarning | Integrità | Coda di replica della replica secondaria quasi completa | Replica | Avviso |
| 16839 | SecondaryReplicationQueueWarningMitigated | Integrità | La coda di replica della replica secondaria è accettabile | Replica | Informazioni |
| 16840 | PrimaryFaultedSlowSecondary | Integrità | La replica primaria ha generato un errore di una replica secondaria lenta | Replica | Avviso |
| 16841 | ReplicatorFaulted | Integrità | Replica con errori | Replica | Avviso |

## <a name="container-events"></a>Eventi dei contenitori

**Eventi relativi al ciclo di vita dei contenitori** 

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Un contenitore è stato avviato | Hosting | Informazioni | 1 |
| 23075 | ContainerDeactivated | Un contenitore è stato arrestato | Hosting | Informazioni | 1 |
| 23082 | ContainerExited | Un contenitore è stato terminato - controllare il flag UnexpectedTermination | Hosting | Informazioni | 1 |

## <a name="health-reports"></a>Report sull'integrità

Il [modello di integrità di Service Fabric](service-fabric-health-introduction.md) offre funzionalità di valutazione e reporting dell'integrità dettagliate, flessibili ed estendibili. A partire da Service Fabric versione 6.2, i dati relativi all'integrità vengono scritti come eventi della piattaforma per creare record cronologici dell'integrità. Per mantenere limitato il volume degli eventi di integrità, gli elementi seguenti vengono scritti sotto forma di eventi di Service Fabric:

* Tutti i report sull'integrità di tipo `Error` o `Warning`
* I report sull'integrità `Ok` durante le transizioni
* Alla scadenza di un evento di integrità `Error` o `Warning`. Consente di stabilire per quanto tempo un'entità è stata non integra

**Eventi dei report sull'integrità del cluster**

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | È disponibile un nuovo report sull'integrità del cluster | HM | Informazioni | 1 |
| 54437 | ClusterHealthReportExpired | Un report esistente sull'integrità del cluster è scaduto | HM | Informazioni | 1 |

**Eventi dei report sull'integrità dei nodi**

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | È disponibile un nuovo report sull'integrità dei nodi | HM | Informazioni | 1 |
| 54432 | NodeHealthReportExpired | Un report esistente sull'integrità dei nodi è scaduto | HM | Informazioni | 1 |

**Eventi dei report sull'integrità delle applicazioni**

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | È stato creato un nuovo report sull'integrità delle applicazioni. È valido per le applicazioni non distribuite | HM | Informazioni | 1 |
| 54426 | DeployedApplicationNewHealthReport | È stato creato un nuovo report sull'integrità delle applicazioni distribuite | HM | Informazioni | 1 |
| 54427 | DeployedServicePackageNewHealthReport | È stato creato un nuovo report sull'integrità dei servizi distribuiti | HM | Informazioni | 1 |
| 54434 | ApplicationHealthReportExpired | Un report esistente sull'integrità delle applicazioni è scaduto | HM | Informazioni | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Un report esistente sull'integrità delle applicazioni distribuite è scaduto | HM | Informazioni | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Un report esistente sull'integrità dei servizi distribuiti è scaduto | HM | Informazioni | 1 |

**Eventi dei report sull'integrità dei servizi**

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | È stato creato un nuovo report sull'integrità dei servizi | HM | Informazioni | 1 |
| 54433 | ServiceHealthReportExpired | Un report esistente sull'integrità dei servizi è scaduto | HM | Informazioni | 1 |

**Eventi dei report sull'integrità delle partizioni**

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | È stato creato un nuovo report sull'integrità delle partizioni | HM | Informazioni | 1 |
| 54431 | PartitionHealthReportExpired | Un report esistente sull'integrità delle partizioni è scaduto | HM | Informazioni | 1 |

**Eventi dei report sull'integrità delle repliche**

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | È stato creato un report sull'integrità delle repliche con stato | HM | Informazioni | 1 |
| 54430 | StatelessInstanceNewHealthReport | È stato creato un nuovo report sull'integrità delle istanze senza stato | HM | Informazioni | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Un report esistente sull'integrità delle repliche con stato è scaduto | HM | Informazioni | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Un report esistente sull'integrità delle istanze senza stato è scaduto | HM | Informazioni | 1 |

## <a name="chaos-testing-events"></a>Eventi di chaos testing 

**Eventi di sessioni di chaos testing**

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | È stata avviata una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 50023 | ChaosStopped | È stata arrestata una sessione di chaos testing | Testabilità | Informazioni | 1 |

**Eventi dei nodi relativi a Chaos**

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | È stato pianificato il riavvio di un nodo nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 50087 | ChaosNodeRestartCompleted | Il riavvio di un nodo è stato completato nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |

**Eventi delle applicazioni relativi a Chaos**

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | È stato pianificato il riavvio di un pacchetto di codice durante una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Il riavvio di un pacchetto di codice è stato completato durante una sessione di chaos testing | Testabilità | Informazioni | 1 |

**Eventi delle partizioni relativi a Chaos**

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | È stato pianificato lo spostamento di una partizione primaria nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | È stato pianificato lo spostamento di una partizione secondaria nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | È disponibile l'analisi più approfondita dello spostamento della partizione primaria | Testabilità | Informazioni | 1 |

**Eventi delle repliche relativi a Chaos**

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | È stato pianificato il riavvio di una replica nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 50051 | ChaosReplicaRemovalScheduled | È stata pianificata la rimozione di una replica nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |
| 50093 | ChaosReplicaRemovalCompleted | È stata completata la rimozione di una replica nell'ambito di una sessione di chaos testing | Testabilità | Informazioni | 1 |

## <a name="other-events"></a>Altri eventi

**Eventi di correlazione**

| EventId | Name | Descrizione |Origine (attività) | Level | Versione |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | È stata rilevata una correlazione | Testabilità | Informazioni | 1 |

## <a name="events-prior-to-version-62"></a>Eventi precedenti alla versione 6.2

Di seguito è riportato un elenco completo degli eventi disponibili in Service Fabric prima della versione 6.2.

| EventId | Name | Origine (attività) | Level |
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

* Panoramica della [diagnostica in Service Fabric](service-fabric-diagnostics-overview.md)
* Altre informazioni sul servizio EventStore in [Panoramica di EventStore di Service Fabric](service-fabric-diagnostics-eventstore.md)
* Modifica della configurazione di [Diagnostica di Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md) per raccogliere più log
* [Configurazione di Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) per visualizzare i log del canale operativo
