---
title: Creazione di un endpoint RESTful per provider personalizzati
description: Questa esercitazione illustra come creare un endpoint RESTful per provider personalizzati. Descrive in dettaglio come gestire le richieste e le risposte per i metodi HTTP RESTful supportati.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 176e3b02cbda7577e306d86363cfe5b41335fb6e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799180"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Creazione di un endpoint RESTful per provider personalizzati

I provider personalizzati consentono di personalizzare i flussi di lavoro in Azure. Un provider personalizzato è un contratto tra Azure e un `endpoint`. Questa esercitazione illustra il processo di creazione di un `endpoint` RESTful per provider personalizzati. Se non si ha familiarità con i provider personalizzati di Azure, vedere la [panoramica dei provider di risorse personalizzati](./custom-providers-overview.md).

Questa esercitazione è suddivisa nei passaggi seguenti:

- Uso di azioni personalizzate e risorse personalizzate
- Come partizionare le risorse personalizzate nell'archiviazione
- Supportare metodi RESTful per provider personalizzati
- Integrare operazioni RESTful

Questa esercitazione si basa sulle esercitazioni seguenti:

- [Configurare Funzioni di Azure per i provider personalizzati di Azure](./tutorial-custom-providers-function-setup.md)

> [!NOTE]
> Questa esercitazione si basa sull'esercitazione precedente. Alcuni passaggi dell'esercitazione funzionano solo se una funzione di Azure è stata configurata per l'uso con i provider personalizzati.

## <a name="working-with-custom-actions-and-custom-resources"></a>Uso di azioni personalizzate e risorse personalizzate

In questa esercitazione la funzione verrà aggiornata per fungere da endpoint RESTful per il provider personalizzato. In Azure le risorse e le azioni sono modellate in base alla specifica RESTful di base: PUT: crea una nuova risorsa, GET (istanza): recupera una risorsa esistente, DELETE: rimuove una risorsa esistente, POST: attiva un'azione e GET (raccolta): elenca tutte le risorse esistenti. Per questa esercitazione verranno usate le tabelle di Azure come risorsa di archiviazione, ma è possibile usare qualsiasi database o servizio di archiviazione.

## <a name="how-to-partition-custom-resources-in-storage"></a>Come partizionare le risorse personalizzate nell'archiviazione

Per creare un servizio RESTful, è necessario archiviare le risorse create nella risorsa di archiviazione. Per l'archiviazione tabelle di Azure, è necessario generare le chiavi di partizione e di riga per i dati. Per i provider personalizzati, i dati devono essere partizionati in base a ognuno di essi. Quando una richiesta in ingresso viene inviata al provider personalizzato, quest'ultimo aggiunge l'intestazione `x-ms-customproviders-requestpath` alla richiesta in uscita per l'`endpoint`.

Intestazione `x-ms-customproviders-requestpath` di esempio per una risorsa personalizzata:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

In base alla precedente intestazione `x-ms-customproviders-requestpath` di esempio, è possibile creare gli elementi partitionKey e rowKey per l'archiviazione come indicato di seguito:

Parametro | Modello | DESCRIZIONE
---|---
partitionKey | '{subscriptionId}:{resourceGroupName}:{resourceProviderName}' | PartitionKey indica come vengono partizionati i dati. Nella maggior parte dei casi, i dati devono essere partizionati in base all'istanza del provider personalizzato.
rowKey | '{myResourceType}:{myResourceName}' | RowKey corrisponde all'identificatore individuale per i dati. Nella maggior parte dei casi, si tratta del nome della risorsa.

Inoltre, è anche necessario creare una nuova classe per modellare la risorsa personalizzata. In questa esercitazione alla funzione verrà aggiunta la classe `CustomResource`, una classe generica che accetta qualsiasi dato di input:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```

Viene così creata una classe di base basata su `TableEntity`, che si usa per archiviare i dati. La classe `CustomResource` eredita due proprietà da `TableEntity`: partitionKey e rowKey.

## <a name="support-custom-provider-restful-methods"></a>Supportare metodi RESTful per provider personalizzati

> [!NOTE]
> Se il codice non viene copiato direttamente da questa esercitazione, il contenuto della risposta deve essere un codice JSON valido che imposta l'intestazione `Content-Type` come `application/json`.

Dopo aver configurato il partizionamento dei dati, eseguire lo scaffolding dei metodi CRUD e trigger di base per risorse e azioni personalizzate. Poiché i provider personalizzati fungono da proxy, la richiesta e la risposta devono essere modellate e gestite dall'`endpoint` RESTful. Seguire i frammenti di codice seguenti per la gestione delle operazioni RESTful di base:

### <a name="trigger-custom-action"></a>Attivare l'azione personalizzata

Per i provider personalizzati, un'azione personalizzata viene attivata tramite richieste `POST`. Un'azione personalizzata può facoltativamente accettare un corpo della richiesta che contiene un set di parametri di input. L'azione dovrà quindi restituire una risposta segnalando il risultato e se è riuscita o meno. In questa esercitazione verrà aggiunto il metodo `TriggerCustomAction` alla funzione:

```csharp
/// <summary>
/// Triggers a custom action with some side effect.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <returns>The http response result of the custom action.</returns>
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

Il metodo `TriggerCustomAction` accetta una richiesta in ingresso e restituisce semplicemente la risposta con un codice di stato di operazione riuscita. 

### <a name="create-custom-resource"></a>Creare una risorsa personalizzata

