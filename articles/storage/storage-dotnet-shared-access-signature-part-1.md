<properties
	pageTitle="Uso delle firme di accesso condiviso | Microsoft Azure"
	description="Informazioni sulla delega dell'accesso alle risorse di archiviazione di Azure, tra cui BLOB, code e tabelle, tramite firme di accesso condiviso (SAS)."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/07/2016"
	ms.author="tamram"/>



# Uso delle firme di accesso condiviso

## Overview

Una firma di accesso condiviso costituisce un potente strumento per concedere ad altri client accesso limitato a BLOB, tabelle e code dell'account di archiviazione, senza dover esporre la chiave dell'account. Nella parte 1 di questa esercitazione sulle firme di accesso condiviso verranno fornite informazioni di carattere generale sul modello della firma di accesso condiviso e ne verranno esaminate le procedure consigliate. [Nella ](storage-dotnet-shared-access-signature-part-2.md)parte 2 dell'esercitazione verrà invece illustrato il processo di creazione delle firme di accesso condiviso tramite il servizio BLOB.

## Informazioni sulle firme di accesso condiviso

Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. Questo significa che è possibile concedere a un client autorizzazioni limitate per BLOB, code o tabelle per un periodo di tempo specificato e con un set di autorizzazioni specificato senza dover condividere le chiavi di accesso dell'account. La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma al costruttore o al metodo appropriato.

## Perché usare una firma di accesso condiviso

È possibile usare una firma di accesso condiviso quando si desidera fornire l'accesso alle risorse dell'account di archiviazione a un client al quale non si desidera fornire la chiave dell'account. Le chiavi dell'account di archiviazione includono una chiave primaria e una chiave secondaria, che garantiscono entrambi accesso amministrativo all'account e a tutte le risorse in esso presenti. Se si espone una delle chiavi dell'account, è possibile che l'account venga utilizzato in modo dannoso o non appropriato. Le firme di accesso condiviso costituiscono un'alternativa sicura per consentire ad altri client di leggere, scrivere ed eliminare dati nell'account di archiviazione sulla base delle autorizzazioni concesse e senza richiedere la chiave dell'account.

Uno scenario comune in cui la firma di accesso condiviso risulta utile è costituito da un servizio in cui gli utenti leggono e scrivono i propri dati nell'account di archiviazione di un utente specifico. Uno scenario in cui i dati utente vengono archiviati nell'account di archiviazione è caratterizzato da due modelli di progettazione standard:


1\. I client caricano e scaricano dati tramite un servizio proxy front-end che esegue l'autenticazione. Tale servizio presenta il vantaggio di consentire la convalida delle regole di business, tuttavia per grandi quantità di dati o per transazioni con volumi elevati, la creazione di un servizio scalabile in base alla domanda può risultare oneroso o complesso.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\. Un servizio semplificato autentica il client in base alle necessità e quindi genera una firma di accesso condiviso. Dopo aver ricevuto la firma di accesso condiviso, il client può quindi accedere alle risorse dell'account di archiviazione direttamente con le autorizzazioni definite dalla firma e per l'intervallo consentito dalla firma. La firma di accesso condiviso consente di ridurre la necessità di instradare tutti i dati tramite il servizio proxy front-end.

![sas-storage-provider-service][sas-storage-provider-service]

In molti servizi effettivi è possibile utilizzare una versione ibrida di questi due approcci, a seconda dello scenario interessato, in cui alcuni dati vengono elaborati e convalidati tramite il proxy front-end, mentre altri vengono salvati e/o letti direttamente tramite la firma di accesso condiviso.

Inoltre, è necessario utilizzare una firma di accesso condiviso per autenticare l'oggetto di origine in un'operazione di copia in determinati scenari:

- Quando si copia un BLOB in un altro BLOB che risiede in un account di archiviazione diverso, è necessario utilizzare una firma di accesso condiviso per autenticare il BLOB di origine. Con la versione 2015-04-05 è anche possibile usare una firma di accesso condiviso per autenticare il BLOB di destinazione.
- Quando si copia un file a un altro file che si trova in un account di archiviazione diverso, è necessario utilizzare una firma di accesso condiviso per autenticare il file di origine. Con la versione 2015-04-05 è anche possibile usare una firma di accesso condiviso per autenticare il file di destinazione.
- Quando si copia un BLOB in un file o un file in un BLOB, è necessario utilizzare una firma di accesso condiviso (SAS) per autenticare l'oggetto di origine, anche se gli oggetti di origine e di destinazione si trovano nello stesso account di archiviazione.

## Tipi di firme di accesso condiviso

La versione 2015-04-05 di Archiviazione di Azure introduce un nuovo tipo di firma di accesso condiviso, la firma di accesso condiviso dell'account. È ora possibile creare uno dei due tipi di firme di accesso condiviso seguenti:

- **Firma di accesso condiviso dell'account.** La firma di accesso condiviso dell'account delega l'accesso alle risorse in uno o più servizi di archiviazione. Tutte le operazioni disponibili tramite la firma di accesso condiviso del servizio lo sono anche tramite la firma di accesso condiviso dell'account. Inoltre, con la firma di accesso condiviso dell'account è possibile delegare l'accesso a operazioni che si applicano a un servizio specifico, ad esempio **Get/Set Service Properties** e **Get Service Stats**. È anche possibile delegare l'accesso alle operazioni di lettura, scrittura ed eliminazioni in contenitori BLOB, tabelle, code e condivisioni file, che con una firma di accesso condiviso del servizio non è consentito. Per informazioni approfondite su come creare il token di firma di accesso condiviso dell'account, vedere [Creazione di una firma di accesso condiviso dell'account](https://msdn.microsoft.com/library/mt584140.aspx).

- **Firma di accesso condiviso del servizio.** La firma di accesso condiviso del servizio delega l'accesso a una risorsa in uno solo dei servizi di archiviazione, ovvero nel servizio BLOB, nel servizio di accodamento, nel servizio tabelle o nel servizio file. Per informazioni approfondite su come creare il token di firma di accesso condiviso del servizio, vedere [Creazione di una firma di accesso condiviso del servizio](https://msdn.microsoft.com/library/dn140255.aspx) ed [Esempi di firma di accesso condiviso del servizio](https://msdn.microsoft.com/library/dn140256.aspx).

## Funzionamento della firma di accesso condiviso

Una firma di accesso condiviso è un URI che punta a una o più risorse di archiviazione e include un token che contiene un set di parametri di query speciale. Il token indica la modalità di accesso alla risorsa da parte del client. Uno dei parametri di query, ovvero la firma, viene creato dai parametri della firma di accesso condiviso e viene firmato con la chiave dell'account. Tale firma viene utilizzata dal servizio di archiviazione di Azure per autenticare la firma di accesso condiviso.

I token di firma di accesso condiviso dell'account e del servizio includono parametri comuni e accettano alcuni parametri diversi.

### Parametri comuni per la firma di accesso condiviso dell'account e del servizio

- **Api version** Parametro facoltativo che specifica la versione del servizio di archiviazione da usare per eseguire la richiesta.
- **Service version** Parametro obbligatorio che specifica la versione del servizio di archiviazione da usare per autenticare la richiesta.
- **Ora di inizio.** Si riferisce all'ora in cui la firma di accesso condiviso diventa valida. L'ora di inizio di una firma di accesso condiviso è facoltativa; se omessa, la firma diventa immediatamente valida. Deve essere espressa in UTC (Coordinated Universal Time), con uno speciale designatore UTC ("Z"), ad esempio 1994-11-05T13:15:30Z.
- **Scadenza.** Si riferisce all'ora dopo la quale la firma di accesso condiviso non è più valida. Secondo le procedure consigliate è preferibile specificare una data di scadenza per una firma di accesso condiviso oppure associarla a criteri di accesso archiviati. Deve essere espressa in UTC (Coordinated Universal Time), con uno speciale designatore UTC ("Z"), ad esempio 1994-11-05T13:15:30Z (vedere più avanti).
- **Autorizzazione.** Le autorizzazioni specificate per la firma di accesso condiviso indicano le autorizzazioni che il client può eseguire sulla risorsa di archiviazione usando la firma. Le autorizzazioni disponibili per la firma di accesso condiviso dell'account e del servizio sono diverse.
- **IP.** Un parametro facoltativo che specifica un indirizzo IP o un intervallo di indirizzi IP all'esterno di Azure (vedere la sezione [Stato di configurazione della sessione di Routing](../expressroute/expressroute-workflows.md#routing-session-configuration-state) per Express route) da cui si desidera accettare le richieste.
- **Protocol.** Parametro facoltativo che specifica il protocollo consentito per una richiesta. I valori possibili sono HTTPS e HTTP (https,http), ovvero il valore predefinito, oppure HTTPS only (https). Si noti che HTTP only non è un valore consentito.
- **Signature.** La firma viene creata dagli altri parametri specificati come parte token e quindi crittografati. Viene quindi usata per autenticare la firma di accesso condiviso.

### Parametri per il token della firma di accesso condiviso dell'account

- **Servizio o servizi.** La firma di accesso condiviso può delegare l'accesso a uno o più servizi di archiviazione. Ad esempio, è possibile creare una firma di accesso condiviso dell'account che delega l'accesso al servizio BLOB e al servizio File. Oppure, è possibile creare una firma di accesso condiviso che delega l'accesso a tutti e quattro i servizi (BLOB, Coda, Tabelle e File).
- **Tipi di risorse di archiviazione.** Una firma di accesso condiviso dell'account si applica a una o più classi di risorse di archiviazione, piuttosto che a una risorsa specifica. È possibile creare una firma di accesso condiviso dell'account per delegare l'accesso a:
	- API a livello di servizio, che vengono chiamate sulla risorsa dell'account di archiviazione. Ad esempio: **Get/Set Service Properties**, **Get Service Stats**, e **List Containers/Queues/Tables/Shares**.
	- API a livello di contenitore, che vengono chiamate sugli oggetti contenitore per ogni servizio, contenitore BLOB, code, tabelle e condivisioni file. Ad esempio: **Create/Delete Container**, **Create/Delete Queue**, **Create/Delete Table**, **Create/Delete Share** e **List Blobs/Files and Directories**.
	- API a livello di oggetto, che vengono chiamate su BLOB, messaggi di coda, entità tabelle e file. Ad esempio: **Put Blob**, **Query Entity**, **Get Messages** e **Create File**.

### Parametri per il token della firma di accesso condiviso del servizio

- **Risorsa di archiviazione.** Le risorse di archiviazione per cui è possibile delegare l'accesso con una firma di accesso condiviso del servizio sono:
	- Contenitori e BLOB
	- Condivisioni di file e file
	- Queues
	- Le tabelle e gli intervalli di entità della tabella.

## Esempi di URI di firma di accesso condiviso

Di seguito è riportato un esempio di URI di firma di accesso condiviso del servizio che fornisce autorizzazioni di lettura e scrittura a un BLOB. Nella tabella le singole parti dell'URI della firma di accesso condiviso vengono descritte separatamente per facilitarne la comprensione:

	https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

Nome|Parte firma di accesso condiviso|Descrizione
---|---|---
URI del BLOB|https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt |Indirizzo del BLOB. Si noti che è vivamente consigliato l'uso di HTTPS.
Versione dei servizi di archiviazione|sv=2015-04-05|Per i servizi di archiviazione della versione 2012-02-12 e successive questo parametro indica la versione da usare.
Ora di inizio|st=2015-04-29T22%3A18%3A26Z|Specificata nell'ora UTC. Se si desidera che la firma di accesso condiviso sia immediatamente valida, omettere l'ora di inizio.
Scadenza|se=2015-04-30T02%3A23%3A26Z|Specificata nell'ora UTC.
Risorsa|sr=b|La risorsa è un BLOB.
Autorizzazioni|sp=rw|Le autorizzazioni concesse dalla firma di accesso condiviso includono lettura (r) e scrittura (w).
Intervallo IP|sip=168.1.5.60-168.1.5.70|Intervallo di indirizzi IP da cui verrà accettata una richiesta.
Protocol|spr=https|Sono consentite solo richieste tramite HTTPS.
Firma|sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D|Utilizzata per autenticare l'accesso al BLOB. La firma è un HMAC calcolato sulla base di una stringa da firmare e della chiave mediante l'algoritmo SHA256, e quindi codificato con la codifica Base64.

Di seguito è riportato un esempio di firma di accesso condiviso dell'account che usa gli stessi parametri comuni nel token. Poiché questi parametri sono già stati illustrati in precedenza, non sono descritti qui. La tabella seguente include solo i parametri specifici per la firma di accesso condiviso dell'account.

	https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B

Nome|Parte firma di accesso condiviso|Descrizione
---|---|---
URI della risorsa|https://myaccount.blob.core.windows.net/?restype=service&comp=properties|The Endpoint di servizio BLOB con parametri per ottenere le proprietà del servizio (operazione GET) o per impostare le proprietà del servizio (operazione SET).
Servizi|ss=bf|La firma di accesso condiviso si applica ai servizi BLOB e File
Tipi di risorse|srt=s|La firma di accesso condiviso si applica alle operazioni a livello di servizio.
Autorizzazioni|sp=rw|Le autorizzazioni concedono l'accesso alle operazioni di lettura e scrittura.  

Poiché le autorizzazioni sono limitate a livello di servizio, le operazioni accessibili tramite questa firma di accesso condiviso sono **Get Blob Service Properties** (lettura) e **Set Blob Service Properties** (scrittura). Con un URI di risorsa diverso, lo stesso token di firma di accesso condiviso può tuttavia essere usato per delegare l'accesso all'operazione **Get Blob Service Stats** (lettura).

## Controllo di una firma di accesso condiviso con criteri di accesso archiviati ##

Una firma di accesso condiviso può assumere una delle due forme seguenti:

- **SAS ad hoc:**quando si crea una firma di accesso condiviso ad hoc, l'ora di inizio, la scadenza e le autorizzazioni vengono tutte specificate nell'URI corrispondente oppure sono implicite, nel caso in cui l'ora di inizio viene omessa. È possibile creare questa firma di accesso condiviso come firma di accesso condiviso dell'account o del servizio.

- **SAS con politica di accesso archiviazione:**i criteri di accesso archiviati vengono definiti per un contenitore di risorse - un contenitore BLOB, una tabella o una coda, e possono essere usati per gestire i vincoli per una o più firme di accesso condiviso. Quando si associa una firma di accesso condiviso a criteri di accesso archiviati, la firma eredita i vincoli, ovvero ora di inizio, scadenza e autorizzazioni, definiti per i criteri di accesso archiviati.

>[AZURE.NOTE] Al momento, una firma di accesso condiviso dell'account deve essere una firma di accesso condiviso ad-hoc. I criteri di accesso archiviati non sono ancora supportati per la firma di accesso condiviso dell'account.

La differenza tra le due forme è importante un unico scenario chiave, la revoca. Una firma di accesso condiviso è un URL, pertanto chiunque la ottiene può utilizzarla indipendentemente da chi l'ha richiesta per iniziare. Se la firma di accesso condiviso è stata pubblicata e resa pubblica, può essere usata da chiunque in tutto il mondo. Una forma di accesso condiviso rimane valida finché non si verifica una delle quattro condizioni seguenti:

1.	Viene raggiunta la scadenza specificata nella firma.
2.	Viene raggiunta la scadenza specificata nei criteri di accesso archiviati cui viene fatto riferimento nella firma (se viene fatto riferimento a criteri di accesso archiviati e se questi indicano una scadenza). Tale situazione può verificarsi alla scadenza dell'intervallo oppure perché i criteri di accesso archiviati sono stati modificati in modo che la scadenza ricorra nel passato e ciò corrisponde a un modo per revocare la firma di accesso condiviso.
3.	I criteri di accesso archiviati cui viene fatto riferimento nella firma di accesso condiviso vengono eliminati e ciò corrisponde a un altro modo per revocare la firma. Si noti che se si ricreano i criteri di accesso archiviati con lo stesso nome, tutti i token esistenti della firma di accesso condiviso saranno ancora validi in base alle autorizzazioni associate ai criteri (presupponendo che non sia stata superata la scadenza indicata nella firma). Se si intende revocare la firma di accesso condiviso, assicurarsi di usare un nome diverso per ricreare i criteri di accesso archiviati con scadenza nel futuro.
4.	La chiave dell'account utilizzata per creare la firma di accesso condiviso viene rigenerata. Si noti che in seguito a tale operazione tutti i componenti dell'applicazione che utilizzano la chiave dell'account non verranno più autenticati finché non verranno aggiornati in modo da usare l'altra chiave dell'account valida oppure la chiave dell'account appena rigenerata.

>[AZURE.IMPORTANT] L'URI di una firma di accesso condiviso è associato alla chiave dell'account usata per creare la firma e ai relativi criteri di accesso archiviati (se presenti). Se non sono specificati criteri di accesso archiviati, l'unico modo per revocare una firma di accesso condiviso consiste nel modificare la chiave dell'account.

## Esempi: creare e usare firme di accesso condiviso

Di seguito sono riportati alcuni esempi di entrambi i tipi di firma di accesso condiviso, dell'account e del servizio.

Per eseguire questi esempi, sarà necessario scaricare e fare riferimento a questi pacchetti:

- [Azure Storage Client Library for .NET](http://www.nuget.org/packages/WindowsAzure.Storage), versione 6.x o versione successiva (per usare l'account SAS).
- [Gestione configurazione di Azure](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

### Esempio: Account SAS

L'esempio di codice seguente crea una firma di accesso condiviso valida per i servizi BLOB e File e concede al client le autorizzazioni di lettura, scrittura ed elenco per accedere alle API a livello di servizio. Per la firma di accesso condiviso dell'account il protocollo è limitato ad HTTPS, pertanto è necessario creare una richiesta HTTPS.

    static string GetAccountSASToken()
    {
        // To create the account SAS, you need to use your shared key credentials. Modify for your account.
	    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

        // Create a new access policy for the account.
        SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
            {
                Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
                Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
                ResourceTypes = SharedAccessAccountResourceTypes.Service,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Protocols = SharedAccessProtocol.HttpsOnly
            };

        // Return the SAS token.
        return storageAccount.GetSharedAccessSignature(policy);
    }

Per usare la firma di accesso condiviso dell'account per accedere alle API a livello di servizio per il servizio BLOB, creare un oggetto client BLOB usando la firma di accesso condiviso e l'endpoint account di archiviazione per l'account di archiviazione corrente.

    static void UseAccountSAS(string sasToken)
    {
        // In this case, we have access to the shared key credentials, so we'll use them
        // to get the Blob service endpoint.
	    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create new storage credentials using the SAS token.
        StorageCredentials accountSAS = new StorageCredentials(sasToken);
        // Use these credentials and the Blob storage endpoint to create a new Blob service client.
        CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, blobClient.StorageUri, null, null, null);
        CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

        // Now set the service properties for the Blob client created with the SAS.
        blobClientWithSAS.SetServiceProperties(new ServiceProperties()
        {
            HourMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            MinuteMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            Logging = new LoggingProperties()
            {
                LoggingOperations = LoggingOperations.All,
                RetentionDays = 14,
                Version = "1.0"
            }
        });

        // The permissions granted by the account SAS also permit you to retrieve service properties.
        ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
        Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
        Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
        Console.WriteLine(serviceProperties.HourMetrics.Version);
    }

### Esempio: servizio SAS con criteri di accesso archiviati

L'esempio di codice seguente crea un criterio di accesso archiviato in un contenitore, quindi genera una firma di accesso condiviso del servizio per il contenitore. Questa firma di accesso condiviso può essere quindi concessa ai client per le autorizzazioni di lettura-scrittura sul contenitore. Modificare il codice per usare il proprio nome dell’account:

    // Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();

    // Get the current permissions for the blob container.
    BlobContainerPermissions blobPermissions = container.GetPermissions();

    // Clear the container's shared access policies to avoid naming conflicts.
    blobPermissions.SharedAccessPolicies.Clear();

    // The new shared access policy provides read/write access to the container for 24 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don’t set the start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
       Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Add
    });

    // The public access setting explicitly specifies that
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;

    // Set the new stored access policy on the container.
    container.SetPermissions(blobPermissions);

    // Get the shared access signature token to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

Un client in possesso di una firma di accesso condiviso del servizio può usarla dal codice per autenticare una richiesta di lettura o di scrittura in un BLOB nel contenitore. Il codice seguente, ad esempio, usa il token di firma di accesso condiviso per creare un nuovo BLOB in blocchi nel contenitore. Modificare il codice per usare il proprio nome dell’account:

    Uri blobUri = new Uri("https://<myaccount>.blob.core.windows.net/mycontainer/myblob.txt");

    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);

    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);

    // Upload the blob.
    // If the blob does not yet exist, it will be created.
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Temp\myblob.txt"))
    {
    	blob.UploadFromStream(fileStream);
    }


## Procedure consigliate per l'utilizzo delle firme di accesso condiviso

Quando si utilizzano le firme di accesso condiviso nell'applicazione, è necessario essere consapevoli di due rischi potenziali:

- In caso di diffusione di una firma di accesso condiviso, chiunque la ottiene può usarla e questo potrebbe compromettere l'account di archiviazione.
- Se una firma di accesso condiviso fornita a un'applicazione client scade e l'applicazione non è in grado di recuperarne una nuova dal servizio, è possibile che le funzionalità dell'applicazione potrebbero risentirne.

Per bilanciare questi rischi, è consigliabile attenersi ai consigli seguenti relativi all'utilizzo di firme di accesso condiviso:

