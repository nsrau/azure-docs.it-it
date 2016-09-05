<properties
 pageTitle="Gestire la scadenza del contenuto di BLOB del servizio di archiviazione di Azure nella rete CDN di Azure | Microsoft Azure"
 description="Informazioni sulle opzioni per il controllo della durata per i BLOB nel caching della rete CDN di Azure."
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="08/24/2016"
 ms.author="casoper"/>  


# Gestire la scadenza del contenuto di BLOB del servizio di archiviazione di Azure nella rete CDN di Azure

Il [servizio BLOB](../storage/storage-introduction.md#blob-storage) di [Archiviazione di Azure](../storage/storage-introduction.md) è una delle varie origini basate su Azure integrate nella rete CDN di Azure. Qualsiasi contenuto BLOB accessibile pubblicamente può essere memorizzato nella cache della rete CDN di Azure fino allo scadere della relativa durata (TTL). La durata (TTL) è determinata dall'[intestazione *Cache-Control*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) nella risposta HTTP di Archiviazione di Azure.

>[AZURE.TIP] È possibile scegliere di non impostare alcuna durata (TTL) per un BLOB. In tal caso, la rete CDN di Azure applica automaticamente una durata (TTL) predefinita di sette giorni.
>
>Per altre informazioni sull'uso della rete CDN di Azure per velocizzare l'accesso a BLOB e altri file, vedere la [panoramica della rete CDN di Azure](./cdn-overview.md).
>
>Per informazioni dettagliate sul servizio BLOB del servizio di Archiviazione di Azure, vedere [Concetti relativi al servizio BLOB](https://msdn.microsoft.com/library/dd179376.aspx).

Questa esercitazione illustra vari modi in cui è possibile impostare la durata (TTL) per un BLOB in Archiviazione di Azure.

## Azure PowerShell

[Azure PowerShell](../powershell-install-configure.md) è uno dei modi più rapidi ed efficaci per amministrare i servizi di Azure. Usare il cmdlet `Get-AzureStorageBlob` per ottenere un riferimento al BLOB, quindi impostare la proprietà `.ICloudBlob.Properties.CacheControl`.

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

>[AZURE.TIP] È anche possibile usare PowerShell per [gestire i profili della rete CDN e gli endpoint](./cdn-manage-powershell.md).

## Libreria client di archiviazione di Azure per .NET

Per impostare la durata (TTL) di un BLOB con .NET, usare la [libreria client di archiviazione di Azure per .NET](../storage/storage-dotnet-how-to-use-blobs.md) per impostare la proprietà [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx).

```csharp
class Program
{
	const string connectionString = "<storage connection string>";
	static void Main()
	{
		// Retrieve storage account information from connection string
		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
		
		// Create a blob client for interacting with the blob service.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		
		// Create a reference to the container
		CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

		// Create a reference to the blob
		CloudBlob blob = container.GetBlobReference("<blob name>");

		// Set the CacheControl property to expire in 1 hour (3600 seconds)
		blob.Properties.CacheControl = "public, max-age=3600";

		// Update the blob's properties in the cloud
		blob.SetProperties();
	}
}
```

>[AZURE.TIP] Molti altri esempi di codice .NET sono disponibili in [Azure Blob Storage Samples for .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) (Esempi di archivio BLOB di Azure per .NET).

## Altri metodi

- [Interfaccia della riga di comando di Azure](../xplat-cli-install.md)

	Quando si carica il BLOB, impostare la proprietà *cacheControl* usando l'opzione `-p`. Questo esempio imposta la durata (TTL) su un'ora, ovvero 3.600 secondi.

	```text
	azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
	```

- [API REST dei servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

	Impostare in modo esplicito la proprietà *x-ms-blob-cache-control* su una richiesta [Put Blob](https://msdn.microsoft.com/it-IT/library/azure/dd179451.aspx), [Put Block List](https://msdn.microsoft.com/it-IT/library/azure/dd179467.aspx) o [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx).

- Strumenti di gestione dell'archiviazione di terze parti

	Alcuni strumenti di gestione di Archiviazione di Azure di terze parti consentono di impostare la proprietà *CacheControl* per i BLOB.

## Test dell'intestazione *Cache-Control*

È possibile verificare facilmente la durata (TTL) dei BLOB. Usare gli [strumenti di sviluppo](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) del browser per verificare che il BLOB includa l'intestazione della risposta *Cache-Control*. È anche possibile usare uno strumento come **wget**, [Postman](https://www.getpostman.com/) o [Fiddler](http://www.telerik.com/fiddler) per esaminare le intestazioni della risposta.

## Passaggi successivi

- [Approfondire l'intestazione *Cache-Control*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
- [Come gestire la scadenza del contenuto del servizio cloud nella rete per la distribuzione di contenuti (rete CDN) di Azure](./cdn-manage-expiration-of-cloud-service-content.md)

<!---HONumber=AcomDC_0824_2016-->