---
title: Uso delle firme di accesso condiviso | Microsoft Docs
description: Informazioni sulla delega dell&quot;accesso alle risorse di archiviazione di Azure, tra cui BLOB, code e tabelle, tramite firme di accesso condiviso (SAS).
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 46fd99d7-36b3-4283-81e3-f214b29f1152
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 12ce6b6bccf3ea2aa2945ddd775716f29cf01e1f
ms.openlocfilehash: c027b8ecbf2941f39056bb696dbcb9c7100cdc6c


---
# <a name="using-shared-access-signatures-sas"></a>Uso delle firme di accesso condiviso
## <a name="overview"></a>Overview
Una firma di accesso condiviso costituisce un potente strumento per concedere ad altri client accesso limitato a BLOB, tabelle e code dell'account di archiviazione, senza dover esporre la chiave dell'account. Nella parte 1 di questa esercitazione sulle firme di accesso condiviso verranno fornite informazioni di carattere generale sul modello della firma di accesso condiviso e ne verranno esaminate le procedure consigliate.

Per altri esempi di codice che usano la firma di accesso condiviso, oltre a quelli già presentati qui, vedere [Introduzione all'archiviazione BLOB di Azure con .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) e altri esempi disponibili nella libreria [Esempi di codice per Azure](https://azure.microsoft.com/documentation/samples/?service=storage). È possibile scaricare le applicazioni di esempio ed eseguirle oppure esaminare il codice in GitHub.

## <a name="what-is-a-shared-access-signature"></a>Informazioni sulle firme di accesso condiviso
Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. Con una firma di accesso condiviso è possibile concedere ai client l'accesso alle risorse nell'account di archiviazione, senza condividere le chiavi dell'account. Questo è il punto chiave associato all'uso delle firme di accesso condiviso nelle applicazioni &mdash; una firma di accesso condiviso rappresenta un metodo sicuro per condividere le risorse di archiviazione senza compromettere le chiavi dell’account.

[!INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

Una firma di accesso condiviso assicura un controllo granulare sul tipo di accesso concesso ai client che dispongono di firma di accesso condiviso, tra cui:

* L'intervallo di validità della firma di accesso condiviso, incluse l'ora di inizio e quella di scadenza.
* Le autorizzazioni concesse dalla firma di accesso condiviso. Ad esempio, una firma di accesso condiviso su un BLOB potrebbe concedere a un utente le autorizzazioni di lettura e di scrittura per questo BLOB, ma non eliminare le autorizzazioni.
* Un indirizzo IP facoltativo o un intervallo di indirizzi IP dai quali Archiviazione di Azure accetterà la firma di accesso condiviso. Ad esempio, è possibile specificare un intervallo di indirizzi IP appartenenti all'organizzazione. Fornisce un'altra misura di sicurezza per la firma di accesso condiviso.
* Il protocollo su cui Archiviazione di Azure accetterà la firma di accesso condiviso. È possibile usare questo parametro facoltativo per limitare l'accesso ai client tramite HTTPS.

## <a name="when-should-you-use-a-shared-access-signature"></a>Perché usare una firma di accesso condiviso
È possibile usare una firma di accesso condiviso quando si desidera fornire l'accesso alle risorse dell'account di archiviazione a un client al quale non si desidera fornire la chiave dell'account. Le chiavi dell'account di archiviazione includono una chiave primaria e una chiave secondaria, che garantiscono entrambi accesso amministrativo all'account e a tutte le risorse in esso presenti. Se si espone una delle chiavi dell'account, è possibile che l'account venga utilizzato in modo dannoso o non appropriato. Le firme di accesso condiviso costituiscono un'alternativa sicura per consentire ad altri client di leggere, scrivere ed eliminare dati nell'account di archiviazione sulla base delle autorizzazioni concesse e senza richiedere la chiave dell'account.

Uno scenario comune in cui la firma di accesso condiviso risulta utile è costituito da un servizio in cui gli utenti leggono e scrivono i propri dati nell'account di archiviazione di un utente specifico. Uno scenario in cui i dati utente vengono archiviati nell'account di archiviazione è caratterizzato da due modelli di progettazione standard:

1\. I client caricano e scaricano dati tramite un servizio proxy front-end che esegue l'autenticazione. Tale servizio presenta il vantaggio di consentire la convalida delle regole di business, tuttavia per grandi quantità di dati o per transazioni con volumi elevati, la creazione di un servizio scalabile in base alla domanda può risultare oneroso o complesso.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\.    Un servizio semplificato autentica il client in base alle necessità e quindi genera una firma di accesso condiviso. Dopo aver ricevuto la firma di accesso condiviso, il client può quindi accedere alle risorse dell'account di archiviazione direttamente con le autorizzazioni definite dalla firma e per l'intervallo consentito dalla firma. La firma di accesso condiviso consente di ridurre la necessità di instradare tutti i dati tramite il servizio proxy front-end.

![sas-storage-provider-service][sas-storage-provider-service]

In molti servizi effettivi è possibile utilizzare una versione ibrida di questi due approcci, a seconda dello scenario interessato, in cui alcuni dati vengono elaborati e convalidati tramite il proxy front-end, mentre altri vengono salvati e/o letti direttamente tramite la firma di accesso condiviso.

Inoltre, è necessario utilizzare una firma di accesso condiviso per autenticare l'oggetto di origine in un'operazione di copia in determinati scenari:

* Quando si copia un BLOB in un altro BLOB che risiede in un account di archiviazione diverso, è necessario utilizzare una firma di accesso condiviso per autenticare il BLOB di origine. Con la versione 2015-04-05 è anche possibile usare una firma di accesso condiviso per autenticare il BLOB di destinazione.
* Quando si copia un file a un altro file che si trova in un account di archiviazione diverso, è necessario utilizzare una firma di accesso condiviso per autenticare il file di origine. Con la versione 2015-04-05 è anche possibile usare una firma di accesso condiviso per autenticare il file di destinazione.
* Quando si copia un BLOB in un file o un file in un BLOB, è necessario utilizzare una firma di accesso condiviso (SAS) per autenticare l'oggetto di origine, anche se gli oggetti di origine e di destinazione si trovano nello stesso account di archiviazione.

## <a name="types-of-shared-access-signatures"></a>Tipi di firme di accesso condiviso
La versione 2015-04-05 di Archiviazione di Azure introduce un nuovo tipo di firma di accesso condiviso, la firma di accesso condiviso dell'account. È ora possibile creare uno dei due tipi di firme di accesso condiviso seguenti:

* **Firma di accesso condiviso dell'account.** La firma di accesso condiviso dell'account delega l'accesso alle risorse in uno o più servizi di archiviazione. Tutte le operazioni disponibili tramite la firma di accesso condiviso del servizio lo sono anche tramite la firma di accesso condiviso dell'account. Con la firma di accesso condiviso dell'account è anche possibile delegare l'accesso a operazioni che si applicano a un servizio specifico, ad esempio **Get/Set Service Properties** e **Get Service Stats**. È anche possibile delegare l'accesso alle operazioni di lettura, scrittura ed eliminazioni in contenitori BLOB, tabelle, code e condivisioni file, che con una firma di accesso condiviso del servizio non è consentito. Per informazioni approfondite su come creare il token di firma di accesso condiviso dell'account, vedere [Creazione di una firma di accesso condiviso dell'account](https://msdn.microsoft.com/library/mt584140.aspx).
* **Firma di accesso condiviso del servizio.** La firma di accesso condiviso del servizio delega l'accesso a una risorsa in uno solo dei servizi di archiviazione, ovvero nel servizio BLOB, nel servizio di accodamento, nel servizio tabelle o nel servizio file. Per informazioni approfondite su come creare il token di firma di accesso condiviso del servizio, vedere [Constructing a Service SAS](https://msdn.microsoft.com/library/dn140255.aspx) (Creare una firma di accesso condiviso del servizio) e [Service SAS Examples](https://msdn.microsoft.com/library/dn140256.aspx) (Esempi di firma di accesso condiviso del servizio).

## <a name="how-a-shared-access-signature-works"></a>Funzionamento della firma di accesso condiviso
Una firma di accesso condiviso è un URI con fimra che punta a una o più risorse di archiviazione e include un token che contiene un set di parametri di query speciale. Il token indica la modalità di accesso alla risorsa da parte del client. Uno dei parametri di query, ovvero la firma, viene creato dai parametri della firma di accesso condiviso e viene firmato con la chiave dell'account. Tale firma viene utilizzata dal servizio di archiviazione di Azure per autenticare la firma di accesso condiviso.

Di seguito è riportato un esempio di un URI di firma di accesso condiviso, che mostra il token di firma di accesso condiviso e l'URI della risorsa:

![sas-storage-uri][sas-storage-uri]

Si noti che il token di firma di accesso condiviso è una stringa generata sul lato client. Per alcuni esempi di codice, vedere la sezione degli [esempi di firma di accesso condiviso](#sas-examples) . Il token di firma di accesso condiviso generato dalla libreria client di archiviazione non viene rilevato da Archiviazione di Azure in alcun modo. È possibile creare un numero illimitato di token di firma di accesso condiviso sul lato client.

Quando un client fornisce un URI di firma di accesso condiviso ad Archiviazione di Azure come parte di una richiesta, il servizio controlla i parametri di firma di accesso condiviso e la firma per verificarne la validità per l'autenticazione della richiesta. Se il servizio conferma che la firma è valida, la richiesta viene autenticata. In caso contrario, la richiesta viene rifiutata con il codice errore 403 (accesso negato).

## <a name="shared-access-signature-parameters"></a>Parametri della firma di accesso condiviso
I token di firma di accesso condiviso dell'account e del servizio includono parametri comuni e accettano alcuni parametri diversi.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Parametri comuni per la firma di accesso condiviso dell'account e del servizio
* **Api version** Parametro facoltativo che specifica la versione del servizio di archiviazione da usare per eseguire la richiesta.
* **Service version** Parametro obbligatorio che specifica la versione del servizio di archiviazione da usare per autenticare la richiesta.
* **Ora di inizio.** Si riferisce all'ora in cui la firma di accesso condiviso diventa valida. L'ora di inizio di una firma di accesso condiviso è facoltativa; se omessa, la firma diventa immediatamente valida. Deve essere espressa in UTC (Coordinated Universal Time), con uno speciale designatore UTC ("Z"), ad esempio 1994-11-05T13:15:30Z.
* **Scadenza.** Si riferisce all'ora dopo la quale la firma di accesso condiviso non è più valida. Secondo le procedure consigliate è preferibile specificare una data di scadenza per una firma di accesso condiviso oppure associarla a criteri di accesso archiviati. Deve essere espressa in UTC (Coordinated Universal Time), con uno speciale designatore UTC ("Z"), ad esempio 1994-11-05T13:15:30Z (vedere più avanti).
* **Autorizzazione.** Le autorizzazioni specificate per la firma di accesso condiviso indicano le autorizzazioni che il client può eseguire sulla risorsa di archiviazione usando la firma. Le autorizzazioni disponibili per la firma di accesso condiviso dell'account e del servizio sono diverse.
* **IP.** Un parametro facoltativo che specifica un indirizzo IP o un intervallo di indirizzi IP all'esterno di Azure (vedere la sezione [Stato di configurazione della sessione di Routing](../expressroute/expressroute-workflows.md#routing-session-configuration-state) per Express route) da cui si desidera accettare le richieste.
* **Protocol.** Parametro facoltativo che specifica il protocollo consentito per una richiesta. I valori possibili sono HTTPS e HTTP (https,http), ovvero il valore predefinito, oppure HTTPS only (https). Si noti che HTTP only non è un valore consentito.
* **Signature.** La firma viene creata dagli altri parametri specificati come parte token e quindi crittografati. Viene quindi usata per autenticare la firma di accesso condiviso.

### <a name="parameters-for-an-account-sas-token"></a>Parametri per il token della firma di accesso condiviso dell'account
* **Servizio o servizi.** La firma di accesso condiviso può delegare l'accesso a uno o più servizi di archiviazione. Ad esempio, è possibile creare una firma di accesso condiviso dell'account che delega l'accesso al servizio BLOB e al servizio File. Oppure, è possibile creare una firma di accesso condiviso che delega l'accesso a tutti e quattro i servizi (BLOB, Coda, Tabelle e File).
* **Tipi di risorse di archiviazione.** Una firma di accesso condiviso dell'account si applica a una o più classi di risorse di archiviazione, piuttosto che a una risorsa specifica. È possibile creare una firma di accesso condiviso dell'account per delegare l'accesso a:
  * API a livello di servizio, che vengono chiamate sulla risorsa dell'account di archiviazione. Ad esempio: **Get/Set Service Properties**, **Get Service Stats** e **List Containers/Queues/Tables/Shares**.
  * API a livello di contenitore, che vengono chiamate sugli oggetti contenitore per ogni servizio, contenitore BLOB, code, tabelle e condivisioni file. Ad esempio: **Create/Delete Container**, **Create/Delete Queue**, **Create/Delete Table**, **Create/Delete Share** e **List Blobs/Files and Directories**.
  * API a livello di oggetto, che vengono chiamate su BLOB, messaggi di coda, entità tabelle e file. Ad esempio: **Put Blob**, **Query Entity**, **Get Messages** e **Create File**.

### <a name="parameters-for-a-service-sas-token"></a>Parametri per il token della firma di accesso condiviso del servizio
* **Risorsa di archiviazione.** Le risorse di archiviazione per cui è possibile delegare l'accesso con una firma di accesso condiviso del servizio sono:
  * Contenitori e BLOB
  * Condivisioni di file e file
  * Queues
  * Le tabelle e gli intervalli di entità della tabella.

## <a name="examples-of-sas-uris"></a>Esempi di URI di firma di accesso condiviso
Di seguito è riportato un esempio di URI di firma di accesso condiviso del servizio che fornisce autorizzazioni di lettura e scrittura a un BLOB. Nella tabella le singole parti dell'URI della firma di accesso condiviso vengono descritte separatamente per facilitarne la comprensione:

    https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

| Nome | Parte firma di accesso condiviso | Descrizione |
| --- | --- | --- |
| URI del BLOB |https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt |Indirizzo del BLOB. Si noti che è vivamente consigliato l'uso di HTTPS. |
| Versione dei servizi di archiviazione |sv=2015-04-05 |Per i servizi di archiviazione della versione 2012-02-12 e successive questo parametro indica la versione da usare. |
| Ora di inizio |st=2015-04-29T22%3A18%3A26Z |Specificata nell'ora UTC. Se si desidera che la firma di accesso condiviso sia immediatamente valida, omettere l'ora di inizio. |
| Scadenza |se=2015-04-30T02%3A23%3A26Z |Specificata nell'ora UTC. |
| Risorsa |sr=b |La risorsa è un BLOB. |
| Autorizzazioni |sp=rw |Le autorizzazioni concesse dalla firma di accesso condiviso includono lettura (r) e scrittura (w). |
| Intervallo IP |sip=168.1.5.60-168.1.5.70 |Intervallo di indirizzi IP da cui verrà accettata una richiesta. |
| Protocol |spr=https |Sono consentite solo richieste tramite HTTPS. |
| Firma |sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D |Utilizzata per autenticare l'accesso al BLOB. La firma è un HMAC calcolato sulla base di una stringa da firmare e della chiave mediante l'algoritmo SHA256, e quindi codificato con la codifica Base64. |

Di seguito è riportato un esempio di firma di accesso condiviso dell'account che usa gli stessi parametri comuni nel token. Poiché questi parametri sono già stati illustrati in precedenza, non sono descritti qui. La tabella seguente include solo i parametri specifici per la firma di accesso condiviso dell'account.

    https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B

| Nome | Parte firma di accesso condiviso | Descrizione |
| --- | --- | --- |
| URI della risorsa |https://myaccount.blob.core.windows.net/?restype=service&comp=properties |Endpoint di servizio BLOB con parametri per ottenere le proprietà del servizio (operazione GET) o per impostare le proprietà del servizio (operazione SET). |
| Servizi |ss=bf |La firma di accesso condiviso si applica ai servizi BLOB e File |
| Tipi di risorse |srt=s |La firma di accesso condiviso si applica alle operazioni a livello di servizio. |
| Autorizzazioni |sp=rw |Le autorizzazioni concedono l'accesso alle operazioni di lettura e scrittura. |

Poiché le autorizzazioni sono limitate a livello di servizio, le operazioni accessibili tramite questa firma di accesso condiviso sono **Get Blob Service Properties** (lettura) e **Set Blob Service Properties** (scrittura). Con un URI di risorsa diverso, lo stesso token di firma di accesso condiviso può tuttavia essere usato per delegare l'accesso all'operazione **Get Blob Service Stats** (lettura).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>Controllo di una firma di accesso condiviso con criteri di accesso archiviati
Una firma di accesso condiviso può assumere una delle due forme seguenti:

* **SAS ad hoc:** quando si crea una firma di accesso condiviso ad hoc, l'ora di inizio, la scadenza e le autorizzazioni vengono tutte specificate nell'URI corrispondente oppure sono implicite, nel caso in cui l'ora di inizio viene omessa. È possibile creare questa firma di accesso condiviso come firma di accesso condiviso dell'account o del servizio.
* **SAS con politica di accesso archiviazione:** i criteri di accesso archiviati vengono definiti per un contenitore di risorse - un contenitore BLOB, una tabella o una coda, e possono essere usati per gestire i vincoli per una o più firme di accesso condiviso. Quando si associa una firma di accesso condiviso a criteri di accesso archiviati, la firma eredita i vincoli, ovvero ora di inizio, scadenza e autorizzazioni, definiti per i criteri di accesso archiviati.

> [!NOTE]
> Al momento, una firma di accesso condiviso dell'account deve essere una firma di accesso condiviso ad-hoc. I criteri di accesso archiviati non sono ancora supportati per la firma di accesso condiviso dell'account.

La differenza tra le due forme è importante un unico scenario chiave, la revoca. Una firma di accesso condiviso è un URL, pertanto chiunque la ottiene può utilizzarla indipendentemente da chi l'ha richiesta per iniziare. Se la firma di accesso condiviso è stata pubblicata e resa pubblica, può essere usata da chiunque in tutto il mondo. Una forma di accesso condiviso rimane valida finché non si verifica una delle quattro condizioni seguenti:

1. Viene raggiunta la scadenza specificata nella firma.
2. Viene raggiunta la scadenza specificata nei criteri di accesso archiviati cui viene fatto riferimento nella firma (se viene fatto riferimento a criteri di accesso archiviati e se questi indicano una scadenza). Tale situazione può verificarsi alla scadenza dell'intervallo oppure perché i criteri di accesso archiviati sono stati modificati in modo che la scadenza ricorra nel passato e ciò corrisponde a un modo per revocare la firma di accesso condiviso.
3. I criteri di accesso archiviati cui viene fatto riferimento nella firma di accesso condiviso vengono eliminati e ciò corrisponde a un altro modo per revocare la firma. Si noti che se si ricreano i criteri di accesso archiviati con lo stesso nome, tutti i token esistenti della firma di accesso condiviso saranno ancora validi in base alle autorizzazioni associate ai criteri (presupponendo che non sia stata superata la scadenza indicata nella firma). Se si intende revocare la firma di accesso condiviso, assicurarsi di usare un nome diverso per ricreare i criteri di accesso archiviati con scadenza nel futuro.
4. La chiave dell'account utilizzata per creare la firma di accesso condiviso viene rigenerata.  Si noti che in seguito a tale operazione tutti i componenti dell'applicazione che utilizzano la chiave dell'account non verranno più autenticati finché non verranno aggiornati in modo da usare l'altra chiave dell'account valida oppure la chiave dell'account appena rigenerata.

> [!IMPORTANT]
> L'URI di una firma di accesso condiviso è associato alla chiave dell'account usata per creare la firma e ai relativi criteri di accesso archiviati (se presenti). Se non sono specificati criteri di accesso archiviati, l'unico modo per revocare una firma di accesso condiviso consiste nel modificare la chiave dell'account.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>Autenticazione da un'applicazione client con una firma di accesso condiviso
Un client che dispone di una firma di accesso condiviso può usare la firma per autenticare una richiesta a un account di archiviazione per il quale non dispone di chiavi dell'account. Una firma di accesso condiviso può essere inclusa in una stringa di connessione o usata direttamente dal metodo o dal costruttore appropriato.

### <a name="using-a-sas-in-a-connection-string"></a>Uso di una firma di accesso condiviso in una stringa di connessione
[!INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Uso di una firma di accesso condiviso in un costruttore o in un metodo
Diversi costruttori di libreria e overload di metodi di archiviazione di Azure offrono un parametro di firma di accesso condiviso affinché sia possibile autenticare una richiesta al servizio con una firma di accesso condiviso.

Ad esempio, in questo caso viene usato un URI di firma di accesso condiviso per creare un riferimento a un BLOB in blocchi. Il provider di firma di accesso condiviso fornisce solo le credenziali necessarie per la richiesta. Il riferimento al BLOB in blocchi viene quindi usato per un'operazione di scrittura:

```csharp
string sasUri = "https://storagesample.blob.core.windows.net/sample-container/" +
    "sampleBlob.txt?sv=2015-07-08&sr=b&sig=39Up9JzHkxhUIhFEjEH9594DJxe7w6cIRCg0V6lCGSo%3D" +
    "&se=2016-10-18T21%3A51%3A37Z&sp=rcw";

CloudBlockBlob blob = new CloudBlockBlob(new Uri(sasUri));

// Create operation: Upload a blob with the specified name to the container.
// If the blob does not exist, it will be created. If it does exist, it will be overwritten.
try
{
    MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    msWrite.Position = 0;
    using (msWrite)
    {
        await blob.UploadFromStreamAsync(msWrite);
    }

    Console.WriteLine("Create operation succeeded for SAS {0}", sasUri);
    Console.WriteLine();
}
catch (StorageException e)
{
    if (e.RequestInformation.HttpStatusCode == 403)
    {
        Console.WriteLine("Create operation failed for SAS {0}", sasUri);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    else
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}

```

## <a name="best-practices-for-using-sas"></a>Procedure consigliate per l'uso di una firma di accesso condiviso
Quando si utilizzano le firme di accesso condiviso nell'applicazione, è necessario essere consapevoli di due rischi potenziali:

* In caso di diffusione di una firma di accesso condiviso, chiunque la ottiene può usarla e questo potrebbe compromettere l'account di archiviazione.
* Se una firma di accesso condiviso fornita a un'applicazione client scade e l'applicazione non è in grado di recuperarne una nuova dal servizio, è possibile che le funzionalità dell'applicazione potrebbero risentirne.

Per bilanciare questi rischi, è consigliabile attenersi ai consigli seguenti relativi all'utilizzo di firme di accesso condiviso:

1. **Usare sempre HTTPS** per creare o distribuire una firma di accesso condiviso.  Se una firma di accesso condiviso passata tramite HTTP viene intercettata, un utente malintenzionato che esegue un attacco man-in-the-middle sarà in grado di leggere la firma e quindi di utilizzarla come se fosse l'utente cui è destinata e questo potrebbe comportare la compromissione di dati sensibili o il danneggiamento dei dati da parte dell'utente malintenzionato.
2. **Laddove possibile fare riferimento a criteri di accesso archiviati.** I criteri di accesso archiviati consentono di revocare le autorizzazioni senza rigenerare le chiavi dell'account di archiviazione.  Impostare la scadenza di questi criteri su un tempo molto lungo (o infinito) e assicurarsi che venga aggiornata regolarmente in modo che ricorra nel futuro.
3. **Usare scadenze a breve termine per una firma di accesso condiviso ad hoc.** In questo modo, anche se una firma di accesso condiviso viene compromessa inconsapevolmente, sarà valida solo per un breve periodo. Questo consiglio è particolarmente importante se non è possibile fare riferimento a criteri di accesso archiviati e consente inoltre di limitare la quantità di dati che è possibile scrivere in un BLOB riducendo il tempo disponibile per il caricamento.
4. **Se necessario, chiedere ai client di rinnovare automaticamente la firma di accesso condiviso.** È consigliabile rinnovare la firma di accesso condiviso nei client prima della scadenza in modo da avere la possibilità di effettuare altri tentativi qualora il servizio che fornisce la firma di accesso condiviso non sia disponibile.  Se la firma di accesso condiviso è destinata a essere utilizzata per poche operazioni immediate e di breve durata da completare entro la data di scadenza, il rinnovo della firma di accesso condiviso non è necessario.  Se tuttavia si dispone di client che effettuano normalmente richieste tramite la firma di accesso condiviso, è necessario considerare la possibilità che la firma scada.  In questo caso è essenziale trovare un punto di equilibrio tale da garantire che la firma di accesso condiviso sia di breve durata (come indicato in precedenza) e che il client richieda il rinnovo in tempo utile per evitare malfunzionamenti causati dalla scadenza della firma prima del rinnovo.
5. **Prestare attenzione all'ora di inizio della firma di accesso condiviso.** Se si imposta l'ora di inizio della firma di accesso condiviso su **ora**, a causa dello sfasamento di orario, ovvero delle differenze dell'ora corrente a seconda del computer in uso, potrebbero verificarsi problemi intermittenti nei primi minuti.  In generale, impostare l'ora di inizio in modo che risalga ad almeno 15 minuti prima oppure non impostarla affatto, in modo che la firma diventi immediatamente valida in qualsiasi caso.  Le stesse considerazioni sono valide anche per la scadenza, pertanto è consigliabile osservare fino a 15 minuti di sfasamento di orario in entrambe le direzioni di qualsiasi richiesta.  Si noti che per i client che usano una versione di REST precedente alla 2012-02-12, la durata massima della firma di accesso condiviso che non fa riferimento a criteri di accesso archiviati è di 1 ora, pertanto tutti i criteri di durata maggiore non saranno validi.
6. **Indicare in modo specifico la risorsa cui accedere.** La procedura consigliata di sicurezza standard consiste nel fornire a un utente i privilegi minimi necessari.  Se un utente necessita solo dell'accesso in lettura a una singola entità, concedere solo tale tipo di accesso per tale entità e non l'accesso in lettura/scrittura/eliminazione per tutte le entità.  Questo contribuirà a ridurre la minaccia di compromissione della firma di accesso condiviso in quanto la firma è meno potente nelle mani di un utente malintenzionato.
7. **Comprendere che all'account verrà fatturato qualsiasi tipo di utilizzo, incluso quello effettuato con la firma di accesso condiviso.** Se si fornisce accesso in scrittura a un BLOB, un utente potrebbe scegliere di caricare un BLOB da 200 GB.  Se si offre anche l'accesso in lettura, l'utente potrebbe scegliere di scaricarlo 10 volte e ciò potrebbe comportare 2 TB di costi in uscita.  Anche in questo caso, fornire autorizzazioni limitate per ridurre l'impatto potenziale di utenti malintenzionati.  Per ridurre questa minaccia, usare firme di accesso condiviso di breve durata, prestando però attenzione allo sfasamento di orario per la scadenza.
8. **Convalidare i dati scritti tramite la firma di accesso condiviso.** Quando un'applicazione client scrive i dati nell'account di archiviazione, tenere presente che tali dati potrebbero causare problemi. Se l'applicazione richiede che i dati vengano convalidati o autorizzati prima dell'uso, è necessario eseguire la convalida dopo la scrittura dei dati e prima che vengano utilizzati dall'applicazione. Questa procedura consente inoltre di evitare la scrittura di dati danneggiati o dannosi nell'account da parte da un utente che ha acquisito correttamente la firma di accesso condiviso o di un utente che sfrutta una firma diffusa per errore.
9. **Non usare sempre SAS.** Talvolta i rischi associati a una particolare operazione su un account di archiviazione superano i benefici derivanti dall'uso della firma di accesso condiviso.  Per tali operazioni creare un servizio di livello intermedio che effettui operazioni di scrittura nell'account di archiviazione dopo autenticazione, controllo e convalida di regole di business. Talvolta è inoltre più semplice gestire l'accesso in modi diversi. Se ad esempio si desidera rendere pubblicamente leggibili tutti i BLOB di un contenitore, è possibile rendere pubblico il contenitore anziché fornire una firma di accesso condiviso a ogni client per consentire l'accesso.
10. **Usare Analisi archiviazione per monitorare l'applicazione.** È possibile usare la registrazione e la metrica per osservare eventuali picchi di errori di autenticazione causati da un'interruzione del servizio del provider di firme di accesso condiviso oppure dalla rimozione accidentale di criteri di accesso archiviati. Per altre informazioni, vedere il [blog del team del servizio di archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) .

## <a name="sas-examples"></a>Esempi di firma di accesso condiviso
Di seguito sono riportati alcuni esempi di entrambi i tipi di firma di accesso condiviso, dell'account e del servizio.

Per eseguire questi esempi, sarà necessario scaricare e fare riferimento a questi pacchetti:

* [Azure Storage Client Library for .NET](http://www.nuget.org/packages/WindowsAzure.Storage), versione 6.x o versione successiva (per usare l'account SAS).
* [Gestione configurazione di Azure](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

Per altri esempi che illustrano come creare e testare una firma di accesso condiviso, vedere [Esempi di codice per Azure per Archiviazione](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Esempio: Creare e usare una firma di accesso condiviso dell'account
L'esempio di codice seguente crea una firma di accesso condiviso valida per i servizi BLOB e File e concede al client le autorizzazioni di lettura, scrittura ed elenco per accedere alle API a livello di servizio. Per la firma di accesso condiviso dell'account il protocollo è limitato ad HTTPS, pertanto è necessario creare una richiesta HTTPS.

```csharp
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
```

Per usare la firma di accesso condiviso dell'account per accedere alle API a livello di servizio per il servizio BLOB, creare un oggetto client BLOB usando la firma di accesso condiviso e l'endpoint account di archiviazione per l'account di archiviazione corrente.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "account-name", endpointSuffix: null, useHttps: true);
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
```

### <a name="example-create-a-stored-access-policy"></a>Esempio: Creare criteri di accesso archiviati
Il codice seguente crea un criterio di accesso archiviato su un contenitore. È possibile usare i criteri di accesso per specificare i vincoli per una firma di accesso condiviso del servizio sul contenitore o i relativi BLOB.

```csharp
private static async Task CreateSharedAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new shared access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
        // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

### <a name="example-create-a-service-sas-on-a-container"></a>Esempio: Creare una firma di accesso condiviso del servizio su un contenitore
Il codice seguente crea una firma di accesso condiviso su un contenitore. Se viene specificato il nome di un criterio di accesso archiviato esistente, tale criterio è associato alla firma di accesso condiviso. Se non viene specificato alcun criterio di accesso archiviato, il codice crea una firma di accesso condiviso ad hoc sul contenitore.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad-hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

### <a name="example-create-a-service-sas-on-a-blob"></a>Esempio: Creare una firma di accesso condiviso del servizio su un BLOB
Il codice seguente crea una firma di accesso condiviso su un BLOB. Se viene specificato il nome di un criterio di accesso archiviato esistente, tale criterio è associato alla firma di accesso condiviso. Se non viene specificato alcun criterio di accesso archiviato, il codice crea una firma di accesso condiviso ad hoc sul BLOB.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```


## <a name="conclusion"></a>Conclusioni
Le firme di accesso condiviso sono utili per offrire autorizzazioni limitate all'account di archiviazione ai client ai quali non si desidera fornire la chiave dell'account.  In quanto tali, costituiscono una parte essenziale del modello di sicurezza di qualsiasi applicazione che utilizza il servizio di archiviazione di Azure.  Il rispetto delle procedure consigliate sopra elencate consente di usare la firma di accesso condiviso per offrire accesso ancor più flessibile alle risorse dell'account di archiviazione senza compromettere la sicurezza dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Archiviazione file di Azure in Windows](storage-dotnet-how-to-use-files.md)
* [Gestire l'accesso in lettura anonimo a contenitori e BLOB](storage-manage-access-to-resources.md)
* [Delega dell'accesso con una firma di accesso condiviso](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Introduzione alla firma di accesso condiviso per tabelle e code](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png
[sas-storage-uri]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png



<!--HONumber=Dec16_HO2-->


