---
title: Creare e gestire uno snapshot BLOB in .NET-archiviazione di Azure
description: Informazioni su come creare uno snapshot di sola lettura di un BLOB per eseguire il backup dei dati BLOB in un determinato momento.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: fb2da8acb0aa4d105f23ab5d1ad42f08a6ae722c
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595243"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Creare e gestire uno snapshot BLOB in .NET

Uno snapshot è una versione di sola lettura di un BLOB eseguito in un determinato momento. Gli snapshot sono utili per il backup dei BLOB. Questo articolo illustra come creare e gestire snapshot BLOB usando la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage/client).

## <a name="about-blob-snapshots"></a>Informazioni sugli snapshot BLOB

Uno snapshot di un BLOB è identico al relativo BLOB di base, ad eccezione del fatto che all'URI del BLOB viene aggiunto un valore **DateTime** per indicare data e ora di acquisizione dello snapshot. Ad esempio, se un URI del BLOB di pagine è `http://storagesample.core.blob.windows.net/mydrives/myvhd`, l'URI dello snapshot è simile a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Tutti gli snapshot condividono l'URI del BLOB di base. L'unica distinzione tra il BLOB di base e lo snapshot è il valore **DateTime** aggiunto.
>

Un BLOB può avere un numero qualsiasi di snapshot. Gli snapshot vengono mantenuti fino a quando non vengono eliminati in modo esplicito, ovvero uno snapshot non può sopravvivere al relativo BLOB di base. È possibile enumerare gli snapshot associati al BLOB di base per tenere traccia degli snapshot correnti.

Quando si crea uno snapshot di un BLOB, le proprietà di sistema del BLOB vengono copiate nello snapshot con gli stessi valori. Anche i metadati del BLOB di base vengono copiati nello snapshot, se non si specificano metadati separati per lo snapshot durante la creazione. Dopo aver creato uno snapshot, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo.

Eventuali lease associati al BLOB di base non vengono copiati nello snapshot. Non è possibile acquisire un lease in uno snapshot.

Un file di disco rigido virtuale viene usato per archiviare lo stato e le informazioni correnti per il disco della macchina virtuale. È possibile scollegare il disco dall'interno della macchina virtuale o arrestare la macchina virtuale e quindi creare uno snapshot del relativo file di disco rigido virtuale. È possibile usare il file di snapshot successivamente per recuperare il file di disco rigido virtuale in quel determinato momento e ricreare la macchina virtuale.

## <a name="create-a-snapshot"></a>Creare uno snapshot

Per creare uno snapshot di un BLOB in blocchi, usare uno dei metodi seguenti:

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

Nell'esempio di codice seguente viene illustrato come creare uno snapshot. Questo esempio specifica metadati aggiuntivi per lo snapshot al momento della creazione.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="delete-snapshots"></a>Eliminare gli snapshot

Per eliminare un BLOB, è necessario innanzitutto eliminare gli snapshot del BLOB. È possibile eliminare uno snapshot singolarmente o specificare di eliminare tutti gli snapshot quando si elimina il BLOB di origine. Se si tenta di eliminare un BLOB per il quale esistono ancora degli snapshot, viene restituito un errore.

Per eliminare gli snapshot BLOB, usare uno dei metodi di eliminazione BLOB seguenti e includere l'enumerazione [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) .

- [Elimina](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

Nell'esempio di codice seguente viene illustrato come eliminare un BLOB e i relativi snapshot in .NET, dove `blockBlob` è un oggetto di tipo [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Restituire l'URI assoluto a uno snapshot

Nell'esempio di codice seguente viene creato uno snapshot e viene scritto l'URI assoluto per la posizione primaria.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Informazioni sull'incremento dei costi dovuto agli snapshot

La creazione di uno snapshot, una copia di sola lettura di un BLOB, può dar luogo a costi di archiviazione dei dati aggiuntivi sul conto. Nel progettare l'applicazione, è importante essere coscienti di come i costi possono aumentare, in modo da poterli ridurre al minimo.

### <a name="important-billing-considerations"></a>Considerazioni importanti sulla fatturazione

Nell'elenco seguente sono inclusi i punti principali da considerare quando si crea uno snapshot.

- Sono previsti costi per l'account di archiviazione per blocchi univoci o pagine, sia che si trovino nel BLOB che nello snapshot. Non sono previsti costi aggiuntivi per gli snapshot associati a un BLOB finché il BLOB su cui si basano non viene aggiornato. Dopo aver aggiornato il BLOB di base, questo differisce dai relativi snapshot. In questo caso, vengono addebitati costi per i blocchi univoci o le pagine in ogni BLOB o snapshot.
- Quando si sostituisce un blocco all'interno di un BLOB in blocchi, tale blocco viene successivamente addebitato come blocco univoco. Ciò è vero persino se il blocco ha lo stesso ID blocco e la stessa data che ha nello snapshot. Una volta che il blocco viene inviato nuovamente, differisce dalla controparte in ogni snapshot e all'utente verrà addebitato un costo per i relativi dati. Lo stesso vale per una pagina in un BLOB di pagine che viene aggiornata con dati identici.
- La sostituzione di un BLOB in blocchi chiamando il metodo [UploadFromFile][dotnet_UploadFromFile], [Uploadtext][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]o [UploadFromByteArray][dotnet_UploadFromByteArray] sostituisce tutti i blocchi nel BLOB. Se è presente uno snapshot associato al BLOB, tutti i blocchi del BLOB di base e lo snapshot a questo punto differiranno e all'utente verranno addebitati i costi di tutti i blocchi in entrambi i BLOB. Questo vale persino se i dati nel BLOB di base e nello snapshot restano identici.
- Il servizio BLOB di Azure non dispone dei mezzi per determinare se due blocchi contengono dati identici. Ogni blocco che viene caricato e inviato viene trattato come univoco, persino se contiene gli stessi dati e ha lo stesso ID blocco. Poiché i costi aumentano per i blocchi univoci, è importante considerare che se si aggiorna un BLOB contenente uno snapshot, si generano altri blocchi univoci e i costi aggiuntivi aumentano.

### <a name="minimize-cost-with-snapshot-management"></a>Ridurre al minimo i costi con la gestione degli snapshot

Si consiglia di gestire gli snapshot con attenzione per evitare costi supplementari. È possibile seguire queste procedure consigliate per ridurre al minimo i costi che sorgono con l'archiviazione degli snapshot:

- Eliminare e ricreare gli snapshot associati a un BLOB ogni volta che si aggiorna il BLOB, persino se l'aggiornamento viene eseguito con dati identici, a meno che la progettazione dell'applicazione non richieda di mantenerli. Se si eliminano e si ricreano gli snapshot del BLOB, ci si garantisce che il BLOB e gli snapshot non differiscano.
- Se si gestiscono snapshot per un BLOB, evitare di chiamare [UploadFromFile][dotnet_UploadFromFile], [Uploadtext][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]o [UploadFromByteArray][dotnet_UploadFromByteArray] per aggiornare il BLOB. Questi metodi sostituiscono tutti i blocchi nel BLOB, creando così delle differenze significative tra il BLOB di base e gli snapshot. Aggiornare invece il minor numero possibile di blocchi usando i metodi [PutBlock][dotnet_PutBlock] e [PutBlockList][dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Scenari di fatturazione degli snapshot

Gli scenari seguenti dimostrano in che modo aumentano i costi per un BLOB in blocchi e i relativi snapshot.

#### <a name="scenario-1"></a>Scenario 1

Nello Scenario 1, il BLOB di base non è stato aggiornato da quanto è stato scattato lo snapshot, pertanto i costi sono relativi sono ai blocchi univoci 1, 2 e 3.

![Risorse di archiviazione di Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

Nello Scenario 2, il BLOB di base è stato aggiornato, ma lo snapshot no. Il blocco 3 è stato aggiornato, e sebbene contenga gli stessi dati e lo stesso ID, non è lo stesso del blocco 3 nello snapshot. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Risorse di archiviazione di Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

Nello Scenario 3, il BLOB di base è stato aggiornato, ma lo snapshot no. Il blocco 3 è stato sostituito con il blocco 4 nel BLOB di base, ma lo snapshot continua a riflettere il blocco 3. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Risorse di archiviazione di Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

Nello Scenario 4, il BLOB di base è stato completamente aggiornato e non contiene nessuno dei blocchi originali. Di conseguenza, all'account vengono addebitati tutti gli otto blocchi univoci. Questo scenario può verificarsi se si usa un metodo di aggiornamento, ad esempio [UploadFromFile][dotnet_UploadFromFile], [Uploadtext][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]o [UploadFromByteArray][dotnet_UploadFromByteArray], perché questi metodi sostituiscono tutto il contenuto di un BLOB.

![Risorse di archiviazione di Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull'utilizzo di snapshot di dischi di macchina virtuale (VM) sono disponibili in [Eseguire il backup dei dischi di VM non gestiti con snapshot incrementali](../../virtual-machines/windows/incremental-snapshots.md).

- Per altri esempi di codice che usano l'archiviazione BLOB, vedere [Esempi di codice per Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). È possibile scaricare un'applicazione di esempio ed eseguirla oppure esaminare il codice in GitHub.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext
