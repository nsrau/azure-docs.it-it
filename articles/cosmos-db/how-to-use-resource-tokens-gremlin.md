---
title: Usare token di risorsa di Azure Cosmos DB con Gremlin SDK
description: Informazioni su come creare token di risorsa e usarli per accedere al database a grafo.
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: 6364bd0f762647b5fe9567ed40042a5ad81f97c1
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105025"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Usare token di risorsa di Azure Cosmos DB con Gremlin SDK

Questo articolo illustra come usare i [token di risorsa di Azure Cosmos DB](secure-access-to-data.md) per accedere al database a grafo tramite Gremlin SDK.

## <a name="create-a-resource-token"></a>Creare un token delle risorse

Apache TinkerPop Gremlin SDK non include un'API utilizzabile per creare token di risorsa. Il termine *token di risorsa* è un concetto di Azure Cosmos DB. Per creare token di risorsa, scaricare l'[SDK di Azure Cosmos DB](sql-api-sdk-dotnet.md). Se l'applicazione deve creare token di risorsa e usarli per accedere al database a grafo, sono necessari due SDK distinti.

Di seguito è illustrata la gerarchia del modello a oggetti sopra i token di risorsa.

- **Account Azure Cosmos DB**: entità di primo livello a cui è associato un DNS, ad esempio `contoso.gremlin.cosmos.azure.com`.
  - **Database di Azure Cosmos DB**
    - **Utente**
      - **Autorizzazione**
        - **Token**: proprietà dell'oggetto autorizzazione che indica le azioni consentite o negate.

Un token di risorsa usa il formato seguente: `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`. Questa stringa è opaca per i client e deve essere usata così com'è, senza alcuna modifica o interpretazione.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Usare un token delle risorse
È possibile usare i token di risorsa direttamente come proprietà "password" durante la costruzione della classe GremlinServer.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a master key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Lo stesso approccio funziona in tutti i TinkerPop Gremlin SDK.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Limite

Con un singolo account Gremlin si può rilasciare un numero illimitato di token. È tuttavia possibile usare solo un massimo di 100 token contemporaneamente in 1 ora. Se un'applicazione supera il limite di token all'ora, una richiesta di autenticazione viene negata e viene visualizzato un messaggio di errore che informa che è stato superato il limite consentito di 100 token di risorsa utilizzabili contemporaneamente. È inutile chiudere le connessioni attive che usano token specifici per liberare slot per i nuovi token. Il motore di database Gremlin di Azure Cosmos DB tiene traccia dei token univoci nell'ora immediatamente precedente alla richiesta di autenticazione.

## <a name="permission"></a>Autorizzazione

Un errore comune che si verifica nelle applicazioni durante l'uso dei token di risorsa è costituito da autorizzazioni insufficienti nell'intestazione dell'autorizzazione per la richiesta corrispondente. Viene quindi indicato di riprovare con un'altra intestazione dell'autorizzazione. Questo errore viene restituito quando un attraversamento di Gremlin tenta di scrivere un arco o un vertice ma il token di risorsa concede solo autorizzazioni *Read*. Controllare l'attraversamento per verificare se contiene uno dei passaggi seguenti: *.addV()* , *.addE()* , *.drop()* o *.property()* .

## <a name="next-steps"></a>Passaggi successivi
* [Controllo degli accessi in base al ruolo](role-based-access-control.md) in Azure Cosmos DB
* [Informazioni su come proteggere l'accesso ai dati](secure-access-to-data.md) in Azure Cosmos DB
