<properties
	pageTitle="Creare snapshot di sola lettura di BLOB | Microsoft Azure"
	description="Scoprire come creare snapshot di un BLOB per eseguire il backup dei dati BLOB in un determinato momento. Comprendere come vengono fatturati gli snapshot e come utilizzarli per ridurre i costi di capacità."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2016"
	ms.author="tamram"/>

# Creare uno snapshot del BLOB

## Panoramica

Uno snapshot è una versione di sola lettura di un BLOB eseguito in un determinato momento. Gli snapshot sono utili per il backup dei BLOB. Una volta creato uno snapshot, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo.

Uno snapshot di un BLOB ha lo stesso nome del BLOB di base da cui deriva, con un valore **DateTime** aggiunto per indicare l'ora in cui lo snapshot è stato acquisito. Ad esempio, se un URI del BLOB di pagine`http://storagesample.core.blob.windows.net/mydrives/myvhd` è , l'URI dello snapshot sarà simile a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. Tutti gli snapshot di un BLOB ne condividono l'URI e si distinguono solo per il valore **DateTime** aggiunto.

Un BLOB può avere un numero qualsiasi di snapshot. Gli snapshot vengono mantenuti fino all'eliminazione esplicita. Uno snapshot non può sopravvivere al relativo BLOB di origine. È possibile enumerare gli snapshot associati al BLOB per tenere traccia degli snapshot correnti.

Quando si crea uno snapshot di un BLOB, le proprietà di sistema del BLOB vengono copiate nello snapshot con gli stessi valori.

Eventuali lease associati al BLOB di base non vengono copiati nello snapshot. Non è possibile acquisire un lease in uno snapshot.

## Copiare gli snapshot

Le operazioni di copia che interessano BLOB e snapshot si attengono alle seguenti regole:

- È possibile copiare uno snapshot sul relativo BLOB di base. Promuovendo uno snapshot alla posizione del BLOB di base, è possibile ripristinare la versione precedente di un BLOB. Lo snapshot resta, ma il BLOB di base viene sovrascritto con una copia scrivibile dello snapshot.

- È possibile copiare uno snapshot in un BLOB di destinazione con un nome diverso. Il BLOB di destinazione risultante è un BLOB scrivibile, non uno snapshot.

- Quando si copia un BLOB di origine, gli snapshot del BLOB di origine non vengono copiati nella destinazione. Quando un BLOB di destinazione viene sovrascritto con una copia, gli snapshot associati al BLOB di destinazione al momento della sovrascrittura rimangono invariati con il relativo nome.

- Quando si crea uno snapshot di un BLOB in blocchi, anche l'elenco di blocchi di cui è stato eseguito il commit del BLOB viene copiato nello snapshot. Eventuali blocchi di cui non è stato eseguito il commit non vengono copiati.

## Specificare una condizione di accesso

È possibile specificare una condizione di accesso in modo da creare lo snapshot solo se viene soddisfatta tale condizione. Per specificare una condizione di accesso, usare la proprietà **AccessCondition**. Se la condizione specificata non viene soddisfatta, lo snapshot non viene creato e il servizio BLOB restituisce il codice di stato HTTPStatusCode.PreconditionFailed.

## Eliminare gli snapshot

Non è possibile eliminare un BLOB contenente snapshot a meno che non vengano eliminati anche gli snapshot. È possibile eliminare uno snapshot singolarmente o indicare al servizio di archiviazione di eliminare tutti gli snapshot quando si elimina il BLOB di origine. Se si tenta di eliminare un BLOB che presenta ancora degli snapshot, viene restituito un errore.

## Snapshot con archiviazione Premium di Azure
Per usare snapshot con Archiviazione Premium è necessario attenersi alle regole seguenti:

- Il numero di snapshot per BLOB di pagine in un account di Archiviazione Premium è limitato a 100. Se tale limite viene superato, l'operazione Snapshot BLOB restituisce il codice errore 409 (**SnapshotCountExceeded**).

- Uno snapshot di un BLOB di pagine in un account di Archiviazione Premium può essere adottato una volta ogni dieci minuti. Se tale frequenza viene superata, l'operazione Snapshot BLOB restituisce il codice errore 409 (**SnaphotOperationRateExceeded**).

- La lettura di uno snapshot di un BLOB di pagine in un account di Archiviazione Premium tramite l'operazione Get BLOB non è supportata. La chiamata Get BLOB per uno snapshot in un account di Archiviazione Premium restituisce il codice errore 400 (**InvalidOperation**). Tuttavia, le chiamate Get Blob Properties e Get Blob Metadata su uno snapshot sono supportate.

- Per leggere uno snapshot, è possibile usare l'operazione Copy BLOB per copiare uno snapshot in un altro BLOB di pagine nell'account. Il BLOB di destinazione per l'operazione di copia non deve contenere snapshot. Se il BLOB di destinazione contiene degli snapshot, l'operazione Copy BLOB restituisce il codice errore 409 (**SnapshotsPresent**).

