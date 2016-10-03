<properties
    pageTitle="Come usare l'archivio BLOB da iOS | Microsoft Azure"
	description="Archiviare i dati non strutturati nel cloud con l'archivio BLOB (archivio di oggetti) di Azure."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="micurd;tamram"/>

# Come usare l'archivio BLOB da iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)] 
<br/> 
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## Panoramica

Questa guida illustra i diversi scenari comuni di uso del servizio di archiviazione BLOB di Microsoft Azure. Gli esempi sono scritti in Objective-C e usano la [libreria del client di archiviazione di Azure per iOS](https://github.com/Azure/azure-storage-ios). Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB. Per ulteriori informazioni sui BLOB, vedere la sezione [Passaggi successivi](#next-steps). È possibile scaricare l'[app di esempio](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) per visualizzare rapidamente l'uso di Archiviazione di Azure in un'applicazione iOS.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Importare la libreria iOS di Archiviazione di Azure nell'applicazione

È possibile importare la libreria iOS di archiviazione di Azure nell'applicazione tramite l'uso del [CocoaPod di Archiviazione di Azure](https://cocoapods.org/pods/AZSClient) o importando il file **Framework**.

## CocoaPod

1. Se non ancora installato, [installare CocoaPod](https://guides.cocoapods.org/using/getting-started.html#toc_3) nel computer aprendo una finestra del terminale ed eseguendo il comando seguente

        sudo gem install cocoapods

2. Successivamente, nella directory del progetto (la directory contenente il file `.xcodeproj`), creare un nuovo file denominato `Podfile` (nessuna estensione di file). Aggiungere quanto segue a `Podfile` e salvare

        pod 'AZSClient'

3. Nella finestra del terminale passare alla directory del progetto ed eseguire il comando seguente

        pod install

4. Se il `.xcodeproj` è aperto in Xcode, chiuderlo. Nella directory del progetto aprire il file di progetto appena creato che avrà l'estensione `.xcworkspace`. Si tratta del file che verrà usato d'ora in poi.

## Framework
Per usare la libreria iOS di Archiviazione di Azure, innanzitutto sarà necessario compilare il file del framework.

1. Per prima cosa, scaricare o clonare il [repository azure-storage-ios](https://github.com/azure/azure-storage-ios).

2. Andare ad *azure-storage-ios* -> *Lib* -> *Azure Storage Client Library* e aprire `AZSClient.xcodeproj` in Xcode.

3. In alto a sinistra in Xcode cambiare lo schema attivo da "Azure Storage Client Library" a "Framework".

4. Compilare il progetto (⌘+B). Verrà creato un file `AZSClient.framework` sulla scrivania.

È quindi possibile importare il file framework nell'applicazione eseguendo le seguenti operazioni:

1. Creare un nuovo progetto o aprire il progetto esistente in Xcode.

2. Fare clic sul progetto nel riquadro di spostamento a sinistra e fare clic sulla scheda *General* nella parte superiore dell'editor del progetto.

3. Nella sezione *Linked Frameworks and Libraries* fare clic sul pulsante Add (+).

4. Fare clic su *Add Other*. Passare al file `AZSClient.framework` appena creato e aggiungerlo.

5. Nella sezione *Linked Frameworks and Libraries* fare nuovamente clic sul pulsante Add (+).

6. Nell'elenco di librerie già fornito cercare `libxml2.2.dylib` e aggiungerla al progetto.

7. Fare clic sulla scheda *Build Settings* (Impostazioni di compilazione) nella parte superiore dell'editor di progetto.

8. Nella sezione *Search Paths* (Percorsi di ricerca) fare doppio clic su *Framework Search Paths* (Percorsi di ricerca framework) e aggiungere il percorso al file `AZSClient.framework`.

## Istruzione import
Sarà necessario includere l'istruzione import seguente nel file in cui si vuole richiamare l'API di archiviazione di Azure.

    // Include the following import statement to use blob APIs.
    #import <AZSClient/AZSClient.h>

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## Operazioni asincrone
> [AZURE.NOTE] Tutti i metodi che eseguono una richiesta al servizio sono operazioni asincrone. Negli esempi di codice si noterà che questi metodi hanno un gestore completamento. Il codice nel gestore completamento verrà eseguito **dopo** il completamento della richiesta. Il codice dopo il gestore completamento verrà eseguito **mentre** la richiesta è in corso.

## Creare un contenitore
Ogni BLOB nell'archivio di Azure deve risiedere in un contenitore. L'esempio seguente mostra come creare un contenitore, denominato *newcontainer*, nell'account di archiviazione se non esiste già. Quando si sceglie un nome per il contenitore, tenere presenti le regole indicate in precedenza.

    -(void)createContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

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

È possibile verificarne il funzionamento controllando [Esplora archivi di Microsoft Azure](http://storageexplorer.com) e accertandosi che *newcontainer* sia nell'elenco di contenitori dell'account di archiviazione.

## Impostare le autorizzazioni del contenitore
Per impostazione predefinita, le autorizzazioni di un contenitore vengono configurate per l'accesso **Privato**. I contenitori, tuttavia, offrono alcune opzioni diverse per l'accesso al contenitore:

- **Privato**: dati BLOB e contenitore possono essere letti solo dal proprietario dell'account.

- **BLOB**: i dati BLOB all'interno di questo contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client non possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima.

- **Contenitore**: dati BLOB e contenitore possono essere letti tramite richiesta anonima. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

L'esempio seguente mostra come creare un contenitore con le autorizzazioni di accesso **Contenitore** che consentiranno l'accesso pubblico di sola lettura per tutti gli utenti in Internet:

    -(void)createContainerWithPublicAccess{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

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
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

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

È possibile verificarne il funzionamento controllando [Esplora archivi di Microsoft Azure](http://storageexplorer.com) e accertandosi che il contenitore *containerpublic* contenga il BLOB *sampleblob*. Poiché in questo esempio è stato usato un contenitore pubblico, per verificarne il funzionamento è possibile andare all'URI dei BLOB:

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
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

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
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

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
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

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
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

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

A questo punto, dopo avere appreso a usare l'archiviazione BLOB da iOS, seguire questi collegamenti per acquisire maggiori informazioni sulla libreria iOS e il servizio di archiviazione.

- [Libreria client di archiviazione di Azure per iOS](https://github.com/azure/azure-storage-ios)
- [Documentazione di riferimento iOS di Archiviazione di Azure](http://azure.github.io/azure-storage-ios/)
- [API REST dei servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage)

In caso di domande su questa libreria, è possibile pubblicare un post nel [forum di Azure su MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) o in [Overflow dello stack](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files). Per inviare suggerimenti per Archiviazione di Azure, pubblicare un post nella pagina dei [commenti e suggerimenti per Archiviazione di Azure](https://feedback.azure.com/forums/217298-storage/).

<!---HONumber=AcomDC_0921_2016-->