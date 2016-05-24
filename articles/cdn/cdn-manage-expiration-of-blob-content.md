<properties
 pageTitle="Come gestire la scadenza del contenuto dei BLOB nella rete per la distribuzione di contenuti (rete CDN) di Azure"
 description="Informazioni sulle opzioni per il controllo della durata per i BLOB nel caching della rete CDN di Azure."
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="05/11/2016"
 ms.author="casoper"/>


#Come gestire la scadenza del contenuto dei BLOB nella rete per la distribuzione di contenuti (rete CDN) di Azure  

I BLOB per cui è più utile la memorizzazione nella cache della rete CDN di Azure sono quelli cui si accede di frequente per tutta la loro durata (TTL). Un BLOB resta nella cache per tutto il TTL e viene quindi aggiornato dal servizio BLOB allo scadere di tale periodo. Il processo viene quindi ripetuto.

Per controllare il TTL, sono disponibili due opzioni.

1.	Evitare di impostare i valori della cache in modo da usare il TTL predefinito di 7 giorni.
2.	Impostare in modo esplicito la proprietà *x-ms-blob-cache-control* su una richiesta **Put Blob**, **Put Block List** o **Set Blob Properties** oppure usare la libreria gestita di Azure per impostare la proprietà [BlobProperties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx). Impostando questa proprietà, si imposta il valore dell'intestazione *Cache-Control* per il BLOB. Il valore dell'intestazione o della proprietà deve specificare il valore appropriato in secondi. Ad esempio, per impostare il periodo massimo di memorizzazione nella cache su un anno, è possibile specificare l'intestazione della richiesta come `x-ms-blob-cache-control: public, max-age=31556926`. Per informazioni dettagliate sull'impostazione delle intestazioni di memorizzazione nella cache, vedere la [specifica HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).  

Qualsiasi contenuto che si vuole memorizzare nella cache tramite la rete CDN deve essere archiviato nell'account di archiviazione di Azure come BLOB accessibile pubblicamente. Per altre informazioni dettagliate sul servizio BLOB di Azure, vedere **Concetti relativi al servizio BLOB**.

Sono disponibili alcuni metodi diversi per usare il contenuto nel servizio BLOB:

-	Tramite l'API gestita disponibile nelle **informazioni di riferimento sulla libreria gestita di Azure**.
-	Tramite uno strumento di gestione dell'archiviazione di terze parti.
-	Tramite l'API REST dei servizi di archiviazione di Azure.  

L'esempio di codice seguente rappresenta un'applicazione console che usa la libreria gestita di Azure per creare un contenitore, impostarne le autorizzazioni per l'accesso pubblico e creare un BLOB al suo interno. Il codice specifica anche in modo esplicito un intervallo di aggiornamento desiderato attraverso l'impostazione dell'intestazione Cache-Control nel BLOB.

Supponendo di aver abilitato la rete CDN come indicato sopra, il BLOB creato verrà memorizzato nella cache dalla rete CDN. Assicurarsi di specificare le credenziali dell'account usando l'account di archiviazione e la chiave di accesso personali:

	using System;
	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.StorageClient;

	namespace BlobsInCDN
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            //Specify storage credentials.
	            StorageCredentialsAccountAndKey credentials = new StorageCredentialsAccountAndKey("storagesample",
	                "m4AHAkXjfhlt2rE2BN/hcUR4U2lkGdCmj2/1ISutZKl+OqlrZN98Mhzq/U2AHYJT992tLmrkFW+mQgw9loIVCg==");

	            //Create a reference to your storage account, passing in your credentials.
	            CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);

	            //Create a new client object, which will provide access to Blob service resources.
	            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	            //Create a new container.
	            CloudBlobContainer container = blobClient.GetContainerReference("cdncontent");
	            container.CreateIfNotExist();

	            //Specify that the container is publicly accessible.
	            BlobContainerPermissions containerAccess = new BlobContainerPermissions();
	            containerAccess.PublicAccess = BlobContainerPublicAccessType.Container;
	            container.SetPermissions(containerAccess);

	            //Create a new blob and write some text to it.
	            CloudBlob blob = blobClient.GetBlobReference("cdncontent/testblob.txt");
	            blob.UploadText("This is a test blob.");

	            //Set the Cache-Control header on the blob to specify your desired refresh interval.
	            blob.SetCacheControl("public, max-age=31536000");
	        }
	    }

	    public static class BlobExtensions
	    {
	        //A convenience method to set the Cache-Control header.
	        public static void SetCacheControl(this CloudBlob blob, string value)
	        {
	            blob.Properties.CacheControl = value;
	            blob.SetProperties();
	        }
	    }
	}

Verificare che il BLOB sia disponibile tramite l'URL specifico della rete CDN. Per il BLOB mostrato sopra, l'URL sarà simile al seguente:

	http://az1234.vo.msecnd.net/cdncontent/testblob.txt  

È possibile scegliere di usare uno strumento come **wget** o Fiddler per esaminare i dettagli della richiesta e della risposta.

##Vedere anche

[Come gestire la scadenza del contenuto del servizio cloud nella rete per la distribuzione di contenuti (rete CDN) di Azure](./cdn-manage-expiration-of-cloud-service-content.md)

<!---HONumber=AcomDC_0511_2016-->