## Restituire l'URI assoluto a uno snapshot

Questo esempio di codice c# consente di creare un nuovo snapshot e scrive l'URI assoluto per la posizione primaria.

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



## Informazioni sull'incremento dei costi dovuto agli snapshot

La creazione di uno snapshot, una copia di sola lettura di un BLOB, può dar luogo a costi di archiviazione dei dati aggiuntivi sul conto. Nel progettare l'applicazione, è importante essere a conoscenza di come i costi possono aumentare, in modo da poter ridurre al minimo i costi inutili.

### Considerazioni importanti sulla fatturazione

Nell'elenco seguente sono inclusi i punti principali da considerare quando si crea uno snapshot.

- Sono previsti dei costi per blocchi univoci o pagine, che si trovino nel BLOB o nello snapshot. Non sono previsti costi aggiuntivi per gli snapshot associati a un BLOB finché il BLOB su cui si basano non viene aggiornato. Una volta aggiornato il BLOB di base, questo differisce dai relativi snapshot e all'utente vengono addebitati i blocchi univoci o le pagine di ogni BLOB o snapshot.

- Quando si sostituisce un blocco all'interno di un BLOB in blocchi, tale blocco viene successivamente addebitato come blocco univoco. Ciò è vero persino se il blocco ha lo stesso ID blocco e la stessa data che ha nello snapshot. Una volta che il blocco viene inviato nuovamente, differisce dalla controparte in ogni snapshot e all'utente verrà addebitato un costo per i relativi dati. Lo stesso rimane vero per una pagina in un BLOB di pagine che viene aggiornata con dati identici.

- Se si sostituisce un BLOB in blocchi richiamando il metodo **UploadFile**, **UploadText**, **UploadStream** o **UploadByteArray** vengono sostituiti tutti i blocchi di quel BLOB. Se si dispone di uno snapshot associato al BLOB, tutti i blocchi del BLOB di base e lo snapshot ora differiranno e all'utente verranno addebitati i costi di tutti i blocchi in entrambi i BLOB. Questo vale persino se i dati nel BLOB di base e nello snapshot restano identici.

- Il servizio BLOB di Azure non dispone dei mezzi per determinare se due blocchi contengono dati identici. Ogni blocco che viene caricato e inviato viene trattato come univoco, persino se contiene gli stessi dati e ha lo stesso ID blocco. Poiché i costi aumentano per i blocchi univoci, è importante considerare che se si aggiorna un BLOB che contiene uno snapshot si genereranno altri blocchi univoci e costi aggiuntivi.

> [AZURE.NOTE] Le procedure consigliate indicano di gestire gli snapshot con attenzione per evitare costi supplementari. È consigliabile gestire gli snapshot nel modo descritto di seguito:

> - Eliminare e ricreare gli snapshot associati a un BLOB ogni volta che si aggiorna il BLOB, persino se l'aggiornamento viene eseguito con dati identici, a meno che la progettazione dell'applicazione non richieda di mantenerli. Eliminando e ricreando gli snapshot del BLOB è possibile essere sicuri che il BLOB e gli snapshot non differiscano.

> - Se si stanno gestendo gli snapshot di un BLOB, evitare di chiamare **UploadFile**, **UploadText**, **UploadStream**, o **UploadByteArray** per aggiornare il BLOB, in quanto questi metodi sostituiscono tutti i blocchi del BLOB. Aggiornare, invece, il minor numero possibile di blocchi usando i metodi **PutBlock** e **PutBlockList**.


### Scenari di fatturazione degli snapshot


Gli scenari seguenti dimostrano in che modo aumentano i costi per un BLOB in blocchi e i relativi snapshot.

Nello Scenario 1, il BLOB di base non è stato aggiornato da quanto è stato scattato lo snapshot, pertanto i costi sono relativi sono ai blocchi univoci 1, 2 e 3.

![Risorse di archiviazione di Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

Nello Scenario 2, il BLOB di base è stato aggiornato, ma lo snapshot no. Il blocco 3 è stato aggiornato, e sebbene contenga gli stessi dati e lo stesso ID, non è lo stesso del blocco 3 nello snapshot. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Risorse di archiviazione di Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

Nello Scenario 3, il BLOB di base è stato aggiornato, ma lo snapshot no. Il blocco 3 è stato sostituito con il blocco 4 nel BLOB di base, ma lo snapshot continua a riflettere il blocco 3. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Risorse di archiviazione di Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

Nello Scenario 4, il BLOB di base è stato completamente aggiornato e non contiene nessuno dei blocchi originali. Di conseguenza, all'account vengono addebitati tutti gli otto blocchi univoci. Questo scenario può verificarsi se si sta usando un metodo di aggiornamento come **UploadFile**, **UploadText**, **UploadFromStream** o **UploadByteArray**, in quanto questi metodi sostituiscono tutto il contenuto di un BLOB.

![Risorse di archiviazione di Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

<!---HONumber=AcomDC_0601_2016-->