---
title: Panoramica delle API .NET Standard di Inoltro di Azure | Microsoft Docs
description: Panoramica dell&quot;API .NET Standard di Inoltro di Azure
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: d1756dee37771941caae781682b342986c7ecbc9
ms.contentlocale: it-it
ms.lasthandoff: 03/27/2017


---

# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Panoramica dell'API .NET Standard per Connessioni ibride di Inoltro di Azure
In questo articolo vengono riepilogate alcune delle principali [API client](/dotnet/api/microsoft.azure.relay) .NET Standard per Connessioni ibride di Inoltro di Azure.
  
## <a name="relay-connection-string-builder"></a>Generatore di stringhe di connessione di Inoltro
La calsse [RelayConnectionStringBuilder][RelayConnectionStringBuilder] genererà stringhe in un formato specifico per Connessioni ibride di Inoltro di Azure. È possibile usarla per verificare il formato di una stringa di connessione o per generarne una da zero. Per un esempio, vedere quanto segue.

```csharp
var endpoint = "{Relay namespace}";
var entityPath = "{Name of the Hybrid Connection}";
var sharedAccessKeyName = "{SAS key name}";
var sharedAccessKey = "{SAS key value}";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

È inoltre possibile passare direttamente una stringa di connessione al metodo `RelayConnectionStringBuilder`. In questo modo sarà possibile verificare che la stringa di connessione è in un formato valido. Il costruttore restituirà un `ArgumentException` in caso di uno o più parametri non validi.

```csharp
var myConnectionString = "{RelayConnectionString}";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Flusso di Connessioni ibride
La classe [HybridConnectionStream][HCStream] è l'oggetto principale usato per inviare e ricevere dati da un endpoint di Inoltro di Azure, sia che si lavori con un [HybridConnectionClient][HCClient] o un [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Ottenere un flusso di Connessioni ibride

#### <a name="listener"></a>Listener
Tramite un [HybridConnectionListener][HCListener] è possibile ottenere un `HybridConnectionStream`, nel modo seguente:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Client
Tramite un [HybridConnectionClient][HCClient] è possibile ottenere un `HybridConnectionStream`, nel modo seguente:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Ricezione di dati
La classe [HybridConnectionStream][HCStream] consente la comunicazione bidirezionale. Nella maggior parte dei casi d'uso, si vorrà avere una ricezione costante dal flusso. Se si legge testo da un flusso, si potrebbe voler usare uno [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) per usufruire di una più semplice analisi dei dati. Ad esempio, è possibile leggere i dati come testo anziché come `byte[]`.

Il codice seguente legge singole righe di testo dal flusso fino a quando non viene richiesto l'annullamento.

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Invio di dati
Dopo aver stabilito una connessione, è possibile inviare un messaggio all'endpoint di Inoltro di Azure. Poiché l'oggetto connessione eredita [Stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), inviare i dati come un `byte[]`. L'esempio seguente illustra come farlo:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Tuttavia, se si desidera inviare testo direttamente e senza dover codificare la stringa ogni volta, è possibile eseguire il wrapping dell'oggetto `hybridConnectionStream` con un oggetto [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx).

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Inoltro di Azure, visitare i collegamenti seguenti:

* [Riferimento su Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md)
* [API di Inoltro disponibili](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener
