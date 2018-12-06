---
title: Servizio EventStore di Azure Service Fabric | Microsoft Docs
description: Informazioni sul servizio EventStore di Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: cd58e24a51b153d6bf217f7d32a82e882183ed73
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290672"
---
# <a name="eventstore-service-overview"></a>Panoramica del servizio EventStore

## <a name="overview"></a>Panoramica

Introdotto nella versione 6.2, il servizio EventStore è un'opzione di monitoraggio di Service Fabric che fornisce un modo per conoscere lo stato del cluster o dei carichi di lavoro in un determinato punto nel tempo. Il servizio EventStore espone gli eventi di Service Fabric tramite un set di API, accessibile tramite endpoint REST o la libreria client. Queste API EventStore consentono di eseguire una query del cluster direttamente per visualizzare i dati di diagnostica relativi a qualsiasi entità presente nel cluster e devono essere usate per:

* Diagnosticare problemi di sviluppo o test o laddove sia possibile usare una pipeline di monitoraggio
* Verificare che le azioni di gestione eseguite nel cluster vengano elaborate correttamente dal cluster

Per visualizzare un elenco completo di eventi disponibili in EventStore, vedere [Eventi di Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>A partire da Service Fabric versione 6.2. le API di EventStore sono attualmente in anteprima solo per i cluster Windows eseguiti in Azure. Questa funzionalità sarà presto disponibile anche per Linux e per i cluster autonomi.

È possibile eseguire una query nel servizio EventStore per gli eventi disponibili per ogni entità e tipo di entità presente nel cluster. Ciò significa che è possibile eseguire una query per gli eventi dei livelli seguenti;
* Cluster: tutti gli eventi a livello di cluster
* Nodi: tutti gli eventi a livello di nodo
* Nodo: eventi specifici di un nodo, in base a `nodeName`
* Applicazioni: tutti gli eventi a livello di applicazione
* Applicazione: eventi specifici di un'applicazione
* Servizi: eventi da tutti i servizi del cluster
* Servizio: eventi da un servizio specifico
* Partizioni: eventi da tutte le partizioni
* Partizione: eventi da una partizione specifica
* Repliche: eventi da tutte le repliche/istanze
* Replica: eventi da una replica/istanza specifica


Il servizio EventStore consente anche di correlare gli eventi del cluster. Esaminando gli eventi scritti nello stesso momento da entità diverse che potrebbero avere avuto effetti reciproci, il servizio EventStore è in grado di collegare questi eventi per facilitare l'identificazione delle cause per le attività del cluster. Ad esempio, se una delle applicazioni risulta non integra senza che vi siano state apportate modifiche, il servizio EventStore controlla anche altri eventi esposti dalla piattaforma ed è in grado di metterli in correlazione con un evento `NodeDown`. Ciò rende più rapido il rilevamento degli errori e l'analisi delle cause principali.

Si consiglia di usare EventStore per un'analisi veloce e per ottenere un'idea di snapshot del modo in cui il cluster sta funzionando e se le operazioni vengono eseguite come previsto.

Per iniziare a usare il servizio EventStore, vedere [Eseguire query sulle API di EventStore per eventi del cluster](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Passaggi successivi
* Panoramica di monitoraggio e diagnostica in Service Fabric - [Monitoraggio e diagnostica per Service Fabric](service-fabric-diagnostics-overview.md)
* Altre informazioni sul monitoraggio del cluster - [Monitoraggio del cluster e della piattaforma](service-fabric-diagnostics-event-generation-infra.md).