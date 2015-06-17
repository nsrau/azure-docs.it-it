<properties 
	pageTitle="Gestire l'accesso alle risorse di archiviazione di Azure" 
	description="Informazioni sui diversi modi in cui è possibile gestire l'accesso alle risorse di archiviazione di Azure." 
	services="storage" 
	documentationCenter="" 
	authors="micurd,tamram" 
	manager="jahogg" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="micurd"/>

# Gestire l'accesso alle risorse di archiviazione di Azure

## Informazioni generali

Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere a BLOB, tabelle e code all'interno dell'account. Se il servizio o l'applicazione deve rendere disponibili queste risorse per altri client senza condividere la chiave di accesso, è possibile usare le seguenti opzioni per consentire l'accesso:

- È possibile impostare le autorizzazioni di un contenitore per consentire l'accesso in lettura anonimo al contenitore e ai relativi BLOB. Questa operazione non è consentita per le tabelle o le code.

- È possibile esporre una risorsa tramite una firma di accesso condiviso, che consente di delegare l'accesso limitato a una risorsa contenitore, BLOB, tabella o coda specificando l'intervallo per il quale le risorse sono disponibili e le relative autorizzazioni di cui disporrà un client.

- È possibile usare criteri di accesso archiviati per gestire le firme di accesso condiviso per un contenitore o i relativi BLOB, una coda o una tabella. I criteri di accesso archiviati offrono un'ulteriore misura di controllo sulle firme di accesso condiviso e consentono di revocare le firme in modo semplice.

## Limitare l'accesso a contenitori e BLOB

Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere a un contenitore e ai BLOB in esso contenuti. Se si desidera assegnare a utenti anonimi autorizzazioni di lettura per un contenitore e i relativi BLOB, è possibile impostare le autorizzazioni del contenitore per consentire l'accesso pubblico. Gli utenti anonimi possono leggere i BLOB presenti in un contenitore accessibile pubblicamente senza effettuare l'autenticazione della richiesta.

I contenitori forniscono le seguenti opzioni per la gestione dell'accesso al contenitore:

- Accesso in lettura pubblico completo: Dati BLOB e contenitore possono essere letti tramite richiesta anonima. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

- Accesso in lettura pubblico solo per i BLOB: I dati BLOB all'interno di questo contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client non possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima.

- Nessun accesso in lettura pubblico: Dati BLOB e contenitore possono essere letti solo dal proprietario dell'account.

>[AZURE.NOTE]Se il servizio richiede un controllo più granulare delle risorse BLOB o se si desidera fornire autorizzazioni per operazioni diverse dalla lettura, è possibile usare una firma di accesso condiviso per rendere una risorsa accessibile agli utenti. 

### Funzionalità disponibili per utenti anonimi
Nella tabella seguente sono riportate le operazioni che possono essere richiamate da utenti anonimi quando l'ACL di un contenitore è impostato per consentire l'accesso pubblico.

| Operazione REST                                         | Autorizzazione con accesso in lettura pubblico completo | Autorizzazione con accesso in lettura pubblico solo per i BLOB |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| List Containers                                        | Solo proprietario                              | Solo proprietario                                        |
| Create Container                                       | Solo proprietario                              | Solo proprietario                                        |
| Get Container Properties                               | Tutti                                     | Solo proprietario                                        |
| Get Container Metadata                                 | Tutti                                     | Solo proprietario                                        |
| Set Container Metadata                                 | Solo proprietario                              | Solo proprietario                                        |
| Get Container ACL                                      | Solo proprietario                              | Solo proprietario                                        |
| Set Container ACL                                      | Solo proprietario                              | Solo proprietario                                        |
| Delete Container                                       | Solo proprietario                              | Solo proprietario                                        |
| List Blobs                                             | Tutti                                     | Solo proprietario                                        |
| Put Blob                                               | Solo proprietario                              | Solo proprietario                                        |
| Get Blob                                               | Tutti                                     | Tutti                                               |
| Get Blob Properties                                    | Tutti                                     | Tutti                                               |
| Set Blob Properties                                    | Solo proprietario                              | Solo proprietario                                        |
| Get Blob Metadata                                      | Tutti                                     | Tutti                                               |
| Set Blob Metadata                                      | Solo proprietario                              | Solo proprietario                                        |
| Put Block                                              | Solo proprietario                              | Solo proprietario                                        |
| Get Block List (solo blocchi di cui è stato eseguito il commit)                 | Tutti                                     | Tutti                                               |
| Get Block List (solo blocchi di cui non è stato eseguito il commit o tutti i blocchi) | Solo proprietario                              | Solo proprietario                                        |
| Put Block List                                         | Solo proprietario                              | Solo proprietario                                        |
| Delete Blob                                            | Solo proprietario                              | Solo proprietario                                        |
| Copy Blob                                              | Solo proprietario                              | Solo proprietario                                        |
| Snapshot Blob                                          | Solo proprietario                              | Solo proprietario                                        |
| Lease Blob                                             | Solo proprietario                              | Solo proprietario                                        |
| Put Page                                               | Solo proprietario                              | Solo proprietario                                        |
| Get Page Ranges                                        | Tutti                                     | Tutti                                                  |

## Creare e usare una firma di accesso condiviso
Una firma di accesso condiviso è un URI che concede diritti di accesso limitati a contenitori, BLOB, code e tabelle per un intervallo di tempo specifico. Fornendo a un client una firma di accesso condiviso, è possibile consentire a tale client l'accesso alle risorse presenti nell'account di archiviazione, senza condividere la chiave dell'account.

>[AZURE.NOTE] Per una panoramica dei concetti e un'esercitazione sulle firme di accesso condiviso, vedere [Firme di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md).

Le operazioni supportate mediante le firme di accesso condiviso includono:

- Lettura e scrittura dei contenuto di BLOB di pagine o in blocchi, elenchi di blocchi, proprietà e metadati

- Eliminazione, leasing e creazione di uno snapshot di un BLOB

- Elenco dei BLOB in un contenitore

- Aggiunta, rimozione, aggiornamento ed eliminazione dei messaggi in coda (in Libreria client di archiviazione 2.0 e versioni successive)

- Recupero dei metadati della coda, incluso il numero di messaggi (in Libreria client di archiviazione 2.0 e versioni successive)

- Esecuzione di query, aggiunta, aggiornamento, eliminazione e inserimento con aggiornamento delle entità tabella (in Libreria client di archiviazione 2.0 e versioni successive)

I parametri di query dell'URI della firma di accesso condiviso incorporano tutte le informazioni necessarie per concedere l'accesso controllato a una risorsa di archiviazione. I parametri di query dell'URI specificano il periodo di validità della firma di accesso condiviso, le autorizzazioni concesse, la risorsa che deve essere resa disponibile e la firma che i servizi di archiviazione devono usare per autenticare la richiesta.

L'URI della firma di accesso condiviso può inoltre fare riferimento a criteri di accesso archiviati che forniscono un livello di controllo aggiuntivo su un set di firme, inclusa la possibilità di modificare o revocare l'accesso alla risorsa, se necessario. 

Per informazioni sul formato URI di una firma di accesso condiviso, vedere [Delega dell'accesso con una firma di accesso condiviso](https://msdn.microsoft.com/library/ee395415.aspx).

### Utilizzo sicuro di firme di accesso condiviso
Una firma di accesso condiviso concede l'accesso alla risorsa specificata dalle autorizzazioni fornite dall'URI. Per creare l'URI di una firma di accesso condiviso è consigliabile usare sempre HTTPS. L'utilizzo di HTTP con firme di accesso condiviso può rendere l'account di archiviazione vulnerabile a uso dannoso.

Se una firma di accesso condiviso concede l'accesso non destinato al pubblico, deve essere creata con il minor numero possibile di autorizzazioni. Inoltre, una firma di accesso condiviso deve essere distribuita in modo sicuro ai client tramite una connessione protetta, deve essere associata a criteri di accesso archiviati a scopo di revoca e deve specificare la durata più breve possibile per la firma.

>[AZURE.NOTE] L'URI di una firma di accesso condiviso è associato alla chiave dell'account usata per creare la firma e ai relativi criteri di accesso archiviati (se presenti). Se non sono specificati criteri di accesso archiviati, l'unico modo per revocare una firma di accesso condiviso consiste nel modificare la chiave dell'account. 

### Creazione di una firma di accesso condiviso
L'esempio di codice seguente crea criteri di accesso per un contenitore e genera quindi una firma di accesso condiviso per il contenitore. Questa firma di accesso condiviso può essere quindi fornita ai client:

    // The connection string for the storage account.  Modify for your account.
    string storageConnectionString =
       "DefaultEndpointsProtocol=https;" +
       "AccountName=myaccount;" +
       "AccountKey=<account-key>";
    
    // As an alternative, you can retrieve storage account information from an app.config file. 
    // This is one way to store and retrieve a connection string if you are 
    // writing an application that will run locally, rather than in Microsoft Azure.
    
    // string storageConnectionString = ConfigurationManager.AppSettings["StorageAccountConnectionString"];
    
    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Create blob container permissions, consisting of a shared access policy 
    // and a public access setting. 
    BlobContainerPermissions blobPermissions = new BlobContainerPermissions();
    
    // The shared access policy provides 
    // read/write access to the container for 10 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don't set start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
       Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
    });
    
    // The public access setting explicitly specifies that 
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Set the permission policy on the container.
    container.SetPermissions(blobPermissions);
    
    // Get the shared access signature to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

### Uso di una firma di accesso condiviso
Un client che riceve una firma di accesso condiviso può usarla dal proprio codice per creare un oggetto di tipo [StorageCredentials](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.auth.storagecredentials.aspx). Queste credenziali possono essere quindi usate per creare un oggetto [CloudStorageAccount](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.cloudstorageaccount.aspx) o [CloudBlobClient](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx) per l'utilizzo della risorsa, come illustrato nell'esempio seguente:

    Uri blobUri = new Uri("https://myaccount.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Upload the blob. 
    // If the blob does not yet exist, it will be created. 
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Test\myblob.txt"))
    {
    blob.UploadFromStream(fileStream);
    }

## Uso di criteri di accesso condiviso
I criteri di accesso archiviati forniscono un livello di controllo aggiuntivo sulle firme di accesso condiviso sul lato server. La definizione di criteri di accesso archiviati viene usata per raggruppare le firme di accesso condiviso e fornire limitazioni aggiuntive per le firme vincolate dai criteri. È possibile usare criteri di accesso archiviati per modificare l'ora di inizio, la scadenza o le autorizzazioni per una firma o per revocarla dopo che è stata emessa.

I criteri di accesso archiviati offrono maggiore controllo sulle firme di accesso condiviso rilasciate. Anziché specificare la durata della firma e le autorizzazioni nell'URL, è possibile specificare questi parametri nei criteri di accesso archiviati nel BLOB, nel contenitore, nella coda o nella tabella condivisa. Per modificare questi parametri per una o più firme, è possibile modificare i criteri di accesso archiviati, anziché emettere nuovamente le firme. È possibile anche revocare rapidamente la firma modificando i criteri di accesso archiviati.

Si supponga, ad esempio, di avere emesso una firma di accesso condiviso associata a criteri di accesso archiviati. Se è stata specificata la scadenza nei criteri di accesso archiviati, è possibile modificare i criteri di accesso per estendere la durata della firma, senza dover emettere una nuova firma.

È consigliabile specificare criteri di accesso archiviati per qualsiasi risorsa firmata per cui si emette una firma di accesso condiviso, in quanto i criteri archiviati possono essere usati per modificare o revocare la firma dopo l'emissione. Se non si specificano criteri archiviati, è consigliabile limitare la durata della firma per ridurre i rischi per le risorse dell'account di archiviazione. 

### Associazione di una firma di accesso condiviso a un criterio di accesso archiviato
Un criterio di accesso archiviato include un nome di una lunghezza massima di 64 caratteri univoco all'interno del contenitore, coda o tabella. Per associare una firma di accesso condiviso a un criterio di accesso archiviato, è necessario specificare questo identificatore quando si crea la firma di accesso condiviso. Nell'URI della firma di accesso condiviso, il campo *signedidentifier* specifica l'identificatore per il criterio di accesso archiviato.

Un contenitore, una coda o una tabella può includere fino a 5 criteri di accesso archiviati. Ogni criterio può essere usato da qualsiasi numero di firme di accesso condiviso.

>[AZURE.NOTE]Quando si stabilisce un criterio di accesso archiviato in un contenitore, in una coda o in una tabella, potrebbero essere necessari fino a 30 secondi per rendere effettivo il criterio. Durante questo intervallo, una firma di accesso condiviso associata al criterio di accesso archiviato avrà esito negativo con codice di stato 403 (accesso negato), fino a quando il criterio di accesso non diventa attivo.

### Definizione dei parametri dei criteri di accesso per i criteri di accesso condiviso
I criteri di accesso archiviati possono specificare i seguenti parametri per le firme con cui sono associati:

- Ora di inizio

- Scadenza

- Autorizzazioni

A seconda del modo in cui si desidera controllare l'accesso alla risorsa di archiviazione, è possibile specificare tutti questi parametri nei criteri di accesso archiviati e ometterli nell'URL per la firma di accesso condiviso. In questo modo sarà possibile modificare il comportamento della firma associata in qualsiasi momento, nonché revocarla. In alternativa, è possibile specificare uno o più parametri dei criteri di accesso nei criteri di accesso archiviati e gli altri parametri nell'URL. Infine, è possibile specificare tutti i parametri nell'URL. In questo caso, è possibile usare i criteri di accesso archiviati per revocare la firma, ma non per modificarne il comportamento.

Insieme, la firma di accesso condiviso e i criteri di accesso archiviati devono includere tutti i campi necessari per autenticare la firma. Se uno dei campi obbligatori risulta mancante, la richiesta avrà esito negativo con codice di stato 403 (accesso negato). Analogamente, se un campo è specificato sia nell'URL della firma di accesso condiviso che nei criteri di accesso archiviati, la richiesta avrà esito negativo con codice di stato 403 (richiesta non valida). Per altre informazioni sui campi che comprendono la firma, vedere Creare e usare una firma di accesso condiviso.

### Modifica o revoca di un criterio di accesso archiviato

Per revocare l'accesso alle firme di accesso condiviso che usano lo stesso criterio di accesso archiviato, rimuovere il criterio archiviato dalla risorsa di archiviazione sovrascrivendo l'elenco dei criteri archiviati con un nuovo elenco che non contiene il nome del criterio. Per modificare le impostazioni di accesso di un criterio di accesso archiviato, sovrascrivere l'elenco dei criteri archiviati con un nuovo elenco contenente un criterio dello stesso nome con nuovi dettagli di controllo di accesso.


<!--HONumber=52--> 