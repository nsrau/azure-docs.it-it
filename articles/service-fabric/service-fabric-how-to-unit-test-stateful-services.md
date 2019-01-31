---
title: Sviluppare unit test per i servizi con stato in Azure Service Fabric | Microsoft Docs
description: Informazioni su come sviluppare unit test per i servizi con stato di Service Fabric.
services: service-fabric
documentationcenter: .net
author: charleszipp
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: ryanwi
ms.openlocfilehash: a030860bcef41d7276e1356553b984f55e27ae1e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164159"
---
# <a name="create-unit-tests-for-stateful-services"></a>Creare unit test per i servizi con stato
Il testing unità dei servizi con stato di Service Fabric svela gli errori comuni che non verrebbero necessariamente rilevati dall'applicazione convenzionale o testing unità specifico di dominio. Durante lo sviluppo di unit test per i servizi con stato, esistono alcune considerazioni speciali che devono essere tenute a mente.

1. Ogni replica esegue il codice dell'applicazione, ma in contesti diversi. Se il servizio usa tre repliche, il codice del servizio è in esecuzione su tre nodi in parallelo nel contesto/ruolo diverso.
2. Lo stato memorizzato all'interno del servizio con stato dovrebbe essere coerenza tra tutte le repliche. Il gestore di stato e le reliable collections forniranno questa coerenza out-of-the-box. Tuttavia, lo stato in memoria dovrà essere gestito dal codice dell'applicazione.
3. Ogni replica cambierà ruoli a un certo punto durante l'esecuzione nel cluster. Una replica secondaria diventerà una replica primaria, nel caso in cui il nodo che ospita il database primario diventa non disponibile o sottoposto a overload. Questo è un comportamento naturale per Service Fabric, pertanto i servizi devono pianificare l'esecuzione finale in un ruolo diverso.

Questo articolo si presuppone che [Unit test di servizi con stato in Service Fabric](service-fabric-concepts-unit-testing.md) è stato letto.

## <a name="the-servicefabricmocks-library"></a>La libreria ServiceFabric.Mocks
A partire dalla versione 3.3.0, [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) fornisce un'API per il comportamento fittizio sia a livello dell'orchestrazione delle repliche sia a livello della gestione dello stato. Verrà usato negli esempi.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks non è di proprietà né è gestito da Microsoft. Tuttavia, questa è attualmente la libreria consigliata di Microsoft per i servizi del testing unità con stato.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Configurare l'orchestrazione fittizia e lo stato
Come parte della porzione di disposizione di un test, verranno creati una replica fittizia impostata e un gestore di stato. Il set di repliche conterrà quindi la creazione di un'istanza del servizio testato per ogni replica. Diventerà proprietario anche degli eventi del ciclo di vita in esecuzione, ad esempio `OnChangeRole` e `RunAsync`. Il gestore di stato fittizio garantirà che eventuali operazioni eseguite a fronte di gestore di stato vengano eseguite e mantenute come farebbe il gestore di stato reale.

1. Creare un delegato di service factory che genererà un'istanza del servizio sottoposto a test. Deve essere uguale o simile al callback di factory del servizio in genere disponibile in `Program.cs` per un attore o un servizio di Service Fabric. Ciò deve seguire la firma seguente:
```csharp
MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
```
2. Creare un'istanza di classe`MockReliableStateManager`. Si simuleranno tutte le interazioni con il gestore di stato.
3. Creare un'istanza del `MockStatefulServiceReplicaSet<TStatefulService>` dove `TStatefulService` è il tipo del servizio sottoposto a test. Questo richiederà che il delegato creato nel passaggio #1 e il gestore di stato crei un'istanza in #2
4. Aggiungere repliche al Set di repliche. Specificare il ruolo (ad esempio Primary, ActiveSecondary, IdleSecondary) e l'ID della replica
> Tenere premuto per l'ID di replica. Questi verranno probabilmente utilizzati durante l'atto e durante le porzioni di asserzione di un unit test.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Eseguire le richieste di servizio
Le richieste di servizio possono essere eseguite in una replica specifica tramite le proprietà di praticità e le ricerche.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Eseguire un'operazione di spostamento del servizio
Il set di repliche fittizie espone molti metodi pratici per attivare i diversi tipi di spostamenti del servizio.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Riassumendo
Il test seguente dimostra l'impostazione di un set di repliche a tre nodi e verificando che i dati sono disponibili da un database secondario dopo una modifica del ruolo. Un problema tipico che potrebbe rilevare se i dati aggiunti durante `InsertAsync` sono stati salvati in un valore in memoria o in una raccolta affidabile non eseguita `CommitAsync`. In entrambi i casi, il database secondario non sarà sincronizzato con la replica primaria. Ciò potrebbe portare a risposte incoerenti dopo che lo spostamento del servizio.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come testare la [comunicazione da servizio a servizio](service-fabric-testability-scenarios-service-communication.md) e [simulare errori tramite l'uso di chaos controllato](service-fabric-controlled-chaos.md).
