---
title: Eseguire la migrazione di contenitori di Azure Cosmos non partizionati a contenitori partizionati
description: Informazioni su come eseguire la migrazione di tutti i contenitori non partizionati esistenti in contenitori partizionati.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/25/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: f2defeb2c417e94109721d32d8d4b611e2e46569
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003219"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Eseguire la migrazione di contenitori non partizionati a contenitori partizionati

Azure Cosmos DB supporta la creazione di contenitori senza una chiave di partizione. Attualmente è possibile creare contenitori non partizionati usando l'interfaccia della riga di comando di Azure e Azure Cosmos DB SDK (.NET, Java, NodeJs) con una versione inferiore o uguale a 2. x. Non è possibile creare contenitori non partizionati utilizzando il portale di Azure. Tuttavia, tali contenitori non partizionati non sono elastici e hanno una capacità di archiviazione fissa di 20 GB e un limite di velocità effettiva di 10.000 UR/s.

I contenitori non partizionati sono legacy ed è necessario eseguire la migrazione dei contenitori non partizionati esistenti ai contenitori partizionati per ridimensionare l'archiviazione e la velocità effettiva. Azure Cosmos DB fornisce un meccanismo definito dal sistema per la migrazione dei contenitori non partizionati ai contenitori partizionati. Questo documento illustra come viene eseguita la migrazione automatica di tutti i contenitori non partizionati esistenti in contenitori partizionati. È possibile sfruttare la funzionalità di migrazione automatica solo se si usa la versione V3 degli SDK in tutte le lingue.

> [!NOTE]
> Attualmente, non è possibile eseguire la migrazione di Azure Cosmos DB account API MongoDB e Gremlin usando i passaggi descritti in questo documento.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Eseguire la migrazione del contenitore con la chiave di partizione definita dal sistema

Per supportare la migrazione, Azure Cosmos DB fornisce una chiave di partizione definita dal sistema denominata `/_partitionkey` in tutti i contenitori che non dispongono di una chiave di partizione. Non è possibile modificare la definizione della chiave di partizione dopo la migrazione dei contenitori. Ad esempio, la definizione di un contenitore di cui viene eseguita la migrazione a un contenitore partizionato sarà la seguente:

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

Dopo la migrazione del contenitore, è possibile creare i documenti popolando la `_partitionKey` Proprietà insieme alle altre proprietà del documento. La `_partitionKey` proprietà rappresenta la chiave di partizione dei documenti.

La scelta della chiave di partizione corretta è importante per l'uso ottimale della velocità effettiva con provisioning. Per altre informazioni, vedere [How to choose a Partition Key](partitioning-overview.md) article.

> [!NOTE]
> È possibile sfruttare la chiave di partizione definita dal sistema solo se si usa la versione più recente/V3 degli SDK in tutte le lingue.

Nell'esempio seguente viene illustrato un codice di esempio per creare un documento con la chiave di partizione definita dal sistema e leggere il documento:

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

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

Per l'esempio completo, vedere il repository GitHub degli [esempi .NET][1] .
                      
## <a name="migrate-the-documents"></a>Migrare i documenti

Mentre la definizione del contenitore viene migliorata con una proprietà della chiave di partizione, i documenti all'interno del contenitore non vengono migrati automaticamente. Il che significa che il percorso della proprietà chiave `/_partitionKey` di partizione di sistema non viene aggiunto automaticamente ai documenti esistenti. È necessario ripartizionare i documenti esistenti leggendo i documenti creati senza una chiave di partizione e riscriverli con `_partitionKey` la proprietà nei documenti.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Accedere ai documenti che non dispongono di una chiave di partizione

Le applicazioni possono accedere ai documenti esistenti che non dispongono di una chiave di partizione utilizzando la speciale proprietà di sistema denominata "PartitionKey. None", ovvero il valore dei documenti non migrati. È possibile usare questa proprietà in tutte le operazioni CRUD e di query. Nell'esempio seguente viene illustrato un esempio per la lettura di un singolo documento da NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Per l'esempio completo su come ripartizionare i documenti, vedere il repository GitHub degli [esempi .NET][1] . 

## <a name="compatibility-with-sdks"></a>Compatibilità con gli SDK

La versione precedente di Azure Cosmos DB SDK, ad esempio V2. x. x e V1. x. x, non supportano la proprietà della chiave di partizione definita dal sistema. Quindi, quando si legge la definizione del contenitore da un SDK precedente, non contiene alcuna definizione della chiave di partizione e questi contenitori si comportano esattamente come prima. Le applicazioni compilate con la versione precedente degli SDK continuano a funzionare con non partizionate così come sono senza alcuna modifica. 

Se un contenitore migrato viene usato dalla versione più recente/V3 dell'SDK e si inizia a popolare la chiave di partizione definita dal sistema all'interno dei nuovi documenti, non è più possibile accedere (leggere, aggiornare, eliminare, eseguire query) tali documenti dagli SDK precedenti.

## <a name="known-issues"></a>Problemi noti

**L'esecuzione di query per il numero di elementi inseriti senza una chiave di partizione con V3 SDK può comportare un consumo di velocità effettiva superiore**

Se si esegue una query da V3 SDK per gli elementi inseriti usando V2 SDK o gli elementi inseriti con l'SDK V3 con il `PartitionKey.None` parametro, la query count potrebbe usare più ur/s se il `PartitionKey.None` parametro viene fornito in FeedOptions. Si consiglia di non specificare il `PartitionKey.None` parametro se nessun altro elemento viene inserito con una chiave di partizione.

Se vengono inseriti nuovi elementi con valori diversi per la chiave di partizione, l'esecuzione di query per tali conteggi di elementi passando la chiave appropriata in `FeedOptions` non avrà alcun problema. Dopo aver inserito i nuovi documenti con la chiave di partizione, se è necessario eseguire una query solo sul numero di documenti senza il valore della chiave di partizione, è possibile che la query comporti un numero maggiore di ur/sec simile alle raccolte partizionate regolari.

## <a name="next-steps"></a>Passaggi successivi

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration
