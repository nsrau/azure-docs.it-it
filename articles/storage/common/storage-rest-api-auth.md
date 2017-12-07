---
title: Operazioni per chiamare l'API REST di Archiviazione di Azure, inclusa l'autenticazione | Microsoft Docs
description: Operazioni per chiamare l'API REST di Archiviazione di Azure, inclusa l'autenticazione
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/27/2017
ms.author: robinsh
ms.openlocfilehash: 73921f7fd4de65513f647db92b737a79f1043182
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="using-the-azure-storage-rest-api"></a>Uso dell'API REST di Archiviazione di Azure

Questo articolo illustra come usare le API REST del servizio di archiviazione BLOB e come autenticare la chiamata al servizio. L'articolo è stato scritto tenendo conto del punto di vista di uno sviluppatore che non conosce REST e non ha idea di come eseguire una chiamata REST. Non sempre è facile comprendere come trasformare i dati della documentazione di riferimento in un'effettiva chiamata REST, specificando i campi nel modo corretto. Dopo aver appreso come configurare una chiamata REST, sarà possibile sfruttare queste informazioni anche per usare le altre API REST dei servizi di archiviazione.

## <a name="prerequisites"></a>Prerequisiti 

L'applicazione restituisce l'elenco dei contenitori nella risorsa di archiviazione BLOB per un account di archiviazione. Per provare il codice di questo articolo, è necessario quanto segue: 

* Installare [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) con il carico di lavoro seguente:
    - Sviluppo di Azure

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Un account di archiviazione di uso generico. Se non si ha un account di archiviazione, è possibile crearlo usando il [portale di Azure](https://portal.azure.com), [PowerShell](storage-quickstart-create-storage-account-powershell.md) o l'[interfaccia della riga di comando di Azure](storage-quickstart-create-storage-account-cli.md).

* L'esempio riportato in questo articolo illustra come elencare i contenitori di un account di archiviazione. Per visualizzare l'output, aggiungere alcuni contenitori alla risorsa di archiviazione BLOB nell'account di archiviazione prima di iniziare.

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

L'applicazione di esempio è un'applicazione console scritta in C#.

Usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Questo comando consente di duplicare il repository nella cartella locale git. Per aprire la soluzione di Visual Studio, cercare la cartella storage-dotnet-rest-api-with-auth, aprirla e fare doppio clic su StorageRestApiAuth.sln. 

## <a name="why-do-i-need-to-know-rest"></a>Perché è necessario conoscere REST?

La conoscenza dell'uso di REST è una competenza utile. Il team di Azure rilascia spesso nuove funzionalità. In molti casi, queste funzionalità sono accessibili tramite l'interfaccia REST, ma non sono ancora visibili tramite **tutte** le librerie client di archiviazione o l'interfaccia utente, ad esempio il portale di Azure. Per avere sempre a disposizione le funzionalità più recenti e potenti, la conoscenza di REST è un requisito fondamentale. L'API REST è inoltre utile quando si vuole creare una libreria personalizzata per interagire con Archiviazione di Azure o si vuole accedere ad Archiviazione di Azure con un linguaggio di programmazione che non ha un SDK o una libreria client di archiviazione.

## <a name="what-is-rest"></a>Che cos'è REST?

REST è l'acronimo di *representational state transfer*, ovvero trasferimento di stato rappresentativo. Per una definizione dettagliata, consultare [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer).

In pratica, REST è un'architettura che è possibile usare quando si eseguono chiamate alle API o si rendono disponibili le API per le chiamate. Questa architettura è indipendente da ciò che avviene su entrambi i lati della comunicazione e dal software usato per inviare o ricevere le chiamate REST. È possibile scrivere un'applicazione da eseguire su un computer Mac, Windows o Linux, un tablet o un telefono Android, un iPhone, un iPod o un sito Web e usare la stessa API REST per tutte le piattaforme. Quando viene chiamata l'API REST, i dati possono essere passati verso l'interno e/o l'esterno. Per l'API REST non è rilevante la piattaforma che esegue la chiamata, ma sono soprattutto importanti le informazioni passate nella richiesta e i dati forniti nella risposta.

## <a name="heres-the-plan"></a>In cosa consiste il piano

Il progetto di esempio elenca i contenitori in un account di archiviazione. Dopo aver appreso come mettere in relazione le informazioni presenti nella documentazione dell'API REST con il codice effettivo, sarà più facile capire come configurare le altre chiamate REST. 

Consultando l'articolo [Blob Service REST API](/rest/api/storageservices/fileservices/Blob-Service-REST-API) (API REST del servizio BLOB) è possibile esaminare l'elenco di tutte le operazioni che possono essere eseguite sulle risorse di archiviazione BLOB. Le librerie client di archiviazione sono wrapper per le API REST, ovvero consentono di accedere più facilmente alle risorse di archiviazione senza usare direttamente le API REST. Tuttavia, come indicato in precedenza, talvolta può essere necessario usare l'API REST in sostituzione di una libreria client di archiviazione.

## <a name="rest-api-reference-list-containers-api"></a>Informazioni di riferimento sulle API REST: API ListContainers

Esaminando la pagina relativa all'operazione [ListContainers](/rest/api/storageservices/fileservices/List-Containers2) nelle informazioni di riferimento sulle API REST è possibile comprendere il significato di alcuni dei campi presenti nella richiesta e nella risposta riportate nella sezione seguente con il codice.

**Metodo della richiesta**: GET. Questo verbo è il metodo HTTP che si specifica come proprietà dell'oggetto della richiesta. Come altri valori per il verbo è possibile usare HEAD, PUT e DELETE, a seconda dell'API che si intende chiamare.

**URI della richiesta**: https://myaccount.blob.core.windows.net/?comp=list  Questo URI viene creato in base all'endpoint dell'account di archiviazione BLOB `http://myaccount.blob.core.windows.net` e alla stringa di risorsa `/?comp=list`.

[Parametri dell'URI](/rest/api/storageservices/fileservices/List-Containers2#uri-parameters): quando si chiama ListContainers è possibile usare parametri di query aggiuntivi, ad esempio *timeout* per definire il timeout della chiamata (in secondi) e *prefix* per applicare un filtro.

Un altro parametro utile è *maxresults*. Con questo parametro, se sono disponibili più contenitori rispetto al valore specificato, il corpo della risposta conterrà un elemento *NextMarker* che indica il contenitore da cui ripartire nella richiesta successiva. Per usare questa funzionalità, è necessario specificare il valore *NextMarker* come parametro *marker* nell'URI della richiesta successiva. Questa funzionalità è analoga al paging attraverso i risultati. 

Per usare parametri aggiuntivi, specificarli dopo la stringa di risorsa con il relativo valore, come in questo esempio:

```
/?comp=list&timeout=60&maxresults=100
```

[Intestazioni della richiesta](/rest/api/storageservices/fileservices/List-Containers2#request-headers)**:** questa sezione include l'elenco delle intestazioni obbligatorie e facoltative della richiesta. Le intestazioni obbligatorie sono tre: *Authorization*, *x-ms-date* (data e ora UTC della richiesta) e *x-ms-version* (versione dell'API REST da usare). L'intestazione *x-ms-client-request-id* è facoltativa. Per questo campo è possibile impostare un valore qualsiasi, che viene scritto nei log dell'analisi di archiviazione quando la registrazione è abilitata.

[Corpo della richiesta](/rest/api/storageservices/fileservices/List-Containers2#request-body)**:** per ListContainers non è previsto un corpo della richiesta, che viene usato invece per tutte le operazioni PUT, durante il caricamento di BLOB, e per SetContainerAccessPolicy, che consente di inviare un elenco XML di criteri di accesso archiviati da applicare. Per informazioni su questi criteri, vedere l'articolo [Uso delle firme di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md).

[Codice di stato della risposta](/rest/api/storageservices/fileservices/List-Containers2#status-code)**:** indica i codici di stato che è necessario conoscere. In questo esempio, il codice di stato HTTP 200 indica che l'operazione è stata eseguita correttamente. Per un elenco completo di codici di stato HTTP, consultare [Status Code Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (Definizioni dei codici di stato). Per i codici di errore specifici per le API REST di archiviazione, vedere [Common REST API error codes](/rest/api/storageservices/common-rest-api-error-codes) (Codici di errore comuni delle API REST).

[Intestazioni della risposta](/rest/api/storageservices/fileservices/List-Containers2#response-headers)**:** sono incluse *Content Type*, *x-ms-request-id* (ID della richiesta passata, se applicabile), *x-ms-version* (versione del servizio BLOB usato) e *Date* (data e ora UTC in cui è stata eseguita la richiesta).

[Corpo della risposta](/rest/api/storageservices/fileservices/List-Containers2#response-body): questo campo è una struttura XML che contiene i dati richiesti. In questo esempio la risposta è un elenco di contenitori e delle relative proprietà.

## <a name="creating-the-rest-request"></a>Creazione della richiesta REST

Alcune note prima di iniziare. Per garantire la sicurezza, durante l'esecuzione in un ambiente di produzione, usare sempre HTTPS anziché HTTP. Ai fini di questo esercizio, si userà HTTP in modo da poter visualizzare i dati della richiesta e della risposta. Per esaminare le informazioni relative alla richiesta e alla risposta nelle chiamate REST effettive, è possibile scaricare [Fiddler](http://www.telerik.com/fiddler) o un'applicazione simile. Nella soluzione di Visual Studio, il nome e la chiave dell'account di archiviazione sono impostati come hardcoded nella classe e il metodo ListContainersAsyncREST passa il nome e la chiave dell'account di archiviazione ai metodi usati per creare i vari componenti della richiesta REST. In un'applicazione reale, il nome e la chiave dell'account di archiviazione si troveranno in un file di configurazione o in variabili di ambiente oppure verranno recuperati da un'istanza di Azure Key Vault.

In questo progetto di esempio, il codice per creare l'intestazione dell'autorizzazione si trova in una classe separata per consentire allo sviluppatore di prendere l'intera classe e aggiungerla a una soluzione personalizzata "così com'è". Il codice dell'intestazione dell'autorizzazione è utilizzabile per la maggior parte delle chiamate all'API REST in Archiviazione di Azure.

Per creare la richiesta, che è un oggetto HttpRequestMessage, passare a ListContainersAsyncREST in Program.cs e seguire questa procedura: 

* Creare l'URI da usare per la chiamata al servizio. 
* Creare l'oggetto HttpRequestMessage e impostare il payload. Per ListContainersAsyncREST il payload è null perché non viene passato alcun elemento.
* Aggiungere le intestazioni x-ms-date e x-ms-version della richiesta.
* Ottenere l'intestazione dell'autorizzazione e aggiungerla.

Alcune informazioni di base che è necessario conoscere: 

*  Per ListContainers, il **metodo** è `GET`. Questo valore viene impostato quando si crea l'istanza della richiesta. 
*  La **risorsa** è la parte di query dell'URI che indica quale API viene chiamata. Il valore della risorsa è pertanto `/?comp=list`. Come indicato in precedenza, la risorsa è riportata nella pagina della documentazione di riferimento che contiene le informazioni relative all'[API ListContainers](/rest/api/storageservices/fileservices/List-Containers2).
*  L'URI viene creato definendo l'endpoint del servizio BLOB per l'account di archiviazione e concatenando la risorsa. Il valore dell'**URI della richiesta** è quindi `http://contosorest.blob.core.windows.net/?comp=list`.
*  Per ListContainers, **requestBody** è null e non vi sono altre **intestazioni**.

Altre API possono avere parametri diversi da passare, come *ifMatch*, che può ad esempio essere usato per una chiamata a PutBlob. In questo caso, si imposta ifMatch su un eTag e il BLOB viene aggiornato solo se l'eTag specificato corrisponde all'eTag corrente nel BLOB. Se altri utenti hanno aggiornato il BLOB da quando l'eTag è stato recuperato, le loro modifiche non verranno sostituite. 

Per prima cosa, impostare i valori di `uri` e `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Creare quindi un'istanza della richiesta impostando il metodo su `GET` e specificando l'URI.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Aggiungere le intestazioni x-ms-date e x-ms-version della richiesta. In questo punto del codice si aggiungono anche le eventuali altre intestazioni della richiesta necessarie per la chiamata. In questo esempio non sono previste intestazioni aggiuntive. Una delle API che passano intestazioni aggiuntive è SetContainerACL. Per l'archiviazione BLOB, questa API aggiunge un'intestazione denominata "x-ms-blob-public-access" e il valore per il livello di accesso.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-04-17");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Chiamare il metodo che crea l'intestazione dell'autorizzazione e aggiungere il risultato alle intestazioni della richiesta. La procedura per creare l'intestazione dell'autorizzazione è illustrata più avanti in questo articolo. Il nome del metodo è GetAuthorizationHeader, come mostrato in questo frammento di codice:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

A questo punto, `httpRequestMessage` contiene la richiesta REST completa delle intestazioni di autorizzazione. 

## <a name="call-the-rest-api-with-the-request"></a>Chiamare l'API REST con la richiesta

Dopo aver creato la richiesta, è possibile chiamare SendAsync per inviare la richiesta REST. SendAsync chiama l'API e ottiene la risposta. Esaminare il codice di stato della risposta (200 indica che l'operazione è riuscita) e quindi analizzarla. In questo caso, si ottiene un elenco XML di contenitori. A questo punto può essere utile esaminare il codice per chiamare il metodo GetRESTRequest per creare la richiesta, eseguire la richiesta e quindi analizzare la risposta per l'elenco di contenitori.

```csharp 
    // Send the request.
    using (HttpResponseMessage httpResponseMessage = 
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200), 
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Se si usa uno sniffer di rete come [Fiddler](https://www.telerik.com/fiddler) quando si esegue la chiamata a SendAsync, è possibile visualizzare le informazioni della richiesta e della risposta, come descritto di seguito. Il nome dell'account di archiviazione è *contosorest*.

**Richiesta:**

```
GET /?comp=list HTTP/1.1
```

**Intestazioni della richiesta:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Codice di stato e intestazioni della risposta restituiti dopo l'esecuzione:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 04-17
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Corpo della risposta (XML):** per ListContainers, mostra l'elenco dei contenitori e le relative proprietà.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Ora che si è appreso come creare la richiesta, chiamare il servizio e analizzare i risultati, è possibile vedere come creare l'intestazione dell'autorizzazione. La procedura per creare questa intestazione è complessa. Tuttavia, una volta creato il codice funzionante, sarà possibile usarlo per tutte le API REST dei servizi di archiviazione.

## <a name="creating-the-authorization-header"></a>Creazione dell'intestazione dell'autorizzazione

I concetti di base relativi all'autenticazione (senza esempi di codice) sono illustrati nell'articolo [Authentication for the Azure Storage Services](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services) (Autenticazione per i servizi di archiviazione di Azure),
ma ai fini pratici è possibile concentrarsi sulle nozioni fondamentali ed esaminare in dettaglio il codice.

Prima di tutto, usare un'autenticazione con chiave condivisa. L'intestazione dell'autorizzazione ha un formato simile al seguente:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Il campo della firma è un codice HMAC (Hash-based Message Authentication Code) creato dalla richiesta, calcolato con l'algoritmo SHA256 e quindi codificato tramite la codifica Base64. Finora è tutto abbastanza chiaro, ma non si è ancora parlato della *canonizzazione*.

Questo frammento di codice illustra il formato della stringa della firma con chiave condivisa:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

La maggior parte di questi campi viene usata raramente. Per l'archiviazione BLOB, si specificano VERB, md5, Content-Length, CanonicalizedHeaders e CanonicalizedResource. Gli altri campi possono essere lasciati vuoti, ma è necessario specificare `\n` per indicare che sono effettivamente vuoti.

Che significato hanno i campi CanonicalizedHeaders e CanonicalizedResource? Per capirlo, è necessario conoscere il significato della parola "canonizzazione". Questa è la definizione riportata su [Wikipedia](http://en.wikipedia.org/wiki/Canonicalization): *In informatica, la canonizzazione (talvolta detta anche standardizzazione o normalizzazione) è un processo per convertire i dati che hanno più di una rappresentazione possibile in un formato standard, normale o canonico.* In parole semplici, ciò significa prendere un elenco di elementi (come le intestazioni nel caso di CanonicalizedHeaders) e convertirli nel formato standard richiesto. Fondamentalmente, Microsoft ha definito un formato specifico che è necessario rispettare.

Si esamineranno ora i due campi in forma canonica che sono necessari per creare l'intestazione dell'autorizzazione.

**CanonicalizedHeaders**

Per creare questo valore, recuperare le intestazioni che iniziano con "x-ms-", ordinarle e quindi formattarle in una singola stringa di istanze `[key:value\n]` concatenate. Per questo esempio, le intestazioni in forma canonica hanno l'aspetto seguente: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-04-17\n
```

Ecco il codice usato per creare tale output:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
                    where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
                    orderby kvp.Key
                    select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. http://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        StringBuilder headerBuilder = new StringBuilder(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValues in kvp.Value)
        {
            string trimmedValue = headerValues.TrimStart().Replace("\r\n", String.Empty);
            headerBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used 
            //   if there are multiple values for one of the headers.
            separator = ',';
        }
        sb.Append(headerBuilder.ToString()).Append("\n");
    }
    return sb.ToString();
}      
```

**CanonicalizedResource**

Questa parte della stringa relativa alla firma rappresenta l'account di archiviazione a cui fa riferimento la richiesta. Tenere presente che l'URI della richiesta è `<http://contosorest.blob.core.windows.net/?comp=list>`, con il nome dell'account effettivo, in questo caso `contosorest`. In questo esempio viene restituita la stringa seguente:

```
/contosorest/\ncomp:list
```

Se sono stati definiti parametri di query, nella stringa saranno inclusi anche tali parametri. Di seguito è riportato il codice che gestisce anche altri parametri di query, eventualmente con più valori. Tenere presente che il codice deve funzionare per tutte le API REST ed è quindi opportuno includere tutti i possibili elementi, anche se non richiesti dal metodo ListContainers.

```csharp 
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Ora che le stringhe in forma canonica sono impostate, si vedrà come creare effettivamente l'intestazione dell'autorizzazione. Per prima cosa, creare una stringa della firma del messaggio nel formato StringToSign illustrato in precedenza in questo articolo. Questo concetto è più semplice da spiegare mostrando il codice con i commenti. Ecco quindi il metodo finale che restituisce l'intestazione dell'autorizzazione:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

Quando si esegue questo codice, il valore risultante di MessageSignature avrà l'aspetto seguente:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-04-17\n/contosorest/\ncomp:list
```

Ecco infine il valore per l'intestazione dell'autorizzazione:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

L'intestazione dell'autorizzazione è l'ultima intestazione inserita nelle intestazioni della richiesta prima dell'invio della risposta.

A questo punto si hanno tutte le informazioni necessarie, oltre al codice, per creare una classe che consenta di creare una richiesta da usare per chiamare le API REST dei servizi di archiviazione.

## <a name="how-about-another-example"></a>Un altro esempio utile 

In questo esempio si vedrà come modificare il codice per chiamare ListBlobs per il contenitore *container-1*. Il codice è quasi identico a quello usato per elencare i contenitori. Le uniche differenze riguardano l'URI e la modalità analisi della risposta. 

Se si consulta la documentazione di riferimento per [ListBlobs](/rest/api/storageservices/fileservices/List-Blobs), si vedrà che il metodo da usare è *GET* e il valore di RequestURI è il seguente:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

In ListContainersAsyncREST modificare il codice che imposta l'URI sull'API per ListBlobs. Il nome del contenitore è **container-1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Pertanto, nel punto in cui si gestisce la risposta, modificare il codice in modo da eseguire la ricerca di BLOB e non di contenitori.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Quando si esegue questo esempio, si ottengono risultati simili ai seguenti:

**CanonicalizedHeaders:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-04-17\n
```

**CanonicalizedResource:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**MessageSignature:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-04-17\n/contosorest/container-1\ncomp:list\nrestype:container
```

**Intestazione dell'autorizzazione:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

I valori seguenti sono restituiti da [Fiddler](http://www.telerik.com/fiddler):

**Richiesta:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Intestazioni della richiesta:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-04-17
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Codice di stato e intestazioni della risposta restituiti dopo l'esecuzione:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-04-17
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Corpo della risposta (XML):** questa risposta XML mostra l'elenco di BLOB e le relative proprietà. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Riepilogo

In questo articolo si è appreso come creare una richiesta da inviare all'API REST del servizio di archiviazione BLOB per recuperare un elenco di contenitori o un elenco di BLOB all'interno di un contenitore. Si è inoltre appreso come creare la firma di autorizzazione per la chiamata all'API REST, come usarla nella richiesta REST e come esaminare la risposta.

## <a name="next-steps"></a>Passaggi successivi

* [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api) (API REST del servizio BLOB)
* [File Service REST API](/rest/api/storageservices/file-service-rest-api) (API REST del servizio file)
* [Queue Service REST API](/rest/api/storageservices/queue-service-rest-api) (API REST del servizio di accodamento)