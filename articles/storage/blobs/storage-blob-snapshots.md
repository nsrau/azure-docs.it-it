---
title: Creare uno snapshot di sola lettura di un BLOB in Archiviazione di Azure | Microsoft Docs
description: "Scoprire come creare snapshot di un BLOB per eseguire il backup dei dati BLOB in un determinato momento. Comprendere come vengono fatturati gli snapshot e come utilizzarli per ridurre i costi di capacità."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 04/11/2017
ms.author: tamram
ms.openlocfilehash: cba28ada79ea806ead4ae9165abba2dc4e04f001
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-blob-snapshot"></a>Creare uno snapshot del BLOB

Uno snapshot è una versione di sola lettura di un BLOB eseguito in un determinato momento. Gli snapshot sono utili per il backup dei BLOB. Dopo aver creato uno snapshot, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo.

Uno snapshot di un BLOB è identico al relativo BLOB di base, ad eccezione del fatto che all'URI del BLOB viene aggiunto un valore **DateTime** per indicare data e ora di acquisizione dello snapshot. Ad esempio, se un URI del BLOB di pagine è `http://storagesample.core.blob.windows.net/mydrives/myvhd`, l'URI dello snapshot è simile a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Tutti gli snapshot condividono l'URI del BLOB di base. L'unica distinzione tra il BLOB di base e lo snapshot è il valore **DateTime** aggiunto.
>

Un BLOB può avere un numero qualsiasi di snapshot. Gli snapshot vengono mantenuti fino all'eliminazione esplicita. Uno snapshot non può sopravvivere al relativo BLOB di base. È possibile enumerare gli snapshot associati al BLOB di base per tenere traccia degli snapshot correnti.

Quando si crea uno snapshot di un BLOB, le proprietà di sistema del BLOB vengono copiate nello snapshot con gli stessi valori. Anche i metadati del BLOB di base vengono copiati nello snapshot, se non si specificano metadati separati per lo snapshot durante la creazione.

Eventuali lease associati al BLOB di base non vengono copiati nello snapshot. Non è possibile acquisire un lease in uno snapshot.

Un file di disco rigido virtuale viene usato per archiviare lo stato e le informazioni correnti per il disco della macchina virtuale. È possibile scollegare il disco dall'interno della macchina virtuale o arrestare la macchina virtuale e quindi creare uno snapshot del relativo file di disco rigido virtuale. È possibile usare il file di snapshot successivamente per recuperare il file di disco rigido virtuale in quel determinato momento e ricreare la macchina virtuale.

Se la Crittografia del servizio di archiviazione (SSE) è abilitata per l'account di archiviazione in cui si trova il BLOB, per gli snapshot del BLOB verrà eseguita la crittografia dei dati inattivi.

## <a name="create-a-snapshot"></a>Creare uno snapshot
L'esempio seguente illustra come creare uno snapshot usando la [libreria client di Archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Questo esempio specifica metadati aggiuntivi per lo snapshot al momento della creazione.

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
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
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

## <a name="copy-snapshots"></a>Copiare gli snapshot
Le operazioni di copia che interessano BLOB e snapshot si attengono alle seguenti regole:

* È possibile copiare uno snapshot sul relativo BLOB di base. Promuovendo uno snapshot alla posizione del BLOB di base, è possibile ripristinare la versione precedente di un BLOB. Lo snapshot resta, ma il BLOB di base viene sovrascritto con una copia scrivibile dello snapshot.
* È possibile copiare uno snapshot in un BLOB di destinazione con un nome diverso. Il BLOB di destinazione risultante è un BLOB scrivibile, non uno snapshot.
* Quando si copia un BLOB di origine, gli snapshot del BLOB di origine non vengono copiati nella destinazione. Quando un BLOB di destinazione viene sovrascritto con una copia, gli eventuali snapshot associati al BLOB di destinazione originale rimangono invariati.
* Quando si crea uno snapshot di un BLOB in blocchi, anche l'elenco di blocchi di cui è stato eseguito il commit del BLOB viene copiato nello snapshot. Eventuali blocchi di cui non è stato eseguito il commit non vengono copiati.

