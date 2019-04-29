---
title: Come scalare con più istanze per il servizio Azure SignalR
description: In molti scenari di ridimensionamento, customer spesso deve effettuare il provisioning di più istanze e configurare per usarli insieme, per creare una distribuzione su larga scala. Ad esempio, partizionamento orizzontale richiede che il supporto di più istanze.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809160"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Come ridimensionare SignalR Service con più istanze?
il SDK più recente del servizio SignalR supporta più endpoint per le istanze di SignalR Service. È possibile usare questa funzionalità per ridimensionare le connessioni simultanee, o usarlo per la messaggistica tra diverse aree.

## <a name="for-aspnet-core"></a>Per ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Come aggiungere più endpoint dalla configurazione?

Configurazione con chiave `Azure:SignalR:ConnectionString` o `Azure:SignalR:ConnectionString:` per stringa di connessione SignalR Service.

Se la chiave inizia con `Azure:SignalR:ConnectionString:`, deve essere nel formato `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, dove `Name` e `EndpointType` sono proprietà del `ServiceEndpoint` oggetti e sono accessibili dal codice.

È possibile aggiungere più stringhe di connessione di istanza utilizzando la seguente query `dotnet` comandi:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Come è possibile aggiungere più endpoint dal codice?

Oggetto `ServicEndpoint` classe è stata introdotta per descrivere le proprietà di un endpoint di servizio Azure SignalR.
È possibile configurare più endpoint di istanza quando si usa il SDK del servizio Azure SignalR tramite:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Come personalizzare router endpoint?

Per impostazione predefinita, il SDK Usa il [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) per prelevare gli endpoint.

#### <a name="default-behavior"></a>Comportamento predefinito 
1. Il routing delle richieste client

    Quando si client `/negotiate` con il server di app. Per impostazione predefinita, il SDK **seleziona casualmente** un endpoint del set di endpoint del servizio disponibili.

2. Routing dei messaggi di server

    Quando * l'invio di messaggi a uno specifico * * connessione * * * e la connessione di destinazione sia indirizzato al server corrente, il messaggio viene inviato direttamente a tale endpoint connessi. In caso contrario, i messaggi di trasmissione per ogni endpoint di Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Personalizzare l'algoritmo di routing
Quando si dispone di conoscenze specializzate per identificare quali endpoint devono passare i messaggi a, è possibile creare il proprio router.

Un router personalizzato viene definito di seguito come esempio quando i gruppi a partire `east-` sempre accedere all'endpoint denominato `east`:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Un altro esempio riportato di seguito, che sostituisce il valore predefinito negotiate comportamento, per selezionare gli endpoint dipende da dove si trova il server di app.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Non dimenticare di registrare il router per l'inserimento delle dipendenze contenitore usando:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>Per ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Come aggiungere più endpoint dalla configurazione?

Configurazione con chiave `Azure:SignalR:ConnectionString` o `Azure:SignalR:ConnectionString:` per stringa di connessione SignalR Service.

Se la chiave inizia con `Azure:SignalR:ConnectionString:`, deve essere nel formato `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, dove `Name` e `EndpointType` sono proprietà del `ServiceEndpoint` oggetti e sono accessibili dal codice.

È possibile aggiungere più stringhe di connessione di istanza per `web.config`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Come è possibile aggiungere più endpoint dal codice?

Oggetto `ServicEndpoint` classe è stata introdotta per descrivere le proprietà di un endpoint di servizio Azure SignalR.
È possibile configurare più endpoint di istanza quando si usa il SDK del servizio Azure SignalR tramite:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Come personalizzare router?

L'unica differenza tra ASP.NET SignalR e ASP.NET Core SignalR è il tipo di contesto http per `GetNegotiateEndpoint`. ASP.NET SignalR, sia espresso [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) tipo.

Di seguito è personalizzato la negoziazione esempio di ASP.NET SignalR:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Non dimenticare di registrare il router per l'inserimento delle dipendenze contenitore usando:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Configurazione in scenari tra più aree

Il `ServiceEndpoint` oggetto ha un `EndpointType` proprietà con valore `primary` o `secondary`.

`primary` gli endpoint sono endpoint preferito per ricevere il traffico del client e dispongono di connessioni di rete più affidabili. `secondary` endpoint vengono considerati meno affidabile connessioni di rete e vengono usati solo per server prendendo al traffico client, ad esempio, la trasmissione dei messaggi, non per client di creazione per il traffico del server.

In casi tra più aree, rete può essere instabile. Per il server di un'app che si trova *Stati Uniti orientali*, l'endpoint di SignalR Service che si trova nella stessa *Stati Uniti orientali* area può essere configurata come `primary` e gli endpoint in altre aree contrassegnate come `secondary`. In questa configurazione, gli endpoint di servizio in altre aree possono **ricevere** messaggi da questa *Stati Uniti orientali* server app, ma non è presente alcun **tra aree** client indirizzato a Questo server di app. L'architettura è illustrata nel diagramma seguente:

![Cross-Geo Capabilities Infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Quando un client tenta `/negotiate` con il server di app, con il router predefinito, SDK **ne seleziona casualmente** un endpoint del set di disponibilità `primary` gli endpoint. Quando l'endpoint è disponibile, quindi SDK **seleziona casualmente** da tutti disponibili `secondary` gli endpoint. L'endpoint viene contrassegnato come **disponibili** quando la connessione tra server e l'endpoint del servizio è attiva.

Nello scenario tra più aree, quando un client tenta `/negotiate` con il server di app ospitato in *Stati Uniti orientali*, da default viene sempre restituisce il `primary` endpoint che si trova nella stessa area. Quando tutti i *Stati Uniti orientali* endpoint non sono disponibili, il client viene reindirizzato agli endpoint in altre aree. Effettuare il failover sezione riportata di seguito viene descritto lo scenario in dettaglio.

![Negotiate normale](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Effettuare il failover

Quando tutti i `primary` gli endpoint non sono disponibili, del client `/negotiate` sceglie dai contatori `secondary` gli endpoint. Questo meccanismo di failover richiede che ogni endpoint deve essere usata come `primary` endpoint al server di almeno un'app.

![Effettuare il failover](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Passaggi successivi

In questa Guida, si è appreso come configurare più istanze nella stessa applicazione per scenari tra più aree, partizionamento e ridimensionamento.

Supporta più l'endpoint è anche utilizzabile negli scenari di ripristino di emergenza e disponibilità elevati.

> [!div class="nextstepaction"]
> [Il programma di installazione SignalR Service per il ripristino di emergenza e disponibilità elevata](./signalr-concept-disaster-recovery.md)
