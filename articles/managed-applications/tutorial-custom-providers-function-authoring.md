---
title: Creare un endpoint RESTful per i provider personalizzati
description: Questa esercitazione illustra come creare un endpoint RESTful per provider personalizzati. Descrive in dettaglio come gestire le richieste e le risposte per i metodi HTTP RESTful supportati.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: ae821f07034b038f49a400de8c00e4ace6787192
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172893"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Creare un endpoint RESTful per i provider personalizzati

Un provider personalizzato è un contratto tra Azure e un endpoint. Con i provider personalizzati, è possibile personalizzare i flussi di lavoro in Azure. Questa esercitazione illustra come creare un endpoint RESTful per provider personalizzati. Se non si ha familiarità con i provider personalizzati di Azure, vedere la [panoramica dei provider di risorse personalizzati](./custom-providers-overview.md).

> [!NOTE]
> Questa esercitazione si basa sull'esercitazione [Configurare Funzioni di Azure per i provider personalizzati di Azure](./tutorial-custom-providers-function-setup.md). Alcuni passaggi dell'esercitazione funzionano solo se un'app per le funzioni di Azure è stata configurata per l'uso con i provider personalizzati.

## <a name="work-with-custom-actions-and-custom-resources"></a>Usare azioni e risorse personalizzate

In questa esercitazione l'app per le funzioni verrà aggiornata per fungere da endpoint RESTful per il provider personalizzato. In Azure le risorse e le azioni sono modellate in base alla specifica RESTful di base seguente:

- **PUT**: Creare una nuova risorsa
- **GET (istanza)** : recupera un'istanza esistente
- **DELETE**: rimuove un'istanza esistente
- **POST**: attiva un'azione
- **GET (raccolta)** : elenca tutte le risorse esistenti

 Per questa esercitazione si userà archiviazione tabelle di Azure. Ma è anche possibile usare qualsiasi database o servizio di archiviazione.

## <a name="partition-custom-resources-in-storage"></a>Partizionare le risorse personalizzate nell'archiviazione

Poiché si sta creando un servizio RESTful, è necessario archiviare le risorse create. Per archiviazione tabelle di Azure, è necessario generare le chiavi di partizione e di riga per i dati. Per i provider personalizzati, i dati devono essere partizionati in base a ognuno di essi. Quando una richiesta in ingresso viene inviata al provider personalizzato, quest'ultimo aggiunge l'intestazione `x-ms-customproviders-requestpath` alla richiesta in uscita per l'endpoint.

L'esempio seguente mostra un'intestazione `x-ms-customproviders-requestpath` per una risorsa personalizzata:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

In base all'intestazione `x-ms-customproviders-requestpath` dell'esempio, è possibile creare i parametri *partitionKey* e *rowKey* per l'archiviazione, come illustrato nella tabella seguente:

Parametro | Modello | DESCRIZIONE
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | Il parametro *partitionKey* specifica come vengono partizionati i dati. In genere i dati vengono partizionati dall'istanza del provider personalizzato.
*rowKey* | `{myResourceType}:{myResourceName}` | Il parametro *rowKey* specifica l'identificatore individuale per i dati. In genere l'identificatore corrisponde al nome della risorsa.

È anche necessario creare una nuova classe per modellare la risorsa personalizzata. In questa esercitazione aggiungere la classe **CustomResource** seguente all'app per le funzioni:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** è una semplice classe generica che accetta qualsiasi dato di input. È basata su **TableEntity**, che si usa per archiviare i dati. La classe **CustomResource** eredita due proprietà da **TableEntity**: **partitionKey** e **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Supportare metodi RESTful per provider personalizzati

> [!NOTE]
> Se il codice non viene copiato direttamente da questa esercitazione, il contenuto della risposta deve essere un codice JSON valido che imposta l'intestazione `Content-Type` su `application/json`.

Dopo aver configurato il partizionamento dei dati,creare il CRUD di base e attivare i metodi per le risorse e le azioni personalizzate. Poiché i provider personalizzati fungono da proxy, l'endpoint RESTful deve modellare e gestire la richiesta e la risposta. I frammenti di codice seguenti mostrano come gestire le operazioni RESTful di base.

### <a name="trigger-a-custom-action"></a>Attivare un'azione personalizzata

Per i provider personalizzati, un'azione personalizzata viene attivata tramite richieste POST. Un'azione personalizzata può facoltativamente accettare un corpo della richiesta che contiene un set di parametri di input. L'azione restituisce quindi una risposta che ne segnala il risultato e indica se è riuscita o meno.

Aggiungere il metodo **TriggerCustomAction** seguente all'app per le funzioni:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

Il metodo **TriggerCustomAction** accetta una richiesta in ingresso e restituisce semplicemente la risposta con un codice di stato.

### <a name="create-a-custom-resource"></a>Creare una risorsa personalizzata

Per i provider personalizzati, una risorsa personalizzata viene attivata tramite richieste PUT. Il provider personalizzato accetterà un corpo della richiesta JSON, che contiene un set di proprietà per la risorsa personalizzata. In Azure le risorse seguono un modello RESTful. È possibile usare lo stesso URL della richiesta per creare, recuperare o eliminare una risorsa.

