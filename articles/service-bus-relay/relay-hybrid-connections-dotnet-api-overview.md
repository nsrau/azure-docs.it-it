---
title: Panoramica delle API .NET Standard di Inoltro di Azure | Microsoft Docs
description: Questo articolo riepiloga alcuni dei principali Cenni preliminari sull'inoltro di Azure Connessioni ibride .NET Standard API.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 18eaf2d2daae817107be6cdb0da9359bb5f9b4e9
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514536"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Panoramica dell'API .NET Standard per Connessioni ibride di Inoltro di Azure

In questo articolo vengono riepilogate alcune delle principali [API client](/dotnet/api/microsoft.azure.relay) .NET Standard per Connessioni ibride di Inoltro di Azure.
  
## <a name="relay-connection-string-builder-class"></a>Classe RelayConnectionStringBuilder

La classe [RelayConnectionStringBuilder][RelayConnectionStringBuilder] formatta le stringhe di connessione specifiche per l'inoltro connessioni ibride. È possibile usarla per verificare il formato di una stringa di connessione o per generarne una da zero. Per un esempio, vedere il codice seguente.

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

È inoltre possibile passare direttamente una stringa di connessione al metodo `RelayConnectionStringBuilder`. Questa operazione consente di verificare che il formato della stringa di connessione sia valido. Se uno dei parametri non è valido, il costruttore genera un'eccezione `ArgumentException`.

```csharp
var myConnectionString = "[RelayConnectionString]";
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

## <a name="hybrid-connection-stream"></a>Flusso di connessione ibrida

La classe [HybridConnectionStream][HCStream] è l'oggetto primario usato per inviare e ricevere dati da un endpoint di inoltro di Azure, indipendentemente dal fatto che si stia lavorando a un [HybridConnectionClient][HCClient]o a un [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Ottenere un flusso di connessione ibrida

#### <a name="listener"></a>Listener

Utilizzando un oggetto [HybridConnectionListener][HCListener] , è possibile ottenere un oggetto `HybridConnectionStream` come segue:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Client

Utilizzando un oggetto [HybridConnectionClient][HCClient] , è possibile ottenere un oggetto `HybridConnectionStream` come segue:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Ricezione di dati

La classe [HybridConnectionStream][HCStream] consente la comunicazione bidirezionale. Nella maggior parte dei casi si ha una ricezione costante dal flusso. Se si legge il testo dal flusso, è anche possibile usare un oggetto [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) per usufruire di una più semplice analisi dei dati. Ad esempio, è possibile leggere i dati come testo anziché come `byte[]`.

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

Per altre informazioni sul servizio di inoltro di Azure, vedere i collegamenti seguenti:

* [Riferimento su Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md)
* [API di inoltro disponibili](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener