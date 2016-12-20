---
title: Crittografia lato client con .NET per Archiviazione di Microsoft Azure | Microsoft Docs
description: La libreria client di archiviazione di Azure per .NET offre supporto per la crittografia lato client e l&quot;integrazione con l&quot;insieme di credenziali chiave di Azure per la massima sicurezza delle applicazioni di archiviazioni Azure.
services: storage
documentationcenter: .net
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: becfccca-510a-479e-a798-2044becd9a64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 223a7f808ae21ddf8e237e7c5ec2d7ba035e38e8
ms.openlocfilehash: 82d91881e7f744471f4e60d1c6571e0ae2f7ee4e


---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Crittografia lato client e Insieme di credenziali chiave Azure per Archiviazione di Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Overview
Il [pacchetto NuGet per la libreria client di archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage) supporta la crittografia dei dati all'interno delle applicazioni client prima del caricamento nell'Archiviazione di Azure, nonché la decrittografia dei dati durante il download nel client. La libreria inoltre supporta l'integrazione con l' [insieme di credenziali chiave](https://azure.microsoft.com/services/key-vault/) di Azure per la gestione delle chiavi dell'account di archiviazione.

Per un'esercitazione dettagliata che guidi l'utente attraverso il processo di crittografia dei BLOB mediante la crittografia lato client e l'insieme di credenziali delle chiavi di Azure, vedere [Crittografare e decrittografare i BLOB in Archiviazione di Microsoft Azure tramite l'insieme di credenziali chiave di Azure](storage-encrypt-decrypt-blobs-key-vault.md).