Aggiungere il metodo **CreateCustomResource** seguente per creare nuove risorse:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

Il metodo **CreateCustomResource** aggiorna la richiesta in ingresso per includere i campi specifici di Azure: **id**, **name** e **type**. Questi campi sono proprietà di primo livello usate da tutti i servizi di Azure. Consentono l'interoperabilità del provider personalizzato con altri servizi, come Criteri di Azure, modelli di Azure Resource Manager e Log attività di Azure.

Proprietà | Esempio | DESCRIZIONE
---|---|---
**nome** | {myCustomResourceName} | Il nome della risorsa personalizzata
**type** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | Lo spazio dei nomi del tipo di risorsa
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | L'ID della risorsa

Oltre ad aggiungere le proprietà, viene anche salvato il documento JSON in archiviazione tabelle di Azure.

### <a name="retrieve-a-custom-resource"></a>Recuperare una risorsa personalizzata

Per i provider personalizzati, una risorsa personalizzata viene recuperata tramite richieste GET. Il provider personalizzato *non* accetta un corpo della richiesta JSON. Per le richieste GET, l'endpoint usa l'intestazione `x-ms-customproviders-requestpath` per restituire la risorsa già creata.

Aggiungere il metodo **RetrieveCustomResource** seguente per recuperare le risorse esistenti:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

In Azure le risorse seguono un modello RESTful. L'URL della richiesta che crea una risorsa è anche quello che la restituisce se viene eseguita una richiesta GET.

### <a name="remove-a-custom-resource"></a>Rimuovere una risorsa personalizzata

Per i provider personalizzati, una risorsa personalizzata viene rimossa tramite richieste DELETE. Il provider personalizzato *non* accetta un corpo della richiesta JSON. Per le richieste DELETE, l'endpoint usa l'intestazione `x-ms-customproviders-requestpath` per eliminare la risorsa già creata.

Aggiungere il metodo **RemoveCustomResource** seguente per rimuovere le risorse esistenti:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

In Azure le risorse seguono un modello RESTful. L'URL della richiesta che crea una risorsa è anche quello che la elimina se viene eseguita una richiesta DELETE.

### <a name="list-all-custom-resources"></a>Elenco di tutte le risorse personalizzate

Per i provider personalizzati, è possibile enumerare un elenco di risorse personalizzate esistente tramite richieste GET della raccolta. Il provider personalizzato *non* accetta un corpo della richiesta JSON. Per una raccolta di richieste GET, l'endpoint usa l'intestazione `x-ms-customproviders-requestpath` per enumerare le richieste già create.

Aggiungere il metodo **EnumerateAllCustomResources** seguente per enumerare le risorse esistenti:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> RowKey QueryComparisons.GreaterThan e QueryComparisons.LessThan rappresentano la sintassi di archiviazione tabelle di Azure per l'esecuzione di una query "startswith" per la ricerca di stringhe.

Per elencare tutte le risorse esistenti, generare una query su archiviazione tabelle di Azure che assicura che le risorse esistano nella partizione del provider personalizzato. La query verifica quindi che la chiave di riga inizi con lo stesso valore `{myResourceType}`.

## <a name="integrate-restful-operations"></a>Integrare operazioni RESTful

Dopo aver aggiunto tutti i metodi RESTful all'app per le funzioni, è possibile aggiornare il metodo **Run** principale per chiamare le funzioni e gestire le diverse richieste REST:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

Il metodo **Run** aggiornato ora include il binding di input *tableStorage* aggiunto per l'archiviazione tabelle di Azure. La prima parte del metodo legge l'intestazione `x-ms-customproviders-requestpath` e usa la libreria `Microsoft.Azure.Management.ResourceManager.Fluent` per analizzare il valore come ID risorsa. L'intestazione `x-ms-customproviders-requestpath` viene inviata dal provider personalizzato e specifica il percorso della richiesta in ingresso.

Usando l'ID risorsa analizzato, è ora possibile generare i valori di **partitionKey** e **rowKey** per i dati per cercare o archiviare risorse personalizzate.

Dopo aver aggiunto i metodi e le classi, è necessario aggiornare i metodi **using** per l'app per le funzioni. Aggiungere il codice seguente all'inizio del file C#:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Se ci si è persi in un punto qualsiasi di questa esercitazione, l'esempio di codice completo è disponibile nelle [informazioni di riferimento sull'endpoint RESTful C# per provider personalizzati](./reference-custom-providers-csharp-endpoint.md). Dopo aver completato l'app per le funzioni, salvare il relativo URL. Può essere usato per attivare l'app per le funzioni nelle esercitazioni successive.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un endpoint RESTful da usare con l'endpoint dei provider personalizzati di Azure. Per informazioni su come creare un provider personalizzato, vedere l'articolo [Esercitazione: Creazione di un provider personalizzato](./tutorial-custom-providers-create.md).
