---
title: Scalabilità con più istanze - Servizio SignalR di AzureScale with multiple instances - Azure SignalR Service
description: In molti scenari di scalabilità, il cliente spesso deve eseguire il provisioning di più istanze e configurarle per usarle insieme, per creare una distribuzione su larga scala. Ad esempio, il partizionamento di partizione richiede il supporto di più istanze.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158169"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Come scalare SignalR Service con più istanze?
L'SDK del servizio SignalR più recente supporta più endpoint per le istanze del servizio SignalR.The latest SignalR Service SDK supports multiple endpoints for SignalR Service instances. È possibile utilizzare questa funzionalità per ridimensionare le connessioni simultanee o utilizzarla per la messaggistica tra aree.

## <a name="for-aspnet-core"></a>Per ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Come aggiungere più endpoint da config?

Configurazione con `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` chiave o per la stringa di connessione del servizio SignalR.Config with key or for SignalR Service connection string.

Se la chiave `Azure:SignalR:ConnectionString:`inizia con , `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`deve `Name` `EndpointType` essere in `ServiceEndpoint` formato , dove e sono proprietà dell'oggetto e sono accessibili dal codice.

È possibile aggiungere più stringhe `dotnet` di connessione di istanza usando i comandi seguenti:You can add multiple instance connection strings using the following commands:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Come aggiungere più endpoint dal codice?

Viene `ServicEndpoint` introdotta una classe per descrivere le proprietà di un endpoint del servizio SignalR di Azure.A class is introduced to describe the properties of an Azure SignalR Service endpoint.
È possibile configurare più endpoint di istanza quando si usa Azure SignalR Service SDK tramite:You can configure multiple instance endpoints when using Azure SignalR Service SDK through:
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

### <a name="how-to-customize-endpoint-router"></a>Come personalizzare il router endpoint?

Per impostazione predefinita, l'SDK utilizza [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) per prelevare gli endpoint.

#### <a name="default-behavior"></a>Comportamento predefinito 
1. Routing delle richieste client

    Quando `/negotiate` il client con il server applicazioni. Per impostazione predefinita, SDK **seleziona in modo casuale** un endpoint dal set di endpoint di servizio disponibili.

2. Routing dei messaggi del server

    Quando si invia un messaggio a un'specifica connessione e la connessione di destinazione viene instradata al server corrente, il messaggio viene indirizzato direttamente all'endpoint connesso. In caso contrario, i messaggi vengono trasmessi a ogni endpoint SignalR di Azure.Otherwise, the messages are broadcasted to every Azure SignalR endpoint.

#### <a name="customize-routing-algorithm"></a>Personalizzare l'algoritmo di routing
È possibile creare il proprio router quando si dispone di conoscenze speciali per identificare gli endpoint a cui devono andare i messaggi.

Un router personalizzato è definito di seguito `east-` come esempio quando `east`i gruppi che iniziano con passano sempre all'endpoint denominato :

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

Un altro esempio seguente, che esegue l'override del comportamento di negoziazione predefinito, per selezionare gli endpoint dipende da dove si trova il server applicazioni.

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

Non dimenticare di registrare il router al contenitore DI utilizzando:

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

### <a name="how-to-add-multiple-endpoints-from-config"></a>Come aggiungere più endpoint da config?

Configurazione con `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` chiave o per la stringa di connessione del servizio SignalR.Config with key or for SignalR Service connection string.

Se la chiave `Azure:SignalR:ConnectionString:`inizia con , `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`deve `Name` `EndpointType` essere in `ServiceEndpoint` formato , dove e sono proprietà dell'oggetto e sono accessibili dal codice.

È possibile aggiungere più `web.config`stringhe di connessione di istanza a :You can add multiple instance connection strings to:

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

Viene `ServicEndpoint` introdotta una classe per descrivere le proprietà di un endpoint del servizio SignalR di Azure.A class is introduced to describe the properties of an Azure SignalR Service endpoint.
È possibile configurare più endpoint di istanza quando si usa Azure SignalR Service SDK tramite:You can configure multiple instance endpoints when using Azure SignalR Service SDK through:

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

### <a name="how-to-customize-router"></a>Come personalizzare il router?

L'unica differenza tra ASP.NET SignalR e ASP.NET Core `GetNegotiateEndpoint`SignalR è il tipo di contesto http per . Ad ASP.NET SignalR, è di tipo [IOwinContext.For ASP.NET](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) SignalR, it is of IOwinContext type.

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

Non dimenticare di registrare il router al contenitore DI utilizzando:

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

## <a name="configuration-in-cross-region-scenarios"></a>Configurazione in scenari tra aree

L'oggetto `ServiceEndpoint` `EndpointType` dispone di `primary` `secondary`una proprietà con valore o .

`primary`Gli endpoint sono endpoint preferiti per ricevere il traffico client e sono considerati con connessioni di rete più affidabili; `secondary` Gli endpoint sono considerati con connessioni di rete meno affidabili e vengono utilizzati solo per portare il traffico del server al traffico client, ad esempio la trasmissione di messaggi, non per portare il traffico da client a server.

Nei casi tra aree geografiche, la rete può essere instabile. Per un server applicazioni situato negli *Stati Uniti orientali,* l'endpoint del `primary` servizio SignalR situato nella stessa area Stati Uniti *orientali* può essere configurato come e gli endpoint in altre aree contrassegnate come `secondary`. In questa configurazione, gli endpoint del servizio in altre aree possono **ricevere** messaggi da questo server applicazioni *Stati Uniti orientali,* ma non saranno presenti client **tra** aree instradati a questo server applicazioni. L'architettura è illustrata nel diagramma seguente:The architecture is shown in the diagram below:

![Cross-Geo Infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Quando un `/negotiate` client tenta con il server applicazioni, con il router predefinito, `primary` SDK **seleziona casualmente** un endpoint dal set di endpoint disponibili. Quando l'endpoint primario non è disponibile, SDK `secondary` seleziona in **modo casuale** da tutti gli endpoint disponibili. L'endpoint viene contrassegnato come **disponibile** quando la connessione tra il server e l'endpoint del servizio è attiva.

In uno scenario tra aree, quando un client tenta `/negotiate` con il server applicazioni ospitato negli Stati Uniti *orientali,* per impostazione predefinita restituisce sempre l'endpoint `primary` che si trova nella stessa area. Quando tutti gli endpoint *Stati Uniti orientali* non sono disponibili, il client viene reindirizzato agli endpoint in altre aree. La sezione di failover riportata di seguito descrive lo scenario in dettaglio.

![Negoziazione normale](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Failover

Quando `primary` tutti gli endpoint non sono `/negotiate` disponibili, i `secondary` prelievi del client dagli endpoint disponibili. Questo meccanismo di failover richiede che `primary` ogni endpoint funga da endpoint per almeno un server applicazioni.

![Failover](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come configurare più istanze nella stessa applicazione per scenari di scalabilità, partizionamento orizzontale e tra aree.

Più endpoint supportati possono essere utilizzati anche in scenari di disponibilità elevata e ripristino di emergenza.

> [!div class="nextstepaction"]
> [Installazione del servizio SignalR per il ripristino di emergenza e la disponibilità elevata](./signalr-concept-disaster-recovery.md)
