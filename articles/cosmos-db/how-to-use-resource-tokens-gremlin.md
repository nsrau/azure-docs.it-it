---
title: Token delle risorse di Azure Cosmos DB con Gremlin
description: Informazioni su come creare token delle risorse e usarli per accedere al database a grafo
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70807012"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>Token delle risorse di Azure Cosmos DB con Gremlin
Questo articolo illustra come usare i [token delle risorse di Cosmos DB](secure-access-to-data.md) per accedere al database a grafo tramite Gremlin SDK.

## <a name="create-a-resource-token"></a>Creare un token delle risorse

TinkerPop Gremlin SDK non dispone di un'API per la creazione di token delle risorse. Il token delle risorse è un concetto di Cosmos DB. Per creare token delle risorse, scaricare [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md). Se l'applicazione deve creare token delle risorse e usarli per accedere al database a grafo, sono necessari due SDK distinti.

Gerarchia del modello a oggetti al di sopra dei token delle risorse:
- **Account Cosmos DB**: entità di primo livello a cui è associato DNS, ad esempio `contoso.gremlin.cosmos.azure.com`
  - **Database Cosmos DB**
    - **Utente**
      - **Autorizzazione**
        - *Token*: proprietà dell'oggetto **autorizzazione** che indica le azioni consentite o negate.

Il formato del token delle risorse è `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`. Questa stringa è opaca per i client e deve essere usata così com'è senza modifica o interpretazione.

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Usare un token delle risorse
I token delle risorse possono essere usati direttamente come proprietà "password" durante la costruzione della classe `GremlinServer`.

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
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

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Limite

Un singolo account Gremlin può rilasciare un numero illimitato di token, ma è possibile usare simultaneamente solo un massimo di **100** token in **un'ora**. Se l'applicazione supera il limite di token all'ora, la richiesta di autenticazione verrà negata con il messaggio di errore `"Exceeded allowed resource token limit of 100 that can be used concurrently"`. È inutile chiudere le connessioni attive con token specifici per liberare slot per i nuovi token. Il motore di database Gremlin di Cosmos DB tiene traccia dei singoli token dell'ultima ora prima della richiesta di autenticazione.

## <a name="permission"></a>Autorizzazione

Un errore comune che si verifica nelle applicazioni quando vengono usati i token delle risorse è `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."`. Questo errore viene restituito quando l'attraversamento di Gremlin tenta di scrivere un arco o un vertice, ma il token delle risorse concede solo autorizzazioni `Read`. Controllare se l'attraversamento contiene uno di questi passaggi: `.addV()`, `.addE()`, `.drop()` o `.property()`.

## <a name="next-steps"></a>Passaggi successivi
* [Controllo degli accessi in base al ruolo](role-based-access-control.md) in Azure Cosmos DB
* [Informazioni su come proteggere l'accesso ai dati](secure-access-to-data.md) in Azure Cosmos DB