## <a name="specify-an-access-condition"></a>Specificare una condizione di accesso
Quando si chiama [CreateSnapshotAsync][dotnet_CreateSnapshotAsync], è possibile specificare una condizione di accesso in modo da creare lo snapshot solo se viene soddisfatta tale condizione. Per specificare una condizione di accesso, usare il parametro [AccessCondition][dotnet_AccessCondition]. Se la condizione specificata non viene soddisfatta, lo snapshot non viene creato e il servizio BLOB restituisce il codice di stato [HTTPStatusCode][dotnet_HTTPStatusCode].PreconditionFailed.

## <a name="delete-snapshots"></a>Eliminare gli snapshot
Non è possibile eliminare un BLOB contenente snapshot a meno che non si eliminino anche gli snapshot. È possibile eliminare uno snapshot singolarmente o specificare di eliminare tutti gli snapshot quando si elimina il BLOB di origine. Se si tenta di eliminare un BLOB per il quale esistono ancora degli snapshot, viene restituito un errore.

L'esempio seguente illustra come eliminare un BLOB e i relativi snapshot in .NET, dove `blockBlob` è un oggetto di tipo [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Snapshot con archiviazione Premium di Azure
Per usare snapshot con Archiviazione Premium è necessario attenersi alle regole seguenti:

* Il numero massimo di snapshot per BLOB di pagine in un account di archiviazione Premium è 100. Se si supera questo limite, l'operazione Snapshot BLOB restituisce il codice errore 409 (`SnapshotCountExceeded`).
* È possibile creare uno snapshot di un BLOB di pagine in un account di archiviazione Premium una volta ogni 10 minuti. Se si supera questa frequenza, l'operazione Snapshot BLOB restituisce il codice errore 409 (`SnapshotOperationRateExceeded`).
* Per leggere uno snapshot, è possibile usare l'operazione Copy BLOB per copiare uno snapshot in un altro BLOB di pagine nell'account. Il BLOB di destinazione per l'operazione di copia non deve contenere snapshot. Se il BLOB di destinazione contiene snapshot, l'operazione Copy BLOB restituisce il codice errore 409 (`SnapshotsPresent`).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Restituire l'URI assoluto a uno snapshot
Questo esempio di codice C# consente di creare uno snapshot e scrive l'URI assoluto per la posizione primaria.

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

* Sono previsti costi per l'account di archiviazione per blocchi univoci o pagine, sia che si trovino nel BLOB che nello snapshot. Non sono previsti costi aggiuntivi per gli snapshot associati a un BLOB finché il BLOB su cui si basano non viene aggiornato. Dopo aver aggiornato il BLOB di base, questo differisce dai relativi snapshot. In questo caso, vengono addebitati costi per i blocchi univoci o le pagine in ogni BLOB o snapshot.
* Quando si sostituisce un blocco all'interno di un BLOB in blocchi, tale blocco viene successivamente addebitato come blocco univoco. Ciò è vero persino se il blocco ha lo stesso ID blocco e la stessa data che ha nello snapshot. Una volta che il blocco viene inviato nuovamente, differisce dalla controparte in ogni snapshot e all'utente verrà addebitato un costo per i relativi dati. Lo stesso vale per una pagina in un BLOB di pagine che viene aggiornata con dati identici.
* Se si sostituisce un BLOB in blocchi tramite chiamata al metodo [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] o [UploadFromByteArray][dotnet_UploadFromByteArray], il metodo sostituisce tutti i blocchi nel BLOB. Se è presente uno snapshot associato al BLOB, tutti i blocchi del BLOB di base e lo snapshot a questo punto differiranno e all'utente verranno addebitati i costi di tutti i blocchi in entrambi i BLOB. Questo vale persino se i dati nel BLOB di base e nello snapshot restano identici.
* Il servizio BLOB di Azure non dispone dei mezzi per determinare se due blocchi contengono dati identici. Ogni blocco che viene caricato e inviato viene trattato come univoco, persino se contiene gli stessi dati e ha lo stesso ID blocco. Poiché i costi aumentano per i blocchi univoci, è importante considerare che se si aggiorna un BLOB contenente uno snapshot, si generano altri blocchi univoci e i costi aggiuntivi aumentano.

