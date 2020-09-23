---
title: Eseguire l'autenticazione in servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni sui vari modi in cui un'app o un servizio può eseguire l'autenticazione ai servizi di comunicazione.
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 928737608ae3e3e44b352724713a284ff9a45da9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939606"
---
# <a name="authenticate-to-azure-communication-services"></a>Eseguire l'autenticazione in servizi di comunicazione di Azure

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Questo articolo fornisce informazioni sull'autenticazione dei client ai servizi di comunicazione di Azure usando *chiavi di accesso* e *token di accesso utente*. Ogni interazione del client con i servizi di comunicazione di Azure deve essere autenticata.

La tabella seguente descrive le opzioni di autenticazione supportate dalle librerie client dei servizi di comunicazione di Azure:

| Libreria client | Chiave di accesso    | Token di accesso utente |
| -------------- | ------------- | ------------------ |
| Amministrazione | Supportato     | Non supportato      |
| SMS            | Supportato     | Non supportato      |
| Chat           | Non supportato | Supportato          |
| Chiamata        | Non supportato | Supportato          |

Ogni opzione di autorizzazione viene descritta brevemente di seguito:

- Autenticazione della **chiave di accesso** per SMS e operazioni di amministrazione. L'autenticazione della chiave di accesso è adatta per le applicazioni in esecuzione in un ambiente del servizio attendibile. Per eseguire l'autenticazione con una chiave di accesso, un client genera un [codice HMAC (hash-based Method Authentication Code)](https://en.wikipedia.org/wiki/HMAC) e lo include all'interno dell' `Authorization` intestazione di ogni richiesta HTTP. Per altre informazioni, vedere [eseguire l'autenticazione con una chiave di accesso](#authenticate-with-an-access-key).
- Autenticazione del **token di accesso utente** per la chat e la chiamata. I token di accesso utente consentono alle applicazioni client di eseguire l'autenticazione direttamente nei servizi di comunicazione di Azure. Questi token vengono generati in un servizio di provisioning di token sul lato server creato dall'utente. Vengono quindi forniti ai dispositivi client che usano il token per inizializzare la chat e chiamare le librerie client. Per altre informazioni, vedere [eseguire l'autenticazione con un token di accesso utente](#authenticate-with-a-user-access-token).

## <a name="authenticate-with-an-access-key"></a>Eseguire l'autenticazione con una chiave di accesso

L'autenticazione della chiave di accesso usa una chiave privata condivisa per generare un HMAC per ogni richiesta HTTP calcolata usando l'algoritmo SHA256 e lo invia nell' `Authorization` intestazione usando lo `HMAC-SHA256` schema.

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

Le librerie client dei servizi di comunicazione di Azure che usano l'autenticazione della chiave di accesso devono essere inizializzate con la stringa di connessione della risorsa. Se non si usa una libreria client, è possibile generare a livello di codice HMAC usando la chiave di accesso della risorsa. Per altre informazioni sulle stringhe di connessione, vedere la [Guida introduttiva al provisioning delle risorse](../quickstarts/create-communication-resource.md).

### <a name="sign-an-http-request"></a>Firma di una richiesta HTTP

Se non si usa una libreria client per effettuare richieste HTTP alle API REST dei servizi di comunicazione di Azure, è necessario creare codice HMAC per ogni richiesta HTTP. I passaggi seguenti descrivono come costruire l'intestazione di autorizzazione:

1. Specificare il timestamp UTC (Coordinated Universal Time) per la richiesta nell' `x-ms-date` intestazione o nell'intestazione HTTP standard `Date` . Il servizio convalida questo oggetto in modo da proteggersi da determinati attacchi di sicurezza, inclusi gli attacchi di riproduzione.
1. Eseguire l'hashing del corpo della richiesta HTTP usando l'algoritmo SHA256, quindi passarlo, con la richiesta, tramite l' `x-ms-content-sha256` intestazione.
1. Costruire la stringa da firmare concatenando il verbo HTTP (ad esempio `GET` o `PUT` ), il percorso della richiesta HTTP e i valori delle `Date` `Host` `x-ms-content-sha256` intestazioni HTTP e nel formato seguente:
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. Generare una firma HMAC-256 della stringa con codifica UTF-8 creata nel passaggio precedente. Quindi, codificare i risultati come Base64. Si noti che è anche necessario decodificare Base64 della chiave dell'account di archiviazione. Usare il formato seguente (mostrato come pseudo-codice):
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_azure_storage_account_shared_key>)))
    ```
1. Specificare l'intestazione dell'autorizzazione come indicato di seguito:
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    Dove `<hmac-sha256-signature>` è il HMAC calcolato nel passaggio precedente.

## <a name="authenticate-with-a-user-access-token"></a>Eseguire l'autenticazione con un token di accesso utente

I token di accesso utente consentono alle applicazioni client di eseguire l'autenticazione direttamente nei servizi di comunicazione di Azure. A tale scopo, è necessario configurare un servizio attendibile che autentica gli utenti dell'applicazione e rilascia i token di accesso utente con la libreria client di amministrazione. Per ulteriori informazioni sulle considerazioni sull'architettura, vedere la documentazione concettuale sull' [architettura client e server](./client-and-server-architecture.md) .

La `CommunicationClientCredential` classe contiene la logica per fornire le credenziali del token di accesso utente alle librerie client e la gestione del ciclo di vita.

### <a name="initialize-the-client-libraries"></a>Inizializzare le librerie client

Per inizializzare le librerie client dei servizi di comunicazione di Azure che richiedono l'autenticazione basata su token di accesso utente, creare prima un'istanza della `CommunicationClientCredential` classe e quindi usarla per inizializzare un client API.

I frammenti di codice seguenti illustrano come inizializzare la libreria client di chat con un token di accesso utente:

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
var userCredential = new CommunicationUserCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance with the AzureCommunicationUserCredential class
const userCredential = new AzureCommunicationUserCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
let userCredential = try CommunicationUserCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
CommunicationUserCredential userCredential = new CommunicationUserCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>Aggiornamento di token di accesso utente

I token di accesso utente sono credenziali di breve durata che devono essere rilasciate per impedire agli utenti di riscontrare eventuali interferenze del servizio. Il `CommunicationUserCredential` costruttore accetta una funzione di callback di aggiornamento che consente di aggiornare i token di accesso utente prima della scadenza. Usare questo callback per recuperare un nuovo token di accesso utente dal servizio attendibile.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationUserCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationUserCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationUserCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationUserCredential credential = new CommunicationUserCredential(tokenRefresher, token, true);
```
---

L' `refreshProactively` opzione consente di decidere come gestire il ciclo di vita del token. Per impostazione predefinita, quando un token non è aggiornato, il callback bloccherà le richieste API e tenterà di aggiornarlo. Quando `refreshProactively` è impostato su `true` , il callback viene pianificato ed eseguito in modo asincrono prima della scadenza del token.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creazione di token di accesso utente](../quickstarts/access-tokens.md)

Per altre informazioni, vedere gli articoli seguenti:
- [Informazioni sull'architettura di client e server](../concepts/client-and-server-architecture.md)
