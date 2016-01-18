<properties 
	pageTitle="Gestire l'accesso in lettura anonimo a contenitori e BLOB | Microsoft Azure" 
	description="Informazioni su come rendere disponibili per l'accesso anonimo contenitori e BLOB e su come accedervi a livello di programmazione." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="carmonm" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="tamram"/>

# Gestire l'accesso in lettura anonimo a contenitori e BLOB

## Panoramica

Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere alle risorse relative all’account. Solo per l'archiviazione BLOB, è possibile impostare le autorizzazioni di un contenitore per consentire l'accesso in lettura anonimo al contenitore e ai relativi BLOB, in modo che sia possibile concedere l'accesso a tali risorse senza condividere la chiave dell'account.

L'accesso anonimo è ideale per scenari in cui si desidera che alcuni BLOB siano sempre disponibili per l'accesso in lettura anonimo. Per un controllo più capillare, è possibile creare una firma di accesso condiviso, che consente l'accesso delegato limitato con autorizzazioni diverse e per un intervallo di tempo specificato. Per altre informazioni sulla creazione di firme di accesso condiviso, vedere [Firme di accesso condiviso: informazioni sul modello di firma di accesso condiviso (SAS)](storage-dotnet-shared-access-signature-part-1.md).

## Concedere le autorizzazioni agli utenti anonimi per contenitori e BLOB

Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere a un contenitore e ai BLOB in esso contenuti. Se si desidera assegnare a utenti anonimi autorizzazioni di lettura per un contenitore e i relativi BLOB, è possibile impostare le autorizzazioni del contenitore per consentire l'accesso pubblico. Gli utenti anonimi possono leggere i BLOB presenti in un contenitore accessibile pubblicamente senza effettuare l'autenticazione della richiesta.

I contenitori forniscono le seguenti opzioni per la gestione dell'accesso al contenitore:

- **Accesso in lettura pubblico completo:** I dati BLOB e del contenitore possono essere letti tramite richiesta anonima. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

- **Accesso in lettura pubblico solo per i BLOB:** I dati BLOB all'interno di questo contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client non possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima.

- **Nessun accesso in lettura pubblico:** I dati BLOB e del contenitore possono essere letti solo dal proprietario dell'account.

È possibile impostare le autorizzazioni per il contenitore nei modi seguenti:

- Dal [portale di Azure](portal.azure.com).
- A livello di programmazione, tramite la libreria client di archiviazione o l'API REST.
- Con PowerShell. Per informazioni su come impostare le autorizzazioni per il contenitore da Azure PowerShell, vedere [Uso di Azure PowerShell con Archiviazione di Azure](storage-powershell-guide-full#how-to-manage-azure-blobs)

### Impostazione delle autorizzazioni per il contenitore dal portale di Azure

Per impostare le autorizzazioni per il contenitore dal [portale di Azure](portal.azure.com), eseguire le operazioni seguenti:

1. Passare al dashboard per l'account di archiviazione.
2. Selezionare il nome del contenitore nell'elenco. Si noti che è necessario fare clic a destra della colonna Nome per selezionare il nome del contenitore. Se si fa clic sul nome viene eseguito il drill-down nel contenitore per visualizzare i relativi BLOB.
3. Selezionare **Modifica** sulla barra degli strumenti.
4. Nella finestra di dialogo **Modifica metadati contenitore** selezionare il livello desiderato di autorizzazioni per il campo **Accesso**, come illustrato nello screenshot di seguito.

	![Finestra di dialogo Modifica metadati contenitore](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-1.png)

### Impostazione delle autorizzazioni per il contenitore a livello di programmazione con .NET

Per impostare le autorizzazioni per un contenitore con la libreria client .NET, recuperare innanzitutto le autorizzazioni esistenti per il contenitore chiamando il metodo **GetPermissions**. Impostare quindi la proprietà **PublicAccess** per l'oggetto **BlobContainerPermissions** restituito dal metodo **GetPermissions**. Infine, chiamare il metodo **SetPermissions** con le autorizzazioni aggiornate.

L'esempio seguente imposta le autorizzazioni per il contenitore per l'accesso in lettura pubblico completo. Per impostare le autorizzazioni per l'accesso in lettura pubblico solo per i BLOB, impostare la proprietà **PublicAccess** su **BlobContainerPublicAccessType.Blob**. Per rimuovere tutte le autorizzazioni per gli utenti anonimi, impostare la proprietà su **BlobContainerPublicAccessType.Off**.

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## Accesso anonimo a contenitori e BLOB

Un client che accede a contenitori e BLOB in modo anonimo può usare costruttori che non richiedono credenziali. L'esempio seguente mostra alcuni modi diversi per fare riferimento a risorse del servizio BLOB in modo anonimo.

### Creare un oggetto client anonimo

È possibile creare un nuovo oggetto client del servizio per l'accesso anonimo, specificando l'endpoint del servizio BLOB per l'account. Tuttavia, è necessario conoscere anche il nome di un contenitore in tale account disponibile per l'accesso anonimo.

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### Fare riferimento a un contenitore in modo anonimo

Se si conosce l'URL per un contenitore disponibile in modo anonimo, è possibile usarlo per fare riferimento direttamente al contenitore.

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### Fare riferimento a un BLOB in modo anonimo

Se si conosce l'URL per un BLOB disponibile per l'accesso anonimo, è possibile fare riferimento direttamente al BLOB con tale URL:

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## Funzionalità disponibili per utenti anonimi

Nella tabella seguente sono riportate le operazioni che possono essere richiamate da utenti anonimi quando l'ACL di un contenitore è impostato per consentire l'accesso pubblico.

| Operazione REST | Autorizzazione con accesso in lettura pubblico completo | Autorizzazione con accesso in lettura pubblico solo per BLOB |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| List Containers | Solo proprietario | Solo proprietario |
| Create Container | Solo proprietario | Solo proprietario |
| Get Container Properties | Tutti | Solo proprietario |
| Get Container Metadata | Tutti | Solo proprietario |
| Set Container Metadata | Solo proprietario | Solo proprietario |
| Get Container ACL | Solo proprietario | Solo proprietario |
| Set Container ACL | Solo proprietario | Solo proprietario |
| Delete Container | Solo proprietario | Solo proprietario |
| List Blobs | Tutti | Solo proprietario |
| Put Blob | Solo proprietario | Solo proprietario |
| Get Blob | Tutti | Tutti |
| Get Blob Properties | Tutti | Tutti |
| Set Blob Properties | Solo proprietario | Solo proprietario |
| Get Blob Metadata | Tutti | Tutti |
| Set Blob Metadata | Solo proprietario | Solo proprietario |
| Put Block | Solo proprietario | Solo proprietario |
| Get Block List (solo blocchi con commit) | Tutti | Tutti |
| Get Block List (solo blocchi senza commit o tutti i blocchi) | Solo proprietario | Solo proprietario |
| Put Block List | Solo proprietario | Solo proprietario |
| Delete Blob | Solo proprietario | Solo proprietario |
| Copy Blob | Solo proprietario | Solo proprietario |
| Snapshot Blob | Solo proprietario | Solo proprietario |
| Lease Blob | Solo proprietario | Solo proprietario |
| Put Page | Solo proprietario | Solo proprietario |
| Get Page Ranges | Tutti | Tutti |
| Append Blob | Solo proprietario | Solo proprietario |


## Vedere anche

- [Autenticazione per i servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Firme di accesso condiviso: informazioni sul modello di firma di accesso condiviso (SAS)](storage-dotnet-shared-access-signature-part-1.md)
- [Delega dell'accesso con una firma di accesso condiviso](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=AcomDC_0107_2016-->