<properties
   pageTitle="Crittografare e decrittografare i BLOB in Archiviazione di Microsoft Azure tramite l'insieme di credenziali chiave di Azure"
   description="Questa esercitazione illustra come crittografare e decrittografare un BLOB mediante la crittografia lato client per Archiviazione di Microsoft Azure con l'insieme di credenziali chiave di Azure."
   services="storage"
   documentationCenter=""
   authors="adhurwit"
   manager=""
   editor=""/>

<tags
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="06/17/2015"
   ms.author="adhurwit"/>

# Crittografare e decrittografare i BLOB in Archiviazione di Microsoft Azure tramite l'insieme di credenziali chiave di Azure

## Introduzione
 
Questa esercitazione illustra come usare la crittografia di archiviazione lato client, attualmente in anteprima, con l'insieme di credenziali chiave di Azure, anch'esso disponibile attualmente in anteprima. Illustra come crittografare e decrittografare un BLOB in un'applicazione console mediante queste tecnologie.

**Tempo previsto per il completamento:** 20 minuti

Per informazioni generali sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali chiave di Azure?](key-vault/key-vault-whatis.md)

Per informazioni generali sulla crittografia lato client per Archiviazione di Azure, vedere l'articolo relativo alla [crittografia lato client per Archiviazione di Microsoft Azure (anteprima)](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview.aspx).


## Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

- Un account di archiviazione Azure
- Visual Studio 2013 o versioni successive
- Azure PowerShell 


## Panoramica del processo di crittografia lato client

Per una panoramica della crittografia lato client per Archiviazione di Microsoft Azure, vedere l'articolo all'indirizzo [http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx "Introduzione alla crittografia lato client per Archiviazione di Microsoft Azure").

Questo è il processo descritto nel post di blog:

1. Azure Storage Client SDK genererà una chiave di crittografia del contenuto (CEK, Content Encryption Key) che funziona come chiave simmetrica monouso.
2. I dati utente vengono crittografati con questa chiave CEK.
3. Viene quindi eseguito il wrapping della chiave CEK mediante la chiave di crittografia della chiave (KEK, Key Encryption Key). La chiave KEK è identificata con un identificatore di chiave e può essere costituita da una coppia di chiavi asimmetriche o da una chiave simmetrica. Può essere gestita localmente o archiviata in insiemi di credenziali chiave di Azure. Il client Archiviazione non ha mai accesso alla chiave KEK. Richiama solo l'algoritmo di wrapping della chiave fornito dall'insieme di credenziali chiave. Gli utenti possono scegliere di usare provider personalizzati per il wrapping o la rimozione del wrapping delle chiavi, se lo desiderano.
4. I dati crittografati vengono quindi caricati nel servizio Archiviazione di Azure.


## Configurare l'insieme di credenziali chiave di Azure
Per procedere con questa esercitazione, è necessario effettuare le operazioni seguenti, descritte nell'esercitazione [Introduzione all'insieme di credenziali chiave di Azure](key-vault/key-vault-get-started.md).

- Creare un insieme di credenziali chiave
- Aggiungere una chiave o un segreto all'insieme di credenziali chiave
- Registrare un'applicazione con Azure Active Directory
- Autorizzare l'applicazione a usare la chiave o il segreto

Prendere nota dei valori di ClientID e ClientSecret generati durante la registrazione di un'applicazione con Azure Active Directory.

Creare una chiave per entrambi nell'insieme di credenziali chiave. Per il resto dell'esercitazione si presupporrà che siano stati usati i nomi seguenti: ContosoKeyVault e TestRSAKey1.


## Creare un'applicazione console con pacchetti e AppSettings

In Visual Studio creare una nuova applicazione console.

Aggiungere i pacchetti nuget necessari nella console di Gestione pacchetti:

	// Note that this is the preview version for Azure Storage
	Install-Package WindowsAzure.Storage -Pre

	// This is the latest stable release for ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	// These are currently only available in preview
	Install-Package Microsoft.Azure.KeyVault -Pre
	Install-Package Microsoft.Azure.KeyVault.Extensions -Pre


Aggiungere AppSettings al file App.Config.

	<appSettings>
	    <add key="accountName" value="myaccount"/>
	    <add key="accountKey" value="theaccountkey"/>
	    <add key="clientId" value="theclientid"/>
	    <add key="clientSecret" value="theclientsecret"/>
    	<add key="container" value="stuff"/>
	</appSettings>

Aggiungere le istruzioni using seguenti e assicurarsi di aggiungere al progetto un riferimento a System.Configuration.

	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Configuration;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.Azure.KeyVault;
	using System.Threading;		
	using System.IO;


## Aggiungere il metodo per ottenere il token per l'applicazione console

Il metodo seguente viene usato dalle classi dell'insieme di credenziali chiave che devono essere autenticate per l'accesso all'insieme di credenziali chiave.

	private async static Task<string> GetToken(string authority, string resource, string scope)
	{
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(
	        ConfigurationManager.AppSettings["clientId"], 
	        ConfigurationManager.AppSettings["clientSecret"]);
		AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	
	    if (result == null)
	        throw new InvalidOperationException("Failed to obtain the JWT token");
	
	    return result.AccessToken;
	}

## Accedere alla risorsa di archiviazione e all'insieme di credenziali chiave nel programma 

Nella funzione Main aggiungere il codice seguente:

	// This is standard code to interact with Blob Storage
	StorageCredentials creds = new StorageCredentials(
		ConfigurationManager.AppSettings["accountName"],
       	ConfigurationManager.AppSettings["accountKey"]);
	CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
	CloudBlobClient client = account.CreateCloudBlobClient();
	CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
	contain.CreateIfNotExists();

	// The Resolver object is used to interact with Key Vault for Azure Storage
	// This is where the GetToken method from above is used
	KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE]Modelli a oggetti dell'insieme di credenziali chiave
