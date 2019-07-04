---
title: Servizio EventStore di Azure Service Fabric | Microsoft Docs
description: Informazioni sul servizio EventStore di Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: e7ae4c77f958bacabea50b7193817cd41ea54aa9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449777"
---
# <a name="eventstore-overview"></a>Panoramica di EventStore

>[!NOTE]
>A partire da Service Fabric versione 6.4. le API di EventStore sono disponibili solo per i cluster Windows eseguiti in Azure. Questa funzionalità sarà presto disponibile anche per Linux e per i cluster autonomi.

## <a name="overview"></a>Panoramica

Introdotto nella versione 6.2, il servizio EventStore è un'opzione di monitoraggio di Service Fabric. EventStore offre la possibilità di comprendere lo stato del cluster o dei carichi di lavoro in un determinato punto nel tempo.
EventStore è un servizio di Service Fabric con stato che gestisce gli eventi del cluster. L'evento viene esposto usando Service Fabric Explorer, REST e le API. EventStore esegue direttamente una query sul cluster per visualizzare i dati di diagnostica relativi a qualsiasi entità presente nel cluster e deve essere usato per:

* Diagnosticare problemi di sviluppo o test o laddove sia possibile usare una pipeline di monitoraggio
* Verificare che le azioni di gestione eseguite nel cluster vengano elaborate correttamente
* Ottenere un'istantanea del modo in cui Service Fabric interagisce con una particolare entità

![EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

Per visualizzare un elenco completo di eventi disponibili in EventStore, vedere [Eventi di Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>A partire da Service Fabric versione 6.4. EventStore APIs e dell'esperienza utente sono disponibili a livello generale per i cluster di Windows Azure. Questa funzionalità sarà presto disponibile anche per Linux e per i cluster autonomi.

È possibile eseguire una query nel servizio EventStore per gli eventi disponibili per ogni entità e tipo di entità presente nel cluster. Ciò significa che è possibile eseguire una query per gli eventi dei livelli seguenti:
* Cluster: eventi specifici per lo stesso cluster, ad esempio l'aggiornamento del cluster
* Nodi: tutti gli eventi a livello di nodo
* Nodo: eventi specifici di un nodo, identificati da `nodeName`
* Applicazioni: tutti gli eventi a livello di applicazione
* Applicazione: eventi specifici di un'applicazione identificata da `applicationId`
* Servizi: eventi da tutti i servizi del cluster
* Servizio: eventi di un servizio specifico identificato da `serviceId`
* Partizioni: eventi da tutte le partizioni
* Partizione: eventi di una partizione specifica identificata da `partitionId`
* Repliche delle partizioni: eventi di tutte le repliche/istanze all'interno di una partizione specifica identificata da `partitionId`
* Replica delle partizioni: eventi di una replica/istanza specifica identificata da `replicaId` e `partitionId`

Per altre informazioni sulle API, consultare il riferimento a [EventsStore APIs](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore) (API di EventsStore).

Il servizio EventStore consente anche di correlare gli eventi del cluster. Esaminando gli eventi scritti nello stesso momento da entità diverse che potrebbero avere avuto effetti reciproci, il servizio EventStore è in grado di collegare questi eventi per facilitare l'identificazione delle cause per le attività del cluster. Ad esempio, se una delle applicazioni risulta non integra senza che siano state apportate modifiche, il servizio EventStore controlla anche altri eventi esposti dalla piattaforma ed è in grado di metterli in correlazione con un evento `Error` o `Warning`. Ciò rende più rapido il rilevamento degli errori e l'analisi delle cause principali.

## <a name="enable-eventstore-on-your-cluster"></a>Abilitare EventStore nel cluster

### <a name="local-cluster"></a>Cluster locale

In [fabricSettings.json nel cluster](service-fabric-cluster-fabric-settings.md) aggiungere EventStoreService come funzionalità del componente aggiuntivo ed eseguire un aggiornamento del cluster.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Versione del cluster Azure 6.5 +
Se il cluster di Azure viene aggiornato alla versione 6.5 o versione successiva, EventStore verrà abilitata automaticamente nel cluster. Per rifiutare esplicitamente, è necessario aggiornare il modello di cluster con il codice seguente:

* Usare la versione API di `2019-03-01` o versione successiva 
* Aggiungere il codice seguente alla sezione di proprietà del cluster
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Cluster di Azure versione 6.4

Se si usa la versione 6.4, è possibile modificare il modello di Azure Resource Manager per abilitare il servizio di EventStore. Questa operazione viene eseguita mediante l'esecuzione di un [aggiornamento della configurazione del cluster](service-fabric-cluster-config-upgrade-azure.md) e aggiungendo il codice seguente, è possibile usare PlacementConstraints per inserire le repliche del servizio di EventStore in un tipo di nodo specifico, ad esempio un tipo di nodo dedicato per i servizi di sistema . La sezione `upgradeDescription` configura l'aggiornamento della configurazione per attivare un riavvio sui nodi. È possibile rimuovere la sezione in un altro aggiornamento.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Passaggi successivi
* Introduzione all'API di EventStore - [Uso delle API di EventStore nei cluster di Azure Service Fabric](service-fabric-diagnostics-eventstore-query.md)
* Altre informazioni sull'elenco di eventi offerto da EventStore - [Eventi di Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* Panoramica di monitoraggio e diagnostica in Service Fabric - [Monitoraggio e diagnostica per Service Fabric](service-fabric-diagnostics-overview.md)
* Visualizzare l'elenco completo delle chiamate API - [Riferimento all'API REST di EventStore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* Altre informazioni sul monitoraggio del cluster - [Monitoraggio del cluster e della piattaforma](service-fabric-diagnostics-event-generation-infra.md).
