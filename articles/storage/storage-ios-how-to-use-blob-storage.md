<properties
    pageTitle="Come usare l'archivio BLOB da iOS | Microsoft Azure"
    description="Informazioni su come usare il servizio di archiviazione BLOB di Microsoft Azure per caricare, scaricare, elencare ed eliminare contenuti BLOB. Gli esempi sono scritti in Objective-C."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Objective-C"
    ms.topic="article"
    ms.date="01/05/2016"
    ms.author="micurd"/>

# Come usare l'archivio BLOB da iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Panoramica

Questa guida illustra i diversi scenari comuni di uso del servizio di archiviazione BLOB di Microsoft Azure. Gli esempi sono scritti in Objective-C e usano la [libreria iOS di Archiviazione di Azure](https://github.com/Azure/azure-storage-ios). Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB. Per ulteriori informazioni sui BLOB, vedere la sezione [Passaggi successivi](#next-steps). È possibile scaricare l'[app di esempio](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) per visualizzare rapidamente l'uso di Archiviazione di Azure in un'applicazione iOS.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Compilare il file del framework
Per usare la libreria iOS di Archiviazione di Azure, innanzitutto sarà necessario compilare il file del framework.

1. Per prima cosa, scaricare o clonare il [repository azure-storage-ios](https://github.com/azure/azure-storage-ios).

2. Andare ad *azure-storage-ios* -> *Lib* -> *Azure Storage Client Library* e aprire `Azure Storage Client Library.xcodeproj` in Xcode.

3. In alto a sinistra in Xcode cambiare lo schema attivo da "Azure Storage Client Library" a "Framework".

4. Compilare il progetto (⌘+B). Verrà creato un file `Azure Storage Client Library.framework` sulla scrivania.

## Importare la libreria iOS di Archiviazione di Azure nell'applicazione

Per importare la libreria iOS di Archiviazione di Azure nell'applicazione, eseguire le seguenti operazioni:

1. Creare un nuovo progetto o aprire il progetto esistente in Xcode.

2. Fare clic sul progetto nel riquadro di spostamento a sinistra e fare clic sulla scheda *General* nella parte superiore dell'editor del progetto.

3. Nella sezione *Linked Frameworks and Libraries* fare clic sul pulsante Add (+).

4. Fare clic su *Add Other*. Passare al file `Azure Storage Client Library.framework` appena creato e aggiungerlo.

5. Nella sezione *Linked Frameworks and Libraries* fare nuovamente clic sul pulsante Add (+).

6. Nell'elenco di librerie già fornito cercare `libxml2.2.dylib` e aggiungerla al progetto.

##Istruzione import
Sarà necessario includere l'istruzione import seguente nel file in cui si vuole richiamare l'API di archiviazione di Azure.

    // Include the following import statement to use blob APIs.
    #import <Azure Storage Client Library/Azure_Storage_Client_Library.h>

## Configurazione dell'applicazione per l'accesso all'archiviazione BLOB

Per autenticare l'applicazione per accedere ai servizi di archiviazione, è possibile procedere in due modi:

- Chiave condivisa: usare la Chiave condivisa solo a scopo di test
- Firma di accesso condiviso: usare la firma di accesso condiviso per le applicazioni per la produzione

###Chiave condivisa
L'autenticazione con la chiave condivisa implica che l'applicazione userà il nome account e la chiave dell'account per accedere ai servizi di archiviazione. Per mostrare rapidamente come usare l'archivio BLOB da iOS, in questa guida introduttiva verrà usata l'autenticazione con la chiave condivisa.

> [AZURE.WARNING (Only use Shared Key authentication for testing purposes!) ]Il nome account e la chiave dell'account, che concedono l'accesso completo in lettura/scrittura all'account di archiviazione associato, verranno distribuiti a ogni persona che scarica l'app. Questa procedura **non** è consigliabile perché si rischia che la chiave venga compromessa da client non attendibili.

Quando si usa l'autenticazione con la chiave condivisa, si creerà una stringa di connessione. La stringa di connessione è costituita da:

- **DefaultEndpointsProtocol**: è possibile scegliere HTTP o HTTPS. Tuttavia, è vivamente consigliato l'uso di HTTPS.
- **Nome account**: nome dell'account di archiviazione
- **Chiave account**: se si utilizza il [portale di Azure](portal.azure.com), passare all’account di archiviazione e fare clic sull’icona **Chiavi** per trovare queste informazioni. Se si utilizza il [portale di Azure classico](manage.windowsazure.com), passare all'account di archiviazione nel portale e fare clic su **Gestisci chiavi di accesso**. 

Ecco come appare nell'applicazione:

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=account_name;AccountKey=account_key"];

###Firme di accesso condiviso
Per un'applicazione iOS, il metodo consigliato per autenticare una richiesta da parte di un client in base all'archivio BLOB è di usare una firma di accesso condiviso. La firma di accesso condiviso consente di concedere a un client l'accesso a una risorsa per un periodo di tempo specificato, con un set di autorizzazioni specificato. Il proprietario dell'account di archiviazione dovrà generare una firma di accesso condiviso che verrà utilizzata dai client iOS. Per generare la firma di accesso condiviso da distribuire ai client, è possibile che si voglia scrivere un apposito servizio separato. A scopo di test, è possibile usare anche l'interfaccia della riga di comando di Azure per generare una firma di accesso condiviso. Si noti che le credenziali della chiave condivisa vengono usate per generare una firma di accesso condiviso, ma i client possono quindi utilizzare la firma di accesso condiviso con le informazioni di autenticazione incapsulate nell'URL della firma di accesso condiviso. Quando si crea la firma di accesso condiviso, è possibile specificare l'intervallo di tempo in cui la firma di accesso condiviso è valida e le autorizzazioni che la firma di accesso condiviso concede al client. Per un contenitore BLOB, ad esempio, la firma di accesso condiviso può concedere autorizzazioni di lettura, scrittura o eliminazione per un BLOB del contenitore e le autorizzazioni per elencare i BLOB nel contenitore.

L'esempio seguente mostra come usare l'interfaccia della riga di comando di Azure per generare un Token di firma di accesso condiviso che concede le autorizzazioni di lettura e scrittura per il contenitore, *sascontainer*, fino alle 00.00 (UTC) del 5 settembre 2015.

1. Seguire innanzitutto questa [guida](../xplat-cli/#how-to-install-the-azure-cli) per informazioni su come installare l'interfaccia della riga di comando di Azure e connettersi alla sottoscrizione di Azure.

2. Digitare quindi il comando seguente nell'interfaccia della riga di comando di Azure per ottenere la stringa di connessione per l'account:

		azure storage account connectionstring show youraccountname

3. Creare una variabile di ambiente con la stringa di connessione appena generata:

		export AZURE_STORAGE_CONNECTION_STRING=”your connection string”

4. Generare l'URL della firma di accesso condiviso:

		azure storage container sas create --container sascontainer --permissions rw --expiry 2015-09-05T00:00:00

5. L'URL della firma di accesso condiviso dovrebbe essere simile a quello riportato di seguito:

		https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

6. Nell'applicazione iOS ora è possibile ottenere un riferimento al contenitore usando l'URL della firma di accesso condiviso nel modo seguente:

		// Get a reference to a container in your Storage account
    	AZSCloudBlobContainer *blobContainer = [[AZSCloudBlobContainer alloc] initWithUrl:[NSURL URLWithString:@" your SAS URL"]];

Come si può osservare, quando si usa un token di firma di accesso condiviso, non si espongono il nome account e la chiave dell'account nell'applicazione iOS. Vedere l'[esercitazione sulla firma di accesso condiviso](../storage-dotnet-shared-access-signature-part-1) per altre informazioni sull'argomento.

##Operazioni asincrone
> [AZURE.NOTE]Tutti i metodi che eseguono una richiesta al servizio sono operazioni asincrone. Negli esempi di codice si noterà che questi metodi hanno un gestore completamento. Il codice nel gestore completamento verrà eseguito **dopo** il completamento della richiesta. Il codice dopo il gestore completamento verrà eseguito **mentre** la richiesta è in corso.

## Creare un contenitore
Ogni BLOB nell'archivio di Azure deve risiedere in un contenitore. L'esempio seguente mostra come creare un contenitore, denominato *newcontainer*, nell'account di archiviazione se non esiste già. Quando si sceglie un nome per il contenitore, tenere presenti le regole indicate in precedenza.

     -(void)createContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

È possibile verificarne il funzionamento controllando il [portale di Azure](portal.azure.com) o qualsiasi [strumento di esplorazione di archiviazione](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) e accertandosi che *newcontainer* sia nell'elenco di contenitori dell'account di archiviazione.

## Impostare le autorizzazioni del contenitore
Per impostazione predefinita, le autorizzazioni di un contenitore vengono configurate per l'accesso **Privato**. I contenitori, tuttavia, offrono alcune opzioni diverse per l'accesso al contenitore:

- **Privato**: dati BLOB e contenitore possono essere letti solo dal proprietario dell'account.

- **BLOB**: i dati BLOB all'interno di questo contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client non possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima.

- **Contenitore**: dati BLOB e contenitore possono essere letti tramite richiesta anonima. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

L'esempio seguente mostra come creare un contenitore con le autorizzazioni di accesso **Contenitore** che consentiranno l'accesso pubblico di sola lettura per tutti gli utenti in Internet:

     -(void)createContainerWithPublicAccess{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## Caricare un BLOB in un contenitore
Come accennato nella sezione [Concetti del servizio BLOB](#blob-service-concepts), l'archiviazione BLOB offre tre diversi tipi di BLOB: BLOB in blocchi, BLOB di accodamento e BLOB di pagine. Per il momento, la libreria iOS di Archiviazione di Azure supporta solo i BLOB in blocchi. Nella maggior parte dei casi è consigliabile utilizzare il tipo di BLOB in blocchi.

L'esempio seguente mostra come caricare un BLOB in blocchi da NSString. Se in questo contenitore esiste già un BLOB con lo stesso nome, i contenuti del BLOB verranno sovrascritti.

     -(void)uploadBlobToContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
         	 else{
				 // Create a local blob object
             	 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

             	 // Upload blob to Storage
             	 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                 	if (error){
						NSLog(@"Error in creating blob.");
                 	}             
             	 }];
			 }
         }];
     }

È possibile verificarne il funzionamento controllando il [portale di Azure](portal.azure.com) o qualsiasi [strumento di esplorazione di archiviazione](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) e accertandosi che il contenitore *containerpublic* contenga il BLOB *sampleblob*. Poiché in questo esempio è stato usato un contenitore pubblico, per verificarne il funzionamento è possibile andare all'URI dei BLOB:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Oltre a caricare un BLOB in blocchi da NSString, esistono metodi simili per NSData, NSInputStream o un file locale.

## Elencare i BLOB in un contenitore
L'esempio seguente mostra come elencare tutti i BLOB in un contenitore. Quando si esegue questa operazione, tenere presenti i parametri seguenti:

- **continuationToken**: il token di continuazione indica dove deve iniziare l'operazione di elenco. Se non viene specificato alcun token, i BLOB verranno elencati dall'inizio. È possibile elencare qualsiasi numero di BLOB, da zero a un massimo impostato. Anche se questo metodo restituisce zero risultati, se `results.continuationToken` non è nil, il servizio potrebbe includere altri BLOB che non sono stati elencati.
- **prefix**: è possibile specificare il prefisso da usare per l'elenco di BLOB. Verranno elencati solo i BLOB che iniziano con questo prefisso.
- **useFlatBlobListing**: come accennato nella sezione [Assegnazione di nome e riferimento a contenitori e BLOB](#naming-and-referencing-containers-and-blobs), anche se il servizio BLOB è uno schema di Archiviazione semplice, è possibile creare una gerarchia virtuale assegnando ai BLOB un nome con le informazioni sul percorso. Gli elenchi non semplici, tuttavia, non sono attualmente supportati, ma lo saranno a breve. Per ora, questo valore dovrebbe essere `YES`.
- **blobListingDetails**: è possibile specificare quali elementi includere quando si elencano i BLOB
	- `AZSBlobListingDetailsNone`: elenca solo i BLOB di cui è stato eseguito il commit e non restituisce i metadati dei BLOB.
	- `AZSBlobListingDetailsSnapshots`: elenca i BLOB di cui è stato eseguito il commit e gli snapshot dei BLOB.
	- `AZSBlobListingDetailsMetadata`: recupera i metadati per ogni BLOB restituito nell'elenco.
	- `AZSBlobListingDetailsUncommittedBlobs`: elenca i BLOB sottoposti a commit e non sottoposti a commit.
	- `AZSBlobListingDetailsCopy`: include le proprietà di copia nell'elenco.
	- `AZSBlobListingDetailsAll`: elenca tutti i BLOB sottoposti a commit, i BLOB non sottoposti e commit e gli snapshot disponibili, restituisce tutti i metadati e copia lo stato di tali BLOB.
- **maxResults**: numero massimo di risultati da restituire per questa operazione. Per non impostare un limite, usare -1.
- **completionHandler**: blocco di codice da eseguire con i risultati dell'operazione di elenco.

In questo esempio viene usato un metodo helper per chiamare in modo ricorsivo il metodo list blobs ogni volta che viene restituito un token di continuazione.

    -(void)listBlobsInContainer{
      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      //List all blobs in container
      [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
         if (error != nil){
             NSLog(@"Error in creating container.");
         }
      }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
      [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
         if (error)
         {
             completionHandler(error);
         }
         else
         {
             for (int i = 0; i < results.blobs.count; i++) {
                 NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
             }
             if (results.continuationToken)
             {
                 [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
             }
             else
             {
                 completionHandler(nil);
             }
         }
      }];
    }

## Scaricare un BLOB

L'esempio seguente mostra come scaricare un BLOB in un oggetto NSString.

     -(void)downloadBlobToString{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob    
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
			else{
            	NSLog(@"%@",text);
			}
        }];
    }

## Eliminare un BLOB

L'esempio seguente mostra come eliminare un BLOB.

     -(void)deleteBlob{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
          if (error) {
            NSLog(@"Error in deleting blob.");
          }
        }];
     }

## Eliminare un contenitore BLOB

L'esempio seguente mostra come eliminare un contenitore.

     -(void)deleteContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Delete container
        [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
            if(error){
                NSLog(@"Error in deleting container");
            }
        }];
    }

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

- [Libreria iOS di Archiviazione di Azure]
- [API REST di Archiviazione di Azure]
- [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy)
- [Blog del team di Archiviazione di Azure]

In caso di domande su questa libreria, è possibile pubblicare un post nel [forum di Azure su MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) o in [Overflow dello stack](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files). Per inviare suggerimenti per Archiviazione di Azure, pubblicare un post nella pagina dei [commenti e suggerimenti per Archiviazione di Azure](http://feedback.azure.com/forums/217298-storage).

[Libreria iOS di Archiviazione di Azure]: https://github.com/azure/azure-storage-ios
[API REST di Archiviazione di Azure]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage

<!---HONumber=AcomDC_0107_2016-->