Per i provider personalizzati, una risorsa personalizzata viene attivata tramite richieste `PUT`. Il provider personalizzato accetterà un corpo della richiesta JSON, che contiene un set di proprietà per la risorsa personalizzata. In Azure le risorse seguono un modello RESTful. Lo stesso URL della richiesta usato per creare una richiesta dovrebbe essere anche in grado di recuperare ed eliminare la risorsa. In questa esercitazione verrà aggiunto il metodo `CreateCustomResource` per creare nuove risorse:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="azureResourceId">The parsed Azure resource Id.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the created custom resource.</returns>
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

Il metodo `CreateCustomResource` aggiorna la richiesta in ingresso per includere i campi specifici di Azure: `id`, `name` e `type`. Si tratta di proprietà di primo livello usate dai servizi di Azure. Consentono l'integrazione del provider personalizzato con altri servizi, come Criteri di Azure, modelli di Azure Resource Manager e log attività di Azure.

Proprietà | Esempio | DESCRIZIONE
---|---|---
name | '{myCustomResourceName}' | Il nome della risorsa personalizzata.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Lo spazio dei nomi del tipo di risorsa.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName}' | L'ID della risorsa.

Oltre ad aggiungere le proprietà, viene anche salvato il documento in archiviazione tabelle di Azure. 

### <a name="retrieve-custom-resource"></a>Recuperare la risorsa personalizzata

Per i provider personalizzati, una risorsa personalizzata viene recuperata tramite richieste `GET`. Il provider personalizzato *non* accetterà un corpo della richiesta JSON. Nel caso di richieste `GET`, l'**endpoint** deve usare l'intestazione `x-ms-customproviders-requestpath` per restituire la risorsa già creata. In questa esercitazione verrà aggiunto il metodo `RetrieveCustomResource` per recuperare le risorse esistenti:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the existing custom resource.</returns>
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

In Azure le risorse devono seguire un modello RESTful. L'URL della richiesta che ha creato la risorsa deve anche restituirla se viene eseguita una richiesta `GET`.

### <a name="remove-custom-resource"></a>Rimuovere la risorsa personalizzata

Per i provider personalizzati, una risorsa personalizzata viene rimossa tramite richieste `DELETE`. Il provider personalizzato *non* accetterà un corpo della richiesta JSON. Nel caso di richieste `DELETE`, l'**endpoint** deve usare l'intestazione `x-ms-customproviders-requestpath` per eliminare la risorsa già creata. In questa esercitazione verrà aggiunto il metodo `RemoveCustomResource` per rimuovere le risorse esistenti:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the result of the delete.</returns>
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

In Azure le risorse devono seguire un modello RESTful. L'URL della richiesta che ha creato la risorsa deve anche eliminarla se viene eseguita una richiesta `DELETE`.

### <a name="list-all-custom-resources"></a>Elenco di tutte le risorse personalizzate

Per i provider personalizzati, l'elenco delle risorse personalizzate esistenti può essere enumerato tramite richieste `GET` della raccolta. Il provider personalizzato *non* accetterà un corpo della richiesta JSON. Nel caso di richieste `GET`, l'`endpoint` deve usare l'intestazione `x-ms-customproviders-requestpath` per enumerare le risorse già create. In questa esercitazione verrà aggiunto il metodo `EnumerateAllCustomResources` per enumerare le risorse esistenti.

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The http response containing a list of resources stored under 'value'.</returns>
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
> La chiave di riga maggiore di e minore di è la sintassi della tabella di Azure per eseguire una query "startswith" per le stringhe. 

Per elencare tutte le risorse esistenti, viene generata una query sulla tabella di Azure che assicura che le risorse esistano nella partizione del provider personalizzato. La query verifica quindi che la chiave di riga inizi con lo stesso valore `{myResourceType}`.

## <a name="integrate-restful-operations"></a>Integrare operazioni RESTful

Dopo aver aggiunto tutti i metodi RESTful alla funzione, è possibile aggiornare il metodo `Run` main per chiamare le funzioni e gestire le diverse richieste REST:

```csharp
/// <summary>
/// Entry point for the Azure Function webhook and acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <returns>The http response for the custom Azure API.</returns>
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
        // Action request for an custom action.
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

Il metodo `Run` aggiornato includerà ora il binding di input `tableStorage` aggiunto per archiviazione tabelle di Azure. La prima parte del metodo leggerà ora l'intestazione `x-ms-customproviders-requestpath` e userà la libreria `Microsoft.Azure.Management.ResourceManager.Fluent` per analizzare il valore come ID risorsa. L'intestazione `x-ms-customproviders-requestpath` viene inviata al provider personalizzato e indica il percorso della richiesta in ingresso. Usando l'ID risorsa analizzato, è ora possibile generare partitionKey e rowKey per i dati per cercare o archiviare risorse personalizzate.

Oltre ad aggiungere i metodi e le classi, è necessario aggiornare i metodi using per la funzione. Aggiungere quanto segue all'inizio del file:

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

Se ci si è persi in un punto qualsiasi di questa esercitazione, l'esempio di codice completo è disponibile nelle [informazioni di riferimento sull'endpoint RESTful C# per provider personalizzati](./reference-custom-providers-csharp-endpoint.md). Una volta completata la funzione, salvare il relativo URL che è possibile usare per attivarla e verrà usato nelle esercitazioni successive.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un endpoint RESTful da usare con l'`endpoint` del provider personalizzato di Azure. Passare all'articolo successivo per creare un provider personalizzato.

- [Esercitazione: Creazione di un provider personalizzato](./tutorial-custom-providers-create.md)
