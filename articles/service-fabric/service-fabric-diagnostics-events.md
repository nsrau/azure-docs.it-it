---
title: Eventi di Azure Service Fabric | Microsoft Docs
description: Informazioni sugli eventi di Service Fabric forniti automaticamente per monitorare il cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: b9372c806eab1b0ca69ba078d972b076c8a7d6f6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212721"
---
# <a name="service-fabric-events"></a>Eventi di Service Fabric 

La piattaforma Service Fabric scrive diversi eventi strutturati per le attività operative chiave eseguite all'interno del cluster. Queste variano dagli aggiornamenti del cluster alle decisioni di posizionamento delle repliche. Ogni evento esposto da Service Fabric è mappato a una delle seguenti entità nel cluster:
* HDInsight
* Applicazione
* Service
* Partition
* Replica 
* Contenitore

Visualizzare un elenco completo degli eventi esposti dalla piattaforma - [Elenco degli eventi di Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Di seguito sono riportati alcuni esempi di scenari importanti per cui è possibile visualizzare degli eventi nel cluster. 
1. Eventi del ciclo di vita di un nodo: ogni volta che i nodi aumentano o diminuiscono o vengono attivati, disattivati o riavviati, vengono esposti eventi per visualizzare che cosa è successo e permettere di rilevare eventuali problemi con la macchina stessa o la chiamata di un'API tramite SF per modificare lo stato di un nodo.
1. Aggiornamento di un cluster: durante l'aggiornamento del cluster (versione SF o modifica della configurazione), viene visualizzato l'aggiornamento avviarsi, includere i diversi domini di aggiornamento e concludersi (o eseguire il rollback). 
1. Aggiornamenti dell'applicazione: in modo analogo agli aggiornamenti del cluster, è presente un set completo di eventi mentre viene eseguito il rollback dell'aggiornamento. Questi eventi possono essere utili per comprendere quando è stato pianificato un aggiornamento, lo stato corrente di un aggiornamento e la sequenza generale degli eventi. Ciò è utile per vedere quali aggiornamenti sono stato eseguiti correttamente.
1. Distribuzione/eliminazione di applicazioni o servizi: sono presenti eventi per ogni applicazione, servizio e contenitore creati o eliminati.
1. Movimenti di partizione (riconfigurazione): ogni volta che una partizione con stato subisce una riconfigurazione (una modifica del set di repliche), viene registrato un evento. Ciò è utile per comprendere la frequenza di modifica del set di repliche di partizione o tenere traccia del nodo su cui era in esecuzione la replica primaria in qualsiasi punto nel tempo.
1. Eventi CHAOS: quando si usa il servizio [CHAOS](service-fabric-controlled-chaos.md) di Service Fabric, si vedranno eventi ogni volta che il servizio viene avviato o arrestato o quando provoca un errore nel sistema.
1. Eventi di integrità: Service Fabric espone eventi di integrità ogni volta che viene creato un report di integrità di tipo Avviso o Errore, oppure quando un'entità viene reimpostata a uno stato di integrità OK o alla scadenza di un report di integrità. Questi eventi sono molto utili per tenere traccia delle statistiche cronologiche di integrità di un'entità. 

## <a name="how-to-access-events"></a>Come accedere agli eventi

Esistono diversi modi tramite cui è possibile accedere agli eventi di Service Fabric:
* tramite il canale operativo. Questi possono essere raccolti tramite l'estensione Diagnostica di Azure e inviati a una tabella Archiviazione per il consumo o l'inserimento in uno strumento quale OMS Log Analytics. Quando l'opzione "Diagnostica" è abilitata per un cluster, l'agente di Diagnostica di Azure viene distribuito nel cluster e viene configurato per impostazione predefinita per la lettura nei log dal canale operativo. Altre informazioni sulla configurazione dell'[agente di Diagnostica di Azure](service-fabric-diagnostics-event-aggregation-wad.md) per modificare la configurazione della diagnostica del cluster in modo da prelevare più log o contatori delle prestazioni. 
* tramite le API Rest del servizio EventStore che consentono di eseguire una query del cluster, direttamente o tramite la libreria client di Service Fabric. Vedere [Eseguire query sulle API di EventStore per eventi del cluster](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul monitoraggio del cluster - [Monitoraggio del cluster e della piattaforma](service-fabric-diagnostics-event-generation-infra.md).
* Altre informazioni sul servizio EventStore - [Panoramica del servizio EventStore](service-fabric-diagnostics-eventstore.md)
