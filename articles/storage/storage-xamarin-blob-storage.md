<properties
	pageTitle="Come usare l'archivio BLOB da Xamarin (anteprima) | Microsoft Azure"
	description="La libreria client di Archiviazione di Azure per l'anteprima di Xamarin consente agli sviluppatori di creare app per Windows Store, iOS e Android con le proprie interfacce utente native. In questa esercitazione viene illustrato come utilizzare Xamarin per creare un'applicazione Android che utilizza l'archivio BLOB di Azure."
	services="storage"
	documentationCenter="xamarin"
	authors="micurd"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="micurd"/>

# Come usare l'archivio BLOB da Xamarin (anteprima)

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Panoramica

Xamarin consente agli sviluppatori di utilizzare una base di codici C# condivisa per creare applicazioni per Windows Store, iOS e Android con le proprie interfacce utente native. La libreria client di Archiviazione di Azure per Xamarin è una libreria di anteprima. Tenere presente che potrebbe cambiare in futuro.

In questa esercitazione viene illustrato come utilizzare l'archivio BLOB di Azure con un'applicazione per Xamarin Android. Per ulteriori informazioni su Archiviazione di Azure prima di approfondire le informazioni sul codice, vedere la sezione [Passaggi successivi](#next-steps) alla fine di questo documento.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Generare una firma di accesso condiviso

Durante lo sviluppo con la libreria client di Archiviazione di Azure per Xamarin, non è possibile autenticare l'accesso a un account di archiviazione di Azure mediante le chiavi di accesso dell'account. Ciò serve a impedire che le credenziali dell'account vengano distribuite agli utenti che potrebbero scaricare l'app. In alternativa, si consiglia l'utilizzo delle firme di accesso condiviso (SAS) che non espongono le credenziali dell'account.

In questa guida, per generare un token SAS verrà utilizzato Azure PowerShell. Verrà quindi creata un'applicazione Xamarin che utilizza la firma SAS generata.

In primo luogo, è necessario installare Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md#Install).

Aprire una sessione di Azure PowerShell ed eseguire i comandi riportati di seguito. Ricordare di sostituire `ACCOUNT_NAME` e `ACCOUNT_KEY== ` con le credenziali dell'account di archiviazione. Sostituire `CONTAINER_NAME` con il nome scelto.

    PS C:\> $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
	PS C:\> New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
	PS C:\> $now = Get-Date
	PS C:\> New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

L'URI della firma di accesso condiviso per il nuovo contenitore deve essere simile al seguente:

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

La firma di accesso condiviso creata per il contenitore rimarrà valida per il giorno successivo. La firma concede autorizzazioni complete (*vale a dire* di lettura, scrittura, eliminazione ed elenco) ai BLOB all'interno del contenitore.

Per altre informazioni sulle firme di accesso condiviso, vedere l'[esercitazione sulla firma di accesso condiviso per .NET](storage-dotnet-shared-access-signature-part-2.md).

## Creare una nuova applicazione Xamarin

Per questa esercitazione, l'applicazione Xamarin verrà creata in Visual Studio. Attenersi alla seguente procedura per creare l'applicazione:

1. Scaricare e installare [Visual Studio](https://www.visualstudio.com/).
2. Scaricare e installare [Xamarin](http://xamarin.com/platform).
3. Aprire Visual Studio e scegliere **File > Nuovo > Progetto > Android > Applicazione vuota (Android)**.
4. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e selezionare **Gestisci pacchetti NuGet**. Cercare quindi **Archiviazione di Azure** e installare **Archiviazione di Azure 4.4.0-preview**.

A questo punto si disporrà di un'applicazione che consente di fare clic su un pulsante e incrementare un contatore.

## Utilizzare la firma di accesso condiviso per eseguire le operazioni del contenitore

Successivamente, aggiungere codice per eseguire una serie di operazioni del contenitore utilizzando l'URI SAS generato.

Innanzitutto, aggiungere le seguenti istruzioni **using**:

	using System.IO;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.Storage.Blob;


In seguito aggiungere una riga per il token SAS. Sostituire la stringa `"SAS_URI"` con l'URI SAS generato in Azure PowerShell. Aggiungere quindi una riga per una chiamata al metodo `UseContainerSAS` che verrà creato di seguito. Tenere presente che la parola chiave **async** è stata aggiunta prima del delegato.


	public class MainActivity : Activity
	{
    	int count = 1;
    	string sas = "SAS_URI";
    	protected override void OnCreate(Bundle bundle)
    	{
        	base.OnCreate(bundle);

        	// Set our view from the "main" layout resource
        	SetContentView(Resource.Layout.Main);

        	// Get our button from the layout resource, and attach an event to it
        	Button button = FindViewById<Button>(Resource.Id.MyButton);

        	button.Click += async delegate	{
             	button.Text = string.Format("{0} clicks!", count++);
             	await UseContainerSAS(sas);
         	};
     }

Aggiungere un nuovo metodo, `UseContainerSAS`, sotto il metodo `OnCreate`.

	static async Task UseContainerSAS(string sas)
	{
    	//Try performing container operations with the SAS provided.

    	//Return a reference to the container using the SAS URI.
    	CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));
    	string date = DateTime.Now.ToString();
    	try
    	{
        	//Write operation: write a new blob to the container.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_" + date + ".txt");

        	string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        	MemoryStream msWrite = new
        	MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        	msWrite.Position = 0;
        	using (msWrite)
         	{
             	await blob.UploadFromStreamAsync(msWrite);
         	}
         	Console.WriteLine("Write operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Write operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
     	try
     	{
        	//Read operation: Get a reference to one of the blobs in the container and read it.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
        	string data = await blob.DownloadTextAsync();

        	Console.WriteLine("Read operation succeeded for SAS " + sas);
        	Console.WriteLine("Blob contents: " + data);
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Additional error information: " + e.Message);
       		Console.WriteLine("Read operation failed for SAS " + sas);
        	Console.WriteLine();
     	}
     	Console.WriteLine();
     	try
     	{
        	//Delete operation: Delete a blob in the container.
         	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
         	await blob.DeleteAsync();

         	Console.WriteLine("Delete operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Delete operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
	}

## Eseguire l'applicazione

È ora possibile eseguire questa applicazione in un emulatore o in un dispositivo Android.

Sebbene questa introduzione sia incentrata su Android, è possibile utilizzare il metodo `UseContainerSAS` anche nelle applicazioni iOS e di Windows Store. Xamarin consente inoltre agli sviluppatori di creare app per Windows Phone. Tuttavia, la libreria ancora non le supporta.

## Passaggi successivi

In questa esercitazione, è stato illustrato come utilizzare l'archivio BLOB di Azure con un'applicazione Xamarin. Come esercizio ulteriore, è possibile applicare un modello simile per generare un token SAS per una tabella o coda di Azure.

È possibile ottenere ulteriori informazioni su BLOB, tabelle e code visitando i collegamenti seguenti:

[Introduzione ad Archiviazione di Microsoft Azure](storage-introduction.md)
[Come usare l'archiviazione BLOB da .NET](storage-dotnet-how-to-use-blobs.md)
[Come usare l'archiviazione tabelle da .NET](storage-dotnet-how-to-use-tables.md)
[Come usare l'archiviazione code da .NET](storage-dotnet-how-to-use-queues.md)
[Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0114_2016-->