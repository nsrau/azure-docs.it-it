---
title: Eseguire la migrazione di contenitori di Azure Cosmos non partizionati in contenitori partizionatiMigrate non-partitioned Azure Cosmos containers to partitioned containers
description: Informazioni su come eseguire la migrazione di tutti i contenitori non partizionati esistenti in contenitori partizionati.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 742ef62895f3ef64e8fa22ab21d2947bee57776b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623358"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Eseguire la migrazione di contenitori non partizionati in contenitori partizionatiMigrate non-partitioned containers to partitioned containers

Il database Cosmos di Azure supporta la creazione di contenitori senza una chiave di partizione. Attualmente è possibile creare contenitori non partizionati usando l'interfaccia della riga di comando di Azure e gli SDK di Azure Cosmos DB (.Net, Java, NodeJs) con una versione minore o uguale a 2.x. Non è possibile creare contenitori non partizionati tramite il portale di Azure.You cannot create non-partitioned containers using the Azure portal. Tuttavia, tali contenitori non partizionati non sono elastici e hanno una capacità di archiviazione fissa di 20 GB e un limite di velocità effettiva di 10K RU/s.

I contenitori non partizionati sono legacy ed è consigliabile eseguire la migrazione dei contenitori non partizionati esistenti in contenitori partizionati per ridimensionare l'archiviazione e la velocità effettiva. Azure Cosmos DB offre un meccanismo definito dal sistema per eseguire la migrazione dei contenitori non partizionati in contenitori partizionati. In questo documento viene illustrato come tutti i contenitori non partizionati esistenti vengono migrati automaticamente in contenitori partizionati. È possibile sfruttare la funzionalità di migrazione automatica solo se si utilizza la versione V3 degli SDK in tutte le lingue.

> [!NOTE]
> Attualmente, non è possibile eseguire la migrazione degli account API MongoDB e Gremlin di Azure Cosmos usando i passaggi descritti in questo documento.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Eseguire la migrazione del contenitore utilizzando la chiave di partizione definita dal sistemaMigrate container using the system defined partition key

Per supportare la migrazione, Azure Cosmos DB `/_partitionkey` fornisce una chiave di partizione definita dal sistema denominata in tutti i contenitori che non dispongono di una chiave di partizione. Non è possibile modificare la definizione della chiave di partizione dopo la migrazione dei contenitori. Ad esempio, la definizione di un contenitore di cui viene eseguita la migrazione in un contenitore partizionato sarà la seguente:For example, the definition of a container that is migrated to a partitioned container will be as follows:

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

Dopo la migrazione del contenitore, è possibile creare `_partitionKey` documenti popolando la proprietà insieme alle altre proprietà del documento. La `_partitionKey` proprietà rappresenta la chiave di partizione dei documenti.

La scelta della chiave di partizione corretta è importante per utilizzare la velocità effettiva di cui è stato eseguito il provisioning in modo ottimale. Per altre informazioni, vedere come scegliere un articolo sulla chiave di [partizione.](partitioning-overview.md)

> [!NOTE]
> È possibile sfruttare la chiave di partizione definita dal sistema solo se si utilizza la versione più recente/V3 degli SDK in tutte le lingue.

L'esempio seguente mostra un codice di esempio per creare un documento con la chiave di partizione definita dal sistema e leggere tale documento:The following example shows a sample code to create a document with the system defined partition key and read that document:

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

Per l'esempio completo, vedere il repository [GitHub degli esempi .Net.For][1] the complete sample, see the .Net samples GitHub repository.
                      
## <a name="migrate-the-documents"></a>Eseguire la migrazione dei documenti

Mentre la definizione del contenitore viene migliorata con una proprietà della chiave di partizione, i documenti all'interno del contenitore non vengono migrati automaticamente. Ciò significa che `/_partitionKey` il percorso della proprietà della chiave della partizione di sistema non viene aggiunto automaticamente ai documenti esistenti. È necessario ripartizionare i documenti esistenti leggendo i documenti creati senza una `_partitionKey` chiave di partizione e riscriverli nuovamente con la proprietà nei documenti.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Accedere a documenti che non dispongono di una chiave di partizioneAccess documents don't have a partition key

Le applicazioni possono accedere ai documenti esistenti che non dispongono di una chiave di partizione utilizzando la proprietà di sistema speciale denominata "PartitionKey.None", questo è il valore dei documenti non migrati. È possibile utilizzare questa proprietà in tutte le operazioni CRUD e di query. The following example shows a sample to read a single Document from the NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Per l'esempio completo su come ripartizionare i documenti, vedere il repository [GitHub di esempi .Net.][1] 

## <a name="compatibility-with-sdks"></a>Compatibilità con gli SDK

Le versioni precedenti degli SDK del database Cosmos di Azure, ad esempio V2.x.x e V1.x.x, non supportano la proprietà della chiave di partizione definita dal sistema. Pertanto, quando si legge la definizione del contenitore da un SDK precedente, non contiene alcuna definizione di chiave di partizione e questi contenitori si comporterà esattamente come prima. Le applicazioni compilate con la versione precedente degli SDK continuano a funzionare con le applicazioni non partizionate senza alcuna modifica. 

Se un contenitore migrato viene utilizzato dalla versione più recente/V3 di SDK e si inizia a popolare la chiave di partizione definita dal sistema all'interno dei nuovi documenti, non è più possibile accedere (lettura, aggiornamento, eliminazione, query) a tali documenti dagli SDK meno recenti.

## <a name="known-issues"></a>Problemi noti

**L'esecuzione di query per il conteggio degli elementi che sono stati inseriti senza una chiave di partizione utilizzando V3 SDK può comportare un maggiore consumo di velocità effettiva**

Se si esegue una query da V3 SDK per gli elementi che vengono inseriti utilizzando `PartitionKey.None` V2 SDK o gli elementi inseriti utilizzando il V3 SDK con parametro, la query di conteggio può consumare più RU/s se il `PartitionKey.None` parametro viene fornito in FeedOptions. È consigliabile non fornire `PartitionKey.None` il parametro se non vengono inseriti altri elementi con una chiave di partizione.

Se vengono inseriti nuovi elementi con valori diversi per la chiave di `FeedOptions` partizione, l'esecuzione di query per tali conteggi di elementi passando la chiave appropriata non avrà problemi. Dopo aver inserito nuovi documenti con chiave di partizione, se è necessario eseguire una query solo sul conteggio dei documenti senza il valore della chiave di partizione, tale query può incorrere nuovamente in RU/s più elevati simili alle normali raccolte partizionate.

## <a name="next-steps"></a>Passaggi successivi

* [Partizionamento in Azure Cosmos DB](partitioning-overview.md)
* [Unità di richiesta in Azure Cosmos DBRequest Units in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration