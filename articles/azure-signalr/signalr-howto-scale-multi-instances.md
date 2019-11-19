---
title: Scalabilità con più istanze-servizio Azure SignalR
description: In molti scenari di scalabilità, il cliente deve spesso eseguire il provisioning di più istanze e configurare per usarle insieme, per creare una distribuzione su larga scala. Il partizionamento orizzontale, ad esempio, richiede supporto per più istanze.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158169"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Come scalare il servizio SignalR con più istanze?
L'SDK del servizio SignalR più recente supporta più endpoint per le istanze del servizio SignalR. È possibile usare questa funzionalità per ridimensionare le connessioni simultanee o usarle per la messaggistica tra più aree.

## <a name="for-aspnet-core"></a>Per ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Come aggiungere più endpoint dalla configurazione

Config con `Azure:SignalR:ConnectionString` chiave o `Azure:SignalR:ConnectionString:` per la stringa di connessione del servizio SignalR.

Se la chiave inizia con `Azure:SignalR:ConnectionString:`, deve essere nel formato `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, in cui `Name` e `EndpointType` sono proprietà dell'oggetto `ServiceEndpoint` e sono accessibili dal codice.

È possibile aggiungere più stringhe di connessione di istanza usando i comandi di `dotnet` seguenti:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Come aggiungere più endpoint dal codice?

Viene introdotta una classe `ServicEndpoint` per descrivere le proprietà di un endpoint del servizio Azure SignalR.
È possibile configurare più endpoint di istanza quando si usa Azure SignalR Service SDK tramite:
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

### <a name="how-to-customize-endpoint-router"></a>Come personalizzare il router endpoint

Per impostazione predefinita, l'SDK USA [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) per selezionare gli endpoint.

#### <a name="default-behavior"></a>Comportamento predefinito 
1. Routing richieste client

    Quando il client `/negotiate` con il server app. Per impostazione predefinita, l'SDK **Seleziona in modo casuale** un endpoint dal set di endpoint di servizio disponibili.

2. Routing messaggi server

    Quando * invia un messaggio a una * * connessione specifica * * * e la connessione di destinazione viene indirizzata al server corrente, il messaggio passa direttamente all'endpoint connesso. In caso contrario, i messaggi vengono trasmessi a ogni endpoint di Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Personalizzare l'algoritmo di routing
È possibile creare un router personalizzato quando si dispone di una conoscenza speciale per identificare gli endpoint ai quali i messaggi devono accedere.

Un router personalizzato viene definito di seguito come esempio quando i gruppi che iniziano con `east-` vengono sempre indirizzati all'endpoint denominato `east`:

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

Un altro esempio seguente, che esegue l'override del comportamento di negoziazione predefinito, per selezionare gli endpoint dipende dalla posizione in cui si trova il server app.

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

Non dimenticare di registrare il router nel contenitore DI inserimento delle dipendenze usando:

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

### <a name="how-to-add-multiple-endpoints-from-config"></a>Come aggiungere più endpoint dalla configurazione

Config con `Azure:SignalR:ConnectionString` chiave o `Azure:SignalR:ConnectionString:` per la stringa di connessione del servizio SignalR.

Se la chiave inizia con `Azure:SignalR:ConnectionString:`, deve essere nel formato `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, in cui `Name` e `EndpointType` sono proprietà dell'oggetto `ServiceEndpoint` e sono accessibili dal codice.

È possibile aggiungere più stringhe di connessione di istanza a `web.config`:

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

### <a name="how-to-add-multiple-endpoints-from-code"></a>Come aggiungere più endpoint dal codice?

Viene introdotta una classe `ServicEndpoint` per descrivere le proprietà di un endpoint del servizio Azure SignalR.
È possibile configurare più endpoint di istanza quando si usa Azure SignalR Service SDK tramite:

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

### <a name="how-to-customize-router"></a>Come personalizzare il router

L'unica differenza tra ASP.NET SignalR e ASP.NET Core SignalR è il tipo di contesto HTTP per `GetNegotiateEndpoint`. Per ASP.NET SignalR, è di tipo [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) .

Di seguito è riportato l'esempio di negoziazione personalizzata per ASP.NET SignalR:

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

Non dimenticare di registrare il router nel contenitore DI inserimento delle dipendenze usando:

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

## <a name="configuration-in-cross-region-scenarios"></a>Configurazione negli scenari tra più aree

L'oggetto `ServiceEndpoint` dispone di una proprietà `EndpointType` con valore `primary` o `secondary`.

gli endpoint `primary` sono endpoint preferiti per la ricezione del traffico client e sono considerati con connessioni di rete più affidabili. gli endpoint `secondary` sono considerati con connessioni di rete meno affidabili e vengono utilizzati solo per l'esecuzione del traffico da server a client, ad esempio per la trasmissione di messaggi, non per l'esecuzione del traffico da client a server.

Nei casi tra aree, la rete può essere instabile. Per un server app che si trova negli *Stati Uniti orientali*, l'endpoint del servizio SignalR situato nella stessa area *Stati Uniti orientali* può essere configurato come `primary` ed endpoint in altre aree contrassegnate come `secondary`. In questa configurazione gli endpoint di servizio in altre aree possono **ricevere** messaggi da questo server applicazioni *degli Stati Uniti orientali* , ma non ci saranno client **tra aree** indirizzati a questo server app. L'architettura è illustrata nel diagramma seguente:

![Infrarossi tra le aree geografiche](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Quando un client tenta di `/negotiate` con il server app, con il router predefinito, l'SDK **Seleziona in modo casuale** un endpoint dal set di endpoint `primary` disponibili. Quando l'endpoint primario non è disponibile, SDK **Seleziona in modo casuale** tutti gli endpoint disponibili `secondary`. L'endpoint è contrassegnato come **disponibile** quando la connessione tra il server e l'endpoint del servizio è attiva.

In uno scenario tra più aree, quando un client prova a `/negotiate` con il server app ospitato negli *Stati Uniti orientali*, per impostazione predefinita restituisce sempre l'endpoint di `primary` che si trova nella stessa area. Quando non sono disponibili tutti gli endpoint *degli Stati Uniti orientali* , il client viene reindirizzato agli endpoint in altre aree. La sezione di failover riportata di seguito descrive in dettaglio lo scenario.

![Negoziazione normale](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Failover

Quando non sono disponibili tutti gli endpoint di `primary`, `/negotiate` il client preleva dagli endpoint `secondary` disponibili. Questo meccanismo di failover richiede che ogni endpoint funga da endpoint `primary` ad almeno un server app.

![Failover](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come configurare più istanze nella stessa applicazione per la scalabilità, il partizionamento orizzontale e scenari tra più aree.

È possibile usare più endpoint supportati anche negli scenari di disponibilità elevata e ripristino di emergenza.

> [!div class="nextstepaction"]
> [Configurare il servizio SignalR per il ripristino di emergenza e la disponibilità elevata](./signalr-concept-disaster-recovery.md)