Per la crittografia sul lato client con Java, vedere [Crittografia sul lato client con Java per l'Archiviazione di Microsoft Azure](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Crittografia e decrittografia tramite la tecnica basata su envelope
Le procedure di crittografia e decrittografia seguono la tecnica basata su envelope.

### <a name="encryption-via-the-envelope-technique"></a>Crittografia tramite la tecnica basata su envelope
La crittografia tramite la tecnica basata su envelope funziona nel modo seguente:

1. La libreria di Azure Storage Client genera una chiave di crittografia del contenuto (CEK) che funziona come chiave simmetrica monouso.
2. I dati utente vengono crittografati con questa chiave CEK.
3. Viene quindi eseguito il wrapping della chiave CEK mediante la chiave di crittografia della chiave (KEK). La chiave KEK è identificata con un identificatore di chiave e può essere costituita da una coppia di chiavi asimmetriche o da una chiave simmetrica. Può essere gestita localmente o archiviata in insiemi di credenziali chiave di Azure.
   
    La libreria del client Archiviazione non ha mai accesso alla chiave KEK. Richiama solo l'algoritmo di wrapping della chiave fornito dall'insieme di credenziali chiave. Gli utenti possono scegliere di usare provider personalizzati per il wrapping o la rimozione del wrapping delle chiavi, se lo desiderano.

4. I dati crittografati vengono quindi caricati nel servizio Archiviazione di Azure. La chiave con wrapping assieme ad alcuni metadati di crittografia aggiuntivi viene archiviata come metadati (su un BLOB) o interpolata con i dati crittografati (entità della tabella e messaggi in coda).

### <a name="decryption-via-the-envelope-technique"></a>Decrittografia tramite la tecnica basata su envelope
La decrittografia tramite la tecnica basata su envelope funziona nel modo seguente:

1. La libreria client presuppone che l'utente gestisce la chiave di crittografia della chiave (KEK) in locale o negli insiemi di credenziali Azure. L'utente non deve necessariamente conoscere la chiave specifica utilizzata per la crittografia. Al contrario, è possibile impostare e utilizzare un resolver di chiavi che risolve diversi identificatori di chiave per le chiavi.
2. La libreria client scarica i dati crittografati insieme al materiale di crittografia archiviato nel servizio.
3. La chiave di crittografia dei contenuti (CEK) con wrapping viene quindi sottoposta a rimozione del wrapping (decrittografata) utilizzando la chiave di crittografia della chiave (KEK). Anche in questo caso, la libreria client non ha accesso alla KEK. Richiama semplicemente l'algoritmo di rimozione del wrapping personalizzato o del provider dell’insieme di credenziali chiave.
4. La chiave di crittografia del contenuto (CEK) viene quindi utilizzata per decrittografare i dati utente crittografati.

## <a name="encryption-mechanism"></a>Meccanismo di crittografia
La libreria client di archiviazione usa [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) per la crittografia dei dati utente. In particolare, si avvale della modalità [Cipher Block Chaining (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) con AES. Ogni servizio funziona in modo diverso, pertanto qui verrà illustrato ciascuno di essi.

### <a name="blobs"></a>Blobs
La libreria client attualmente supporta la crittografia solo di interi BLOB. In particolare, la crittografia è supportata quando gli utenti usano i metodi **UploadFrom*** o il metodo **OpenWrite**. Per i download, sono supportati sia i download completi che di intervallo.

Durante la crittografia, la libreria client genererà un vettore di inizializzazione (IV) casuale di 16 byte con una chiave di crittografia del contenuto (CEK) casuale di 32 byte ed eseguirà la crittografia envelope dei dati BLOB utilizzando queste informazioni. La CEK con wrapping e alcuni metadati di crittografia aggiuntivi vengono quindi archiviati come metadati BLOB insieme al BLOB crittografato nel servizio.

> [!WARNING]
> Se si modificano o si caricano i propri metadati per il BLOB, è necessario assicurarsi che tali metadati siano conservati. Se si caricano nuovi metadati senza questi metadati, la CEK con wrapping, il vettore di inizializzazione e altri metadati andranno persi e il contenuto del BLOB non potrà mai più essere recuperato.
> 
> 

Il download di un BLOB crittografato comporta il recupero del contenuto dell'intero BLOB usando i metodi di servizio **DownloadTo***/**BlobReadStream**. La CEK con wrapping viene sottoposta a rimozione del wrapping e utilizzata con il vettore di inizializzazione (archiviato come metadati BLOB in questo caso) per restituire i dati decrittografati agli utenti.

Il download di un intervallo arbitrario (metodi**DownloadRange***) nel BLOB crittografato implica la regolazione dell'intervallo fornito dagli utenti per ottenere una piccola quantità di dati aggiuntivi che possono essere utilizzati per decrittografare correttamente l'intervallo richiesto.

Tutti i tipi di BLOB (BLOB in blocchi, BLOB di pagine e BLOB di accodamento) possono essere crittografati/decrittografati con questo schema.

### <a name="queues"></a>Queues
Poiché i messaggi in coda possono essere in qualsiasi formato, la libreria client definisce un formato personalizzato che include il vettore di inizializzazione (IV) e la chiave di crittografia del contenuto crittografato (CEK) nel testo del messaggio.

Durante la crittografia, la libreria client genera un vettore di inizializzazione casuale di 16 byte con una CEK casuale di 32 byte ed esegue la crittografia envelope del testo del messaggio in coda utilizzando queste informazioni. Al messaggio in coda crittografato vengono quindi aggiunti la CEK con wrapping e alcuni metadati di crittografia aggiuntivi. Questo messaggio modificato (illustrato di seguito) viene archiviato nel servizio.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Durante la decrittografia, la chiave con wrapping viene estratta dal messaggio in coda e sottoposta a rimozione del wrapping. Anche il vettore di inizializzazione viene estratto dal messaggio in coda e utilizzato con la chiave senza wrapping per decrittografare i dati del messaggio in coda. Si noti che i metadati di crittografia sono di piccole dimensioni (inferiori a 500 byte), quindi mentre vengono tenuti in considerazione per il limite di 64 KB di un messaggio in coda, l'impatto dovrebbe essere gestibile.

### <a name="tables"></a>Tabelle
La libreria client supporta la crittografia di proprietà di entità per le operazioni di inserimento e sostituzione.

> [!NOTE]
> L’unione non è attualmente supportata. Poiché un subset di proprietà potrebbe essere stato crittografato in precedenza utilizzando una chiave diversa, la semplice unione delle nuove proprietà e l’aggiornamento dei metadati comportano la perdita di dati. L'unione richiede chiamate a servizi aggiuntivi per la lettura dell’entità preesistente dal servizio o l’utilizzo di una nuova chiave per ogni proprietà, entrambe operazioni non idonee per motivi di prestazioni.
> 
> 

La crittografia dei dati della tabella funziona nel modo seguente:  

1. Gli utenti specificano le proprietà che devono essere crittografate.
2. La libreria client genera un vettore di inizializzazione (IV) casuale di 16 byte con una chiave di crittografia del contenuto (CEK) casuale di 32 byte per ogni entità ed esegue la crittografia envelope sulle singole proprietà che devono essere crittografate mediante la derivazione di un nuovo vettore di inizializzazione per ciascuna proprietà. La proprietà di crittografia viene memorizzata come dati binari.
3. La CEK con wrapping e alcuni metadati di crittografia aggiuntivi vengono quindi archiviati come due proprietà riservate aggiuntive. La prima proprietà riservata (_ClientEncryptionMetadata1) è una proprietà stringa che contiene le informazioni su vettore di inizializzazione, versione e chiave con wrapping. La seconda proprietà riservata (_ClientEncryptionMetadata2) è una proprietà binaria che contiene le informazioni sulle proprietà che vengono crittografate. Le informazioni contenute in questa seconda proprietà (_ClientEncryptionMetadata2) sono crittografate.
4. A causa di queste proprietà riservate aggiuntive richieste per la crittografia, gli utenti ora possono avere solo 250 proprietà personalizzate anziché 252. Le dimensioni totali dell'entità devono essere minori di 1 MB.

Si noti che solo le proprietà di stringa possono essere crittografate. Se devono essere crittografati altri tipi di proprietà, essi devono essere convertiti in stringhe. Le stringhe crittografate vengono archiviate nel servizio come proprietà binarie e vengono convertite nuovamente in stringhe dopo la decrittografia.

Per le tabelle, oltre al criterio di crittografia, gli utenti devono specificare le proprietà da crittografare. Questa operazione può essere eseguita specificando un attributo [EncryptProperty] (per le entità POCO che derivano da TableEntity) o un resolver di crittografia nelle opzioni di richiesta. Un resolver di crittografia è un delegato che accetta una chiave di partizione, una chiave di riga e un nome di proprietà e restituisce un valore booleano che indica se tale proprietà deve essere crittografata. Durante la crittografia, la libreria client utilizzerà queste informazioni per decidere se una proprietà deve essere crittografata durante la scrittura in rete. Il delegato fornisce inoltre la possibilità di logica per la modalità di crittografia delle proprietà. (Ad esempio, se X, quindi crittografa la proprietà A; in caso contrario crittografa le proprietà A e B). Si noti che non è necessario fornire queste informazioni durante la lettura o la query su entità.

### <a name="batch-operations"></a>Operazioni batch
Nelle operazioni batch, la stessa KEK verrà utilizzata per tutte le righe nell’operazione batch, perché la libreria client consente un solo oggetto options (e pertanto un criterio/KEK) per ogni operazione batch. Tuttavia, la libreria client genera internamente un nuovo vettore di inizializzazione casuale e una CEK casuale per ogni riga nel batch. Gli utenti possono scegliere anche di crittografare proprietà diverse per ogni operazione nel batch mediante la definizione di questo comportamento nel resolver di crittografia.

### <a name="queries"></a>Query
Per eseguire operazioni di query, è necessario specificare un resolver di chiave in grado di risolvere tutte le chiavi nel set di risultati. Se un'entità inclusa nel risultato della query non può essere risolta in un provider, la libreria client genererà un errore. Per ogni query che esegue le proiezioni del lato server, la libreria client aggiungerà le proprietà dei metadati di crittografia speciali (_ClientEncryptionMetadata1 e ClientEncryptionMetadata2) per impostazione predefinita alle colonne selezionate.

## <a name="azure-key-vault"></a>Insieme di credenziali chiave Azure
L'insieme di credenziali chiave di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Con l'insieme di credenziali chiave di Azure gli utenti possono crittografare chiavi e segreti (ad esempio, chiavi di autenticazione, chiavi dell'account di archiviazione, chiavi di crittografia dati, file PFX e password) usando chiavi protette da moduli di protezione hardware (HSM). Per altre informazioni, vedere [Informazioni sull’insieme di credenziali chiave di Azure](../key-vault/key-vault-whatis.md)

La libreria client di archiviazione utilizza la libreria principale insieme di credenziali chiave per fornire un framework comune in Azure per la gestione delle chiavi. Gli utenti ottengono anche l'ulteriore vantaggio dell'utilizzo della libreria di estensioni dell’insieme di credenziali chiave. La libreria di estensioni fornisce funzionalità utili per provider di chiavi locali e cloud Symmetric/RSA semplici, nonché l'aggregazione e la memorizzazione nella cache.

### <a name="interface-and-dependencies"></a>Interfaccia e dipendenze
Esistono tre pacchetti insieme di credenziali chiave:

* Microsoft.Azure.KeyVault.Core contiene IKey e IKeyResolver. È un pacchetto di piccole dimensioni senza dipendenze. La libreria client di archiviazione per .NET lo definisce come dipendenza.
* Microsoft.Azure.KeyVault contiene il client REST insieme di credenziali di chiave.
* Microsoft.Azure.KeyVault.Extensions contiene codice di estensione che include le implementazioni di algoritmi di crittografia e un RSAKey e un SymmetricKey. Dipende dagli spazi dei nomi Core e KeyVault e fornisce funzionalità per definire un resolver aggregato (quando gli utenti desiderano utilizzare più provider di chiavi) e un resolver di chiavi di caching. Anche se la libreria client di archiviazione non dipende direttamente da questo pacchetto, se gli utenti desiderano utilizzare l’insieme di credenziali chiave di Azure per archiviare le chiavi o utilizzare le estensioni dell'insieme di credenziali chiave per utilizzare i provider di crittografia in locale e cloud, questo pacchetto è necessario.

L’insieme di credenziali chiave è progettato per chiavi master di valore elevato e la soglia di limitazione per ogni insieme di credenziali chiave è progettata considerando questo fattore. Quando si esegue la crittografia lato client con l'insieme di credenziali chiave, il modello preferito consiste nell'utilizzare chiavi master simmetriche archiviate come segreti nell'insieme di credenziali chiave e memorizzate localmente nella cache. Gli utenti devono eseguire le operazioni seguenti:

1. Creare un segreto non in linea e caricarlo nell’insieme di credenziali chiave.
2. Utilizzare l’identificatore di base del segreto come parametro per risolvere la versione corrente del segreto per la crittografia e memorizzare nella cache queste informazioni in locale. Utilizzare CachingKeyResolver per la memorizzazione nella cache; non è previsto che gli utenti implementino la propria logica di memorizzazione nella cache.
3. Utilizzare il resolver di memorizzazione nella cache come input durante la creazione del criterio di crittografia.

Altre informazioni sull'uso dell'insieme di credenziali delle chiavi sono disponibili negli [esempi di codice di crittografia](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

## <a name="best-practices"></a>Procedure consigliate
Il supporto di crittografia è disponibile solo nella libreria client di archiviazione per .NET. Windows Phone e Windows Runtime attualmente non supportano la crittografia.

> [!IMPORTANT]
> Quando si utilizza la crittografia lato client, tenere presente i seguenti aspetti importanti:
> 
> * Durante la lettura o la scrittura di un BLOB crittografato, utilizzare i comandi di caricamento completo dei BLOB e i comandi di download completo o basato sull'intervallo dei BLOB. Evitare di scrivere in un BLOB crittografato usando le operazioni di protocollo, ad esempio Put Block, Put Block List, Write Pages, Clear Pages o Append Block. In caso contrario, si potrebbe danneggiare il BLOB crittografato e renderlo illeggibile.
> * Per le tabelle esiste un vincolo simile. Prestare attenzione a non aggiornare le proprietà crittografate senza aggiornare i metadati di crittografia.
> * Se si impostano i metadati nel BLOB crittografato, si potrebbero sovrascrivere i metadati correlati alla crittografia richiesti per la decrittografia, poiché i metadati di impostazione non sono additivi. Ciò vale anche per gli snapshot; evitare di specificare i metadati durante la creazione di uno snapshot di un BLOB crittografato. Per impostare i metadati, assicurarsi di chiamare il metodo **FetchAttributes** per ottenere i metadati di crittografia correnti ed evitare le scritture simultanee durante l'impostazione dei metadati.
> * Abilitare la proprietà **RequireEncryption** nelle opzioni di richiesta predefinite per gli utenti che dovrebbero lavorare solo con i dati crittografati. Per ulteriori informazioni, vedere di seguito.
> 
> 

## <a name="client-api-interface"></a>API client/interfaccia
Durante la creazione di un oggetto EncryptionPolicy, gli utenti possono specificare solo una chiave (implementazione IKey), solo un resolver (implementazione IKeyResolver) o entrambi. IKey è il tipo di chiave di base che viene identificato utilizzando un identificatore di chiave e che fornisce la logica per wrapping/rimozione del wrapping. IKeyResolver viene utilizzato per risolvere una chiave durante la procedura di decrittografia. Definisce un metodo ResolveKey che restituisce un IKey dato un identificatore di chiave. Ciò fornisce agli utenti la possibilità di scegliere tra più chiavi che vengono gestite in più posizioni.

* Per la crittografia, la chiave viene sempre utilizzata e l'assenza di una chiave genera un errore.
* Per la decrittografia:
  * Se specificato per ottenere la chiave, viene richiamato il resolver di chiave. Se il resolver è specificato, ma non dispone di un mapping per l'identificatore di chiave, viene generato un errore.
  * Se il resolver non è specificato, ma viene specificata una chiave, la chiave viene utilizzata se l’identificatore corrisponde all’identificatore della chiave richiesta. Se l'identificatore non corrisponde, viene generato un errore.

Gli [esempi di crittografia](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) rappresentano uno scenario end-to-end più dettagliato per BLOB, code e tabelle, assieme all'integrazione dell’insieme di credenziali chiave.

### <a name="requireencryption-mode"></a>Modalità RequireEncryption
Gli utenti possono facoltativamente abilitare una modalità operativa quando tutte le operazioni di caricamento e download devono essere crittografate. In questa modalità, i tentativi di caricare dati senza un criterio di crittografia o di scaricare dati non crittografati nel servizio avranno esito negativo nel client. La proprietà **RequireEncryption** dell'oggetto opzioni di richiesta controlla questo comportamento. Se l'applicazione esegue la crittografia di tutti gli oggetti archiviati in Archiviazione di Azure, è possibile impostare la proprietà **RequireEncryption** sulle opzioni di richiesta predefinite per l'oggetto client del servizio. Ad esempio, impostare **CloudBlobClient.DefaultRequestOptions.RequireEncryption** su **true** per richiedere la crittografia di tutte le operazioni di BLOB eseguite tramite l'oggetto client.

### <a name="blob-service-encryption"></a>Crittografia del servizio BLOB
Creare un oggetto **BlobEncryptionPolicy** e impostarlo nelle opzioni di richiesta (per API o a livello di client tramite **DefaultRequestOptions**). Tutto il resto verrà gestito dalla libreria client internamente.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

 // Set the encryption policy on the request options.
 BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

 // Upload the encrypted contents to the blob.
 blob.UploadFromStream(stream, size, null, options, null);

 // Download and decrypt the encrypted contents from the blob.
 MemoryStream outputStream = new MemoryStream();
 blob.DownloadToStream(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Crittografia del servizio di accodamento
Creare un oggetto **QueueEncryptionPolicy** e impostarlo nelle opzioni di richiesta (per API o a livello di client tramite **DefaultRequestOptions**). Tutto il resto verrà gestito dalla libreria client internamente.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

### <a name="table-service-encryption"></a>Crittografia del servizio tabelle
Oltre a creare un criterio di crittografia e a impostarlo nelle opzioni di richiesta, è necessario specificare **EncryptionResolver** in **TableRequestOptions** o impostare l'attributo [EncryptProperty] nell'entità.

#### <a name="using-the-resolver"></a>Utilizzo del resolver

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>Utilizzo di attributi
Come indicato in precedenza, se l'entità implementa TableEntity, le proprietà possono essere dotate dell'attributo [EncryptProperty] invece di specificare **EncryptionResolver**.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Crittografia e prestazioni
Si noti che la crittografia dei dati di archiviazione restituisce un overhead delle prestazioni aggiuntivo. La chiave simmetrica e il vettore devono essere generati, il contenuto stesso deve essere crittografato e metadati aggiuntivi devono essere formattati e caricati. Questo overhead varia a seconda della quantità di dati da crittografare. È consigliabile che i clienti testano sempre le proprie applicazioni per le prestazioni durante lo sviluppo.

## <a name="next-steps"></a>Passaggi successivi
* [Esercitazione: Crittografare e decrittografare i BLOB in Archiviazione di Microsoft Azure tramite l'insieme di credenziali delle chiavi di Azure](storage-encrypt-decrypt-blobs-key-vault.md)
* Scaricare il [pacchetto NuGet per la libreria client di Archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage)
* Scaricare i pacchetti NuGet [Core](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Client](http://www.nuget.org/packages/Microsoft.Azure.KeyVault/) ed [Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) per l'Insieme di credenziali delle chiavi di Azure  
* Vedere la [documentazione sull'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-whatis.md)


<!--HONumber=Nov16_HO3-->