>
>È importante capire che esistono in realtà due modelli a oggetti dell'insieme di credenziali chiave da tenere presenti: uno è basato sull'API REST (spazio dei nomi KeyVault) e l'altro è un'estensione per la crittografia lato client.

> Il client insieme di credenziali chiave interagisce con l'API REST e interpreta correttamente le chiavi Web e i segreti JSON per i due tipi di oggetti contenuti nell'insieme di credenziali chiave.

> Le estensioni dell'insieme di credenziali chiave sono classi che sembrano create appositamente per la crittografia lato client in Archiviazione di Azure. Contengono un'interfaccia per le chiavi, ovvero IKey, e classi basate sul concetto di resolver di chiavi. Esistono due implementazioni di IKey che è necessario conoscere: RSAKey e SymmetricKey. Coincidono con gli oggetti contenuti in un insieme di credenziali chiave, ma a questo punto sono classi indipendenti (pertanto la chiave e il segreto recuperati dal client insieme di credenziali chiave non implementano IKey).


## Crittografare un BLOB ed eseguire il caricamento
Aggiungere il codice seguente per crittografare un BLOB e caricarlo nell'account di Archiviazione di Azure. Il metodo ResolveKeyAsync che viene usato restituisce un'interfaccia IKey.

	
	// Retrieve the key that you created previously
	// The IKey that is returned here is an RsaKey
	// Remember that we used the names contosokeyvault and testrsakey1
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


	// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy. 
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

	// Reference a block blob
	CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

	// Upload using the UploadFromStream method
	using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
		blob.UploadFromStream(stream, stream.Length, null, options, null);


Di seguito è riportato uno screenshot del portale di gestione di Azure corrente relativo a un BLOB che è stato crittografato mediante la crittografia lato client con una chiave archiviata nell'insieme di credenziali chiave. La proprietà KeyId è l'URI per la chiave nell'insieme di credenziali chiave che agisce come chiave di crittografia della chiave (KEK). La proprietà EncryptedKey contiene la versione crittografata della chiave di crittografia del contenuto (CEK).

![Screenshot che mostra i metadati BLOB con i metadati di crittografia][1]

> [AZURE.NOTE]Se si osserva il costruttore BlobEncryptionPolicy, è possibile notare che è in grado di accettare una chiave e/o un resolver. Tenere presente che al momento non è possibile usare un resolver per la crittografia perché non supporta attualmente una chiave predefinita.



## Decrittografare un BLOB ed eseguire il download
La decrittografia è l'operazione per cui si rivelano utili le classi Resolver. L'ID della chiave usata per la crittografia è associato al BLOB nei relativi metadati, pertanto non è necessario recuperare la chiave e ricordare l'associazione tra la chiave e il BLOB. È sufficiente verificare che la chiave rimanga nell'insieme di credenziali chiave.

La chiave privata di una chiave RSA resta nell'insieme di credenziali chiave. Perché venga eseguita la decrittografia, la chiave crittografata nei metadati del BLOB che contengono la chiave CEK viene pertanto inviata all'insieme di credenziali chiave per la decrittografia.

Aggiungere quanto segue per decrittografare il BLOB appena caricato.

	// In this case we will not pass a key and only pass the resolver because 
	// 	this policy will only be used for downloading / decrypting
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
	    blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE]Esistono un paio di altri tipi di resolver per semplificare la gestione delle chiavi, tra cui: AggregateKeyResolver e CachingKeyResolver.


## Uso dei segreti dell'insieme di credenziali chiave
Per usare un segreto con la crittografia lato client, usare una classe SymmetricKey, dal momento che un segreto è in pratica una chiave simmetrica. Come indicato in precedenza però, un segreto nell'insieme di credenziali chiave non corrisponde esattamente a una classe SymmetricKey. Tenere presenti le informazioni seguenti:


- La chiave in una classe SymmetricKey deve avere una lunghezza fissa: 128, 192, 256, 384 o 512 bit.
- La chiave in una classe SymmetricKey deve essere con codifica Base64.
- Un segreto dell'insieme di credenziali chiave che verrà usato come SymmetricKey deve avere come tipo di contenuto "application/octet-stream" nell'insieme di credenziali chiave.

Di seguito è riportato un esempio di PowerShell per la creazione di un segreto nell'insieme di credenziali chiave che può essere usato come SymmetricKey:

	// Here we are making a 128-bit key so we have 16 characters. 
	// 	The characters are in the ASCII range of UTF8 so they are
	//	each 1 byte. 16 x 8 = 128
	$key = "qwertyuiopasdfgh"
	$b = [System.Text.Encoding]::UTF8.GetBytes($key)
	$enc = [System.Convert]::ToBase64String($b)
	$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

	// substitute the VaultName and Name in this command
	$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

Nell'applicazione console è possibile usare la stessa chiamata descritta precedentemente per recuperare questo segreto come SymmetricKey.

	SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    	"https://contosokeyvault.vault.azure.net/secrets/TestSecret2/", 
        CancellationToken.None).GetAwaiter().GetResult();

È tutto. pronta per essere usata.

## Passaggi successivi

Per altre informazioni sull'uso di Archiviazione di Microsoft Azure con C#, vedere [Libreria client di archiviazione di Microsoft Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Per altre informazioni sull'API REST per i BLOB, vedere [API REST del servizio Blob](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Per le informazioni più recenti su Archiviazione di Microsoft Azure, visitare il [blog del team di Archiviazione di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/).


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png

<!---HONumber=July15_HO1-->