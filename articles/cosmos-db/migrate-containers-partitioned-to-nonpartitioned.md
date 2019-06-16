---
title: Eseguire la migrazione non partizionato i contenitori di Azure Cosmos DB a contenitori partizionati
description: Informazioni su come eseguire la migrazione di tutti i contenitori di non partizionata esistenti in contenitori partizionati.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 8ba9489496a8f9e3703702e344684b4028a002cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241933"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Eseguire la migrazione dei contenitori non partizionata a contenitori partizionati

Azure Cosmos DB supporta la creazione di contenitori senza una chiave di partizione. Attualmente è possibile creare contenitori non partizionata utilizzando CLI di Azure e Azure Cosmos DB SDK (.Net, Java, NodeJs) che dispone di una versione minore o uguale alla versione 2.x. Non è possibile creare i contenitori non partizionata usando il portale di Azure. Tuttavia, tali contenitori non partizionati non sono elastici e sono corretti capacità di archiviazione di 10 GB e velocità effettiva di limite di 10 K UR/s.

È consigliabile eseguire la migrazione dei contenitori non partizionata esistenti a contenitori partizionati per l'archiviazione con scalabilità e velocità effettiva dei contenitori non partizionata sono legacy. Azure Cosmos DB offre un meccanismo definito dal sistema per eseguire la migrazione dei contenitori non partizionati da contenitori partizionati. Questo documento illustra come tutti i contenitori di non partizionata esistenti vengono migrate automaticamente in contenitori partizionati. È possibile sfruttare la funzionalità di migrazione automatica solo se si usa la versione V3 degli SDK in tutte le lingue.

> [!NOTE] 
> Attualmente, non è possibile migrare gli account Azure Cosmos DB MongoDB e API di Gremlin usando la procedura descritta in questo documento. 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Eseguire la migrazione di contenitore usando la chiave di partizione definito dal sistema

Per supportare la migrazione, Azure Cosmos DB definisce una chiave di partizione definito dal sistema denominata `/_partitionkey` su tutti i contenitori che non hanno una chiave di partizione. È possibile modificare la definizione di chiave di partizione dopo la migrazione dei contenitori. Ad esempio, la definizione di un contenitore in cui viene eseguita la migrazione a un contenitore partizionato sarà come segue: 

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```
 
Dopo il contenitore viene eseguita la migrazione, è possibile creare documenti popolando la `_partitionKey` proprietà insieme alle altre proprietà del documento. Il `_partitionKey` proprietà rappresenta la chiave di partizione dei documenti. 

Scegliere la chiave di partizione corretta è importante usare in modo ottimale la velocità effettiva con provisioning. Per altre informazioni, vedere [come scegliere una chiave di partizione](partitioning-overview.md) articolo. 

> [!NOTE]
> È possibile sfruttare i vantaggi della chiave di partizione definito sistema solo se si usa la versione più recente/V3 degli SDK in tutte le lingue.

L'esempio seguente illustra un codice di esempio per creare un documento con la chiave di partizione definito sistema e leggere il documento:

**Rappresentazione JSON del documento**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

Per l'esempio completo, vedere la [esempi di .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) repository GitHub. 
                      
## <a name="migrate-the-documents"></a>Eseguire la migrazione dei documenti

Mentre la definizione del contenitore è stata migliorata con una proprietà chiave di partizione, i documenti all'interno del contenitore non vengono automaticamente migrati. Ovvero la proprietà di chiave di partizione di sistema `/_partitionKey` percorso non viene aggiunto automaticamente per i documenti esistenti. È necessario ripartizionare i documenti esistenti, leggere i documenti che sono stati creati senza una chiave di partizione e riscriverli con `_partitionKey` proprietà nei documenti. 

## <a name="access-documents-that-dont-have-a-partition-key"></a>Accedere a documenti che non hanno una chiave di partizione

Le applicazioni possono accedere i documenti esistenti che non hanno una chiave di partizione usando la proprietà di sistema speciale chiamata "CosmosContainerSettings.NonePartitionKeyValue", si tratta del valore dei documenti non migrati. È possibile usare questa proprietà in tutte le operazioni CRUD e di query. Nell'esempio seguente viene illustrato un esempio per leggere un singolo documento dal NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

Per l'esempio completo su come partizionare i documenti, vedere la [esempi di .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) repository GitHub. 

## <a name="compatibility-with-sdks"></a>Compatibilità con gli SDK

Versione precedente del SDK di Azure Cosmos DB, ad esempio V2.x.x e V1.x.x non supportano le proprietà di chiave di partizione definito dal sistema. Pertanto, quando si legge la definizione del contenitore da un SDK precedente, non contiene qualsiasi definizione di chiave di partizione e questi contenitori comporteranno esattamente come in precedenza. Le applicazioni compilate con la versione precedente del SDK continuano a funzionare con non partizionati perché è senza apportare modifiche. 

Se un contenitore migrato viene utilizzato per la versione più recente/V3 di SDK e iniziare la compilazione la chiave di partizione definito dal sistema all'interno dei documenti nuovi, non è possibile accedere (read, update, delete, query) tali documenti da SDK meno recenti più.

## <a name="next-steps"></a>Passaggi successivi

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](account-overview.md)