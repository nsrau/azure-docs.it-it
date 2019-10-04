---
title: Funzionalità HTTP in Durable Functions-funzioni di Azure
description: Informazioni sulle funzionalità HTTP integrate nell'estensione Durable Functions per funzioni di Azure.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 953558e34d41184f75d72baf5982e84eb51b1781
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694881"
---
# <a name="http-features"></a>Funzionalità HTTP

Durable Functions dispone di diverse funzionalità che semplificano l'incorporamento di orchestrazioni ed entità durevoli in flussi di lavoro HTTP. Questo articolo illustra in dettaglio alcune di queste funzionalità.

## <a name="exposing-http-apis"></a>Esposizione di API HTTP

Le orchestrazioni e le entità possono essere richiamate e gestite tramite richieste HTTP. L'estensione Durable Functions espone le API HTTP predefinite. Fornisce inoltre le API per interagire con le orchestrazioni e le entità dalle funzioni attivate da HTTP.

### <a name="built-in-http-apis"></a>API HTTP predefinite

L'estensione Durable Functions aggiunge automaticamente un set di API HTTP all'host di funzioni di Azure. Con queste API è possibile interagire con e gestire le orchestrazioni e le entità senza scrivere codice.

Sono supportate le API HTTP predefinite seguenti.

* [Avvia nuova orchestrazione](durable-functions-http-api.md#start-orchestration)
* [Istanza di orchestrazione query](durable-functions-http-api.md#get-instance-status)
* [Termina l'istanza di orchestrazione](durable-functions-http-api.md#terminate-instance)
* [Inviare un evento esterno a un'orchestrazione](durable-functions-http-api.md#raise-event)
* [Elimina cronologia orchestrazione](durable-functions-http-api.md#purge-single-instance-history)
* [Inviare un evento Operation a un'entità](durable-functions-http-api.md#signal-entity)
* [Eseguire query sullo stato di un'entità](durable-functions-http-api.md#query-entity)

Per una descrizione completa di tutte le API HTTP predefinite esposte dall'estensione Durable Functions, vedere l'articolo relativo alle [API HTTP](durable-functions-http-api.md) .

### <a name="http-api-url-discovery"></a>Rilevamento dell'URL di API HTTP

L' [associazione del client di orchestrazione](durable-functions-bindings.md#orchestration-client) espone le API che possono generare utili payload di risposta http. Ad esempio, è possibile creare una risposta contenente collegamenti alle API di gestione per un'istanza di orchestrazione specifica. Negli esempi seguenti viene illustrata una funzione trigger HTTP che illustra come utilizzare questa API per una nuova istanza di orchestrazione:

#### <a name="precompiled-c"></a>C# precompilato

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>Script C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-with-functions-20-or-later-only"></a>JavaScript con funzioni solo 2,0 o versioni successive

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

L'avvio di una funzione di orchestrazione tramite le funzioni trigger HTTP mostrate in precedenza può essere eseguito usando qualsiasi client HTTP. Il comando cURL seguente avvia una funzione dell'agente di orchestrazione denominata `DoWork`:

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Di seguito è riportato un esempio di risposta per un'orchestrazione con `abc123` come ID. Alcuni dettagli sono stati rimossi per maggiore chiarezza.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Nell'esempio precedente, ognuno dei campi che terminano con `Uri` corrisponde a un'API HTTP incorporata. È possibile utilizzare queste API per gestire l'istanza di orchestrazione di destinazione.

> [!NOTE]
> Il formato degli URL del webhook dipende dalla versione dell'host di funzioni di Azure in esecuzione. L'esempio precedente è per l'host di funzioni di Azure 2,0.

Per una descrizione di tutte le API HTTP predefinite, vedere le informazioni di [riferimento sull'API HTTP](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Verifica di operazioni asincrone

La risposta HTTP indicata in precedenza è stata concepita per semplificare l'implementazione di API asincrone a esecuzione prolungata con Durable Functions. Questo modello viene talvolta definito *modello di consumer di polling*. Il flusso client/server funziona come segue:

1. Il client invia una richiesta HTTP per avviare un processo a esecuzione prolungata come una funzione dell'agente di orchestrazione.
1. Il trigger HTTP di destinazione restituisce una risposta HTTP 202 con un'intestazione Location con il valore "statusQueryGetUri".
1. Il client esegue il polling dell'URL nell'intestazione Location. Il client continua a vedere le risposte HTTP 202 con un'intestazione Location.
1. Quando l'istanza viene completata o non riesce, l'endpoint nell'intestazione Location restituisce HTTP 200.

Questo protocollo consente il coordinamento dei processi a esecuzione prolungata con client o servizi esterni che possono eseguire il polling di un endpoint HTTP e seguire l'intestazione Location. Entrambe le implementazioni client e server di questo modello sono incorporate nelle API HTTP Durable Functions.

> [!NOTE]
> Per impostazione predefinita, tutte le azioni basate su HTTP fornite dalle [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/) supportano il modello di operazione asincrono standard. Questa funzionalità rende possibile incorporare una funzione permanente a esecuzione prolungata come parte di un flusso di lavoro di App per la logica. Per altre informazioni sul supporto delle app per la logica per i modelli HTTP asincroni [, vedere la documentazione sulle azioni e sui trigger del flusso di lavoro app](../../logic-apps/logic-apps-workflow-actions-triggers.md)per la logica di Azure.

> [!NOTE]
> Le interazioni con le orchestrazioni possono essere eseguite da qualsiasi tipo di funzione, non solo da funzioni attivate da HTTP.

Per ulteriori informazioni sulla gestione delle orchestrazioni e delle entità mediante le API client, vedere l'articolo relativo alla [gestione delle istanze](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>Utilizzo di API HTTP

Come descritto nei vincoli di codice della funzione dell'agente di [orchestrazione](durable-functions-code-constraints.md), le funzioni dell'agente di orchestrazione non possono eseguire direttamente i/ Ma in genere chiamano [funzioni di attività](durable-functions-types-features-overview.md#activity-functions) che eseguono operazioni di I/O.

A partire da Durable Functions 2,0, le orchestrazioni possono utilizzare le API HTTP in modo nativo utilizzando l' [associazione del trigger di orchestrazione](durable-functions-bindings.md#orchestration-trigger).

> [!NOTE]
> La possibilità di chiamare endpoint HTTP direttamente dalle funzioni dell'agente di orchestrazione non è ancora disponibile in JavaScript.

Il codice di esempio seguente illustra C# una funzione dell'agente di orchestrazione che effettua una richiesta HTTP in uscita tramite l'API .NET **CallHttpAsync** :

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

Utilizzando l'azione "Call HTTP", è possibile eseguire le operazioni seguenti nelle funzioni dell'agente di orchestrazione:

* Chiamare le API HTTP direttamente dalle funzioni di orchestrazione, con alcune limitazioni indicate in seguito.
* Supporta automaticamente i modelli di polling dello stato HTTP 202 sul lato client.
* Usare le [identità gestite di Azure](../../active-directory/managed-identities-azure-resources/overview.md) per effettuare chiamate http autorizzate ad altri endpoint di Azure.

La possibilità di utilizzare le API HTTP direttamente dalle funzioni dell'agente di orchestrazione è concepita come una praticità per un determinato set di scenari comuni. È possibile implementare tutte queste funzionalità usando le funzioni di attività. In molti casi, le funzioni di attività potrebbero offrire maggiore flessibilità.

### <a name="http-202-handling"></a>Gestione HTTP 202

L'API "Call HTTP" può implementare automaticamente il lato client del modello consumer di polling. Se un'API chiamata restituisce una risposta HTTP 202 con un'intestazione Location, la funzione dell'agente di orchestrazione esegue automaticamente il polling della risorsa location fino alla ricezione di una risposta diversa da 202. Questa risposta sarà la risposta restituita al codice della funzione dell'agente di orchestrazione.

> [!NOTE]
> Le funzioni dell'agente di orchestrazione supportano inoltre in modo nativo il modello di consumer di polling lato server, come descritto in [rilevamento delle operazioni asincrone](#async-operation-tracking). Questo supporto significa che le orchestrazioni in un'app per le funzioni possono coordinare facilmente le funzioni dell'agente di orchestrazione in altre app per le funzioni. Questa operazione è simile al concetto di [orchestrazione secondaria](durable-functions-sub-orchestrations.md) , ma con supporto per la comunicazione tra app. Questo supporto è particolarmente utile per lo sviluppo di app in stile microservizio.

### <a name="managed-identities"></a>Identità gestite

Durable Functions supporta in modo nativo le chiamate alle API che accettano i token di Azure Active Directory (Azure AD) per l'autorizzazione. Questo supporto usa le [identità gestite di Azure](../../active-directory/managed-identities-azure-resources/overview.md) per acquisire questi token.

Il codice seguente è un esempio di una funzione dell'agente di orchestrazione .NET. La funzione effettua chiamate autenticate per riavviare una macchina virtuale usando l' [API REST Azure Resource Manager macchine virtuali](https://docs.microsoft.com/rest/api/compute/virtualmachines).

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

Nell'esempio precedente, il parametro `tokenSource` è configurato per acquisire i token Azure AD per [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). I token sono identificati dall'URI della risorsa `https://management.core.windows.net`. Nell'esempio si presuppone che l'app per le funzioni corrente sia in esecuzione localmente o sia stata distribuita come app per le funzioni con un'identità gestita. Si presuppone che l'identità locale o l'identità gestita disponga di autorizzazioni per gestire le macchine virtuali nel gruppo di risorse specificato `myRG`.

In fase di esecuzione, l'origine del token configurata restituisce automaticamente un token di accesso OAuth 2,0. L'origine aggiunge quindi il token come bearer token all'intestazione di autorizzazione della richiesta in uscita. Questo modello rappresenta un miglioramento rispetto all'aggiunta manuale di intestazioni di autorizzazione alle richieste HTTP per i motivi seguenti:

* L'aggiornamento del token viene gestito automaticamente. Non è necessario preoccuparsi dei token scaduti.
* I token non vengono mai archiviati nello stato dell'orchestrazione durevole.
* Non è necessario scrivere codice per gestire l'acquisizione dei token.

È possibile trovare un esempio più completo nell' [esempio C# RestartVMs precompilato](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs).

Le identità gestite non sono limitate a gestione risorse di Azure. È possibile usare le identità gestite per accedere a qualsiasi API che accetti Azure AD token di connessione, inclusi i servizi di Azure da Microsoft e dalle app Web dei partner. L'app Web di un partner può anche essere un'altra app per le funzioni. Per un elenco dei servizi di Azure Microsoft che supportano l'autenticazione con Azure AD, vedere [servizi di Azure che supportano l'autenticazione Azure ad](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Limitazioni

Il supporto incorporato per la chiamata di API HTTP è una funzionalità comoda. Non è appropriato per tutti gli scenari.

Le richieste HTTP inviate dalle funzioni dell'agente di orchestrazione e le relative risposte vengono serializzate e permanenti come messaggi in coda. Questo comportamento di Accodamento garantisce che le chiamate HTTP siano [affidabili e sicure per la riproduzione dell'orchestrazione](durable-functions-orchestrations.md#reliability). Tuttavia, anche il comportamento di Accodamento presenta limitazioni:

* Ogni richiesta HTTP comporta una latenza aggiuntiva rispetto a un client HTTP nativo.
* I messaggi di richiesta o di risposta di grandi dimensioni che non possono rientrare in un messaggio in coda possono ridurre significativamente le prestazioni dell'orchestrazione Il sovraccarico di offload dei messaggi nell'archiviazione BLOB può causare un potenziale calo delle prestazioni.
* I payload di flusso, Chunked e Binary non sono supportati.
* La possibilità di personalizzare il comportamento del client HTTP è limitata.

Se una di queste limitazioni potrebbe influenzare il caso d'uso, è consigliabile usare invece le funzioni di attività e le librerie client HTTP specifiche del linguaggio per effettuare chiamate HTTP in uscita.

> [!NOTE]
> Gli sviluppatori .NET possono chiedersi perché questa funzionalità usi i tipi **DurableHttpRequest** e **DurableHttpResponse** anziché i tipi .NET **HttpRequestMessage** e **HttpResponseMessage** predefiniti.
>
> Questa scelta di progettazione è intenzionale. Il motivo principale è che i tipi personalizzati contribuiscono a garantire che gli utenti non rifacciano presupposti non corretti sui comportamenti supportati del client HTTP interno. I tipi specifici di Durable Functions consentono anche di semplificare la progettazione dell'API. Possono anche rendere più semplici le funzionalità speciali disponibili, ad esempio l' [integrazione delle identità gestite](#managed-identities) e il [modello di consumer di polling](#http-202-handling). 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle entità durevoli](durable-functions-entities.md)