1. **Usare sempre HTTPS** per creare o distribuire una firma di accesso condiviso. Se una firma di accesso condiviso passata tramite HTTP viene intercettata, un utente malintenzionato che esegue un attacco man-in-the-middle sarà in grado di leggere la firma e quindi di utilizzarla come se fosse l'utente cui è destinata e questo potrebbe comportare la compromissione di dati sensibili o il danneggiamento dei dati da parte dell'utente malintenzionato.
2. **Laddove possibile fare riferimento a criteri di accesso archiviati.** I criteri di accesso archiviati consentono di revocare le autorizzazioni senza rigenerare le chiavi dell'account di archiviazione. Impostare la scadenza di questi criteri su un tempo molto lungo (o infinito) e assicurarsi che venga aggiornata regolarmente in modo che ricorra nel futuro.
3. **Usare scadenze a breve termine per una firma di accesso condiviso ad hoc.** In questo modo, anche se una firma di accesso condiviso viene compromessa inconsapevolmente, sarà valida solo per un breve periodo. Questo consiglio è particolarmente importante se non è possibile fare riferimento a criteri di accesso archiviati e consente inoltre di limitare la quantità di dati che è possibile scrivere in un BLOB riducendo il tempo disponibile per il caricamento.
4. **Se necessario, chiedere ai client di rinnovare automaticamente la firma di accesso condiviso..** I client devono rinnovare la firma di accesso condiviso prima della scadenza prevista al fine di disporre di tempo per ulteriori tentativi nel caso in cui il servizio che fornisce la firma non sia disponibile. Se la firma di accesso condiviso deve essere usata per un numero ridotto di operazioni immediate e di breve durata, che si prevede vengano completate entro la scadenza specificata, tale operazione potrebbe non essere necessaria in quanto non è previsto il rinnovo della firma. Se tuttavia si dispone di client che effettuano normalmente richieste tramite la firma di accesso condiviso, è necessario considerare la possibilità che la firma scada. In questo caso è essenziale trovare un punto di equilibrio tale da garantire che la firma di accesso condiviso sia di breve durata (come indicato in precedenza) e che il client richieda il rinnovo in tempo utile per evitare malfunzionamenti causati dalla scadenza della firma prima del rinnovo.
5. **Prestare attenzione all'ora di inizio della firma di accesso condiviso.** Se si imposta l'ora di inizio della firma di accesso condiviso su **ora**, a causa dello sfasamento di orario, ovvero delle differenze dell'ora corrente a seconda del computer in uso, potrebbero verificarsi problemi intermittenti nei primi minuti. In generale, impostare l'ora di inizio in modo che risalga ad almeno 15 minuti prima oppure non impostarla affatto, in modo che la firma diventi immediatamente valida in qualsiasi caso. Le stesse considerazioni sono valide anche per la scadenza, pertanto è consigliabile osservare fino a 15 minuti di sfasamento di orario in entrambe le direzioni di qualsiasi richiesta. Si noti che per i client che usano una versione di REST precedente alla 2012-02-12, la durata massima della firma di accesso condiviso che non fa riferimento a criteri di accesso archiviati è di 1 ora, pertanto tutti i criteri di durata maggiore non saranno validi.
6.	**Indicare in modo specifico la risorsa cui accedere.** La procedura consigliata di sicurezza standard consiste nel fornire a un utente i privilegi minimi necessari. Se un utente necessita solo dell'accesso in lettura a una singola entità, concedere solo tale tipo di accesso per tale entità e non l'accesso in lettura/scrittura/eliminazione per tutte le entità. Questo contribuirà a ridurre la minaccia di compromissione della firma di accesso condiviso in quanto la firma è meno potente nelle mani di un utente malintenzionato.
7.	**Comprendere che all'account verrà fatturato qualsiasi tipo di utilizzo, incluso quello effettuato con la firma di accesso condiviso.** Se si fornisce accesso in scrittura a un BLOB, un utente potrebbe scegliere di caricare un BLOB da 200 GB. Se poi si offre anche accesso in lettura, gli utenti potrebbero scegliere di scaricarlo 10 volte e ciò potrebbe comportare 2 TB di costi in uscita. Anche in questo caso, fornire autorizzazioni limitate per ridurre l'impatto potenziale di utenti malintenzionati. Per ridurre questa minaccia, usare firme di accesso condiviso di breve durata, prestando però attenzione allo sfasamento di orario per la scadenza.
8.	**Convalidare i dati scritti tramite la firma di accesso condiviso.** Quando un'applicazione client scrive i dati nell'account di archiviazione, tenere presente che tali dati potrebbero causare problemi. Se l'applicazione richiede che i dati vengano convalidati o autorizzati prima dell'uso, è necessario eseguire la convalida dopo la scrittura dei dati e prima che vengano utilizzati dall'applicazione. Questa procedura consente inoltre di evitare la scrittura di dati danneggiati o dannosi nell'account da parte da un utente che ha acquisito correttamente la firma di accesso condiviso o di un utente che sfrutta una firma diffusa per errore.
9. **Non usare sempre SAS.** Talvolta i rischi associati a una particolare operazione su un account di archiviazione superano i benefici derivanti dall'uso della firma di accesso condiviso. Per tali operazioni creare un servizio di livello intermedio che effettui operazioni di scrittura nell'account di archiviazione dopo autenticazione, controllo e convalida di regole di business. Talvolta è inoltre più semplice gestire l'accesso in modi diversi. Se ad esempio si desidera rendere pubblicamente leggibili tutti i BLOB di un contenitore, è possibile rendere pubblico il contenitore anziché fornire una firma di accesso condiviso a ogni client per consentire l'accesso.
10.	**Usare Analisi archiviazione per monitorare l'applicazione.** È possibile usare la registrazione e la metrica per osservare eventuali picchi di errori di autenticazione causati da un'interruzione del servizio del provider di firme di accesso condiviso oppure dalla rimozione accidentale di criteri di accesso archiviati. Per altre informazioni, vedere il [blog del team del servizio di archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx).

## Conclusioni ##

Le firme di accesso condiviso sono utili per offrire autorizzazioni limitate all'account di archiviazione ai client ai quali non si desidera fornire la chiave dell'account. In quanto tali, costituiscono una parte essenziale del modello di sicurezza di qualsiasi applicazione che utilizza il servizio di archiviazione di Azure. Il rispetto delle procedure consigliate sopra elencate consente di usare la firma di accesso condiviso per offrire accesso ancor più flessibile alle risorse dell'account di archiviazione senza compromettere la sicurezza dell'applicazione.

## Passaggi successivi ##

- [Firme di accesso condiviso, parte 2: creare e usare una firma di accesso condiviso con l'archiviazione BLOB](storage-dotnet-shared-access-signature-part-2.md)
- [Introduzione ad Archiviazione file di Azure in Windows](storage-dotnet-how-to-use-files.md)
- [Gestire l'accesso in lettura anonimo a contenitori e BLOB](storage-manage-access-to-resources.md)
- [Delega dell'accesso con una firma di accesso condiviso](http://msdn.microsoft.com/library/azure/ee395415.aspx)
- [Introduzione alla firma di accesso condiviso per tabelle e code](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
[sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png

<!---HONumber=AcomDC_0914_2016-->