### <a name="minimize-cost-with-snapshot-management"></a>Ridurre al minimo i costi con la gestione degli snapshot

Si consiglia di gestire gli snapshot con attenzione per evitare costi supplementari. È possibile seguire queste procedure consigliate per ridurre al minimo i costi che sorgono con l'archiviazione degli snapshot:

* Eliminare e ricreare gli snapshot associati a un BLOB ogni volta che si aggiorna il BLOB, persino se l'aggiornamento viene eseguito con dati identici, a meno che la progettazione dell'applicazione non richieda di mantenerli. Se si eliminano e si ricreano gli snapshot del BLOB, ci si garantisce che il BLOB e gli snapshot non differiscano.
* Quando si gestiscono gli snapshot per un BLOB, evitare di chiamare il metodo [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] o [UploadFromByteArray][dotnet_UploadFromByteArray] per aggiornare il BLOB. Questi metodi sostituiscono tutti i blocchi nel BLOB, creando così delle differenze significative tra il BLOB di base e gli snapshot. Aggiornare invece il minor numero possibile di blocchi usando i metodi [PutBlock][dotnet_PutBlock] e [PutBlockList][dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Scenari di fatturazione degli snapshot
Gli scenari seguenti dimostrano in che modo aumentano i costi per un BLOB in blocchi e i relativi snapshot.

**Scenario 1**

Nello Scenario 1, il BLOB di base non è stato aggiornato da quanto è stato scattato lo snapshot, pertanto i costi sono relativi sono ai blocchi univoci 1, 2 e 3.

![Risorse di archiviazione di Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**Scenario 2**

Nello Scenario 2, il BLOB di base è stato aggiornato, ma lo snapshot no. Il blocco 3 è stato aggiornato, e sebbene contenga gli stessi dati e lo stesso ID, non è lo stesso del blocco 3 nello snapshot. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Risorse di archiviazione di Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**Scenario 3**

Nello Scenario 3, il BLOB di base è stato aggiornato, ma lo snapshot no. Il blocco 3 è stato sostituito con il blocco 4 nel BLOB di base, ma lo snapshot continua a riflettere il blocco 3. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Risorse di archiviazione di Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**Scenario 4**

Nello Scenario 4, il BLOB di base è stato completamente aggiornato e non contiene nessuno dei blocchi originali. Di conseguenza, all'account vengono addebitati tutti gli otto blocchi univoci. Questo scenario può verificarsi se si usa un metodo di aggiornamento come [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] o [UploadFromByteArray][dotnet_UploadFromByteArray], in quanto questi metodi sostituiscono tutto il contenuto di un BLOB.

![Risorse di archiviazione di Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'utilizzo di snapshot di dischi di macchina virtuale (VM) sono disponibili in [Eseguire il backup dei dischi di VM non gestiti con snapshot incrementali](../../virtual-machines/windows/incremental-snapshots.md).

* Per altri esempi di codice che usano l'archiviazione BLOB, vedere [Esempi di codice per Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). È possibile scaricare un'applicazione di esempio ed eseguirla oppure esaminare il codice in GitHub.

[dotnet_AccessCondition]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.accesscondition.aspx
[dotnet_CloudBlockBlob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx
[dotnet_CreateSnapshotAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.createsnapshotasync.aspx
[dotnet_HTTPStatusCode]: https://msdn.microsoft.com/library/system.net.httpstatuscode(v=vs.110).aspx
[dotnet_PutBlockList]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblocklist.aspx
[dotnet_PutBlock]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblock.aspx
[dotnet_UploadFromByteArray]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfrombytearray.aspx
[dotnet_UploadFromFile]: https://msdn.microsoft.com/library/azure/mt705654.aspx
[dotnet_UploadFromStream]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstream.aspx
[dotnet_UploadText]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadtext.aspx