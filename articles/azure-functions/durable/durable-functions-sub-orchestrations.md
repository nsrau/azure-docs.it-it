---
title: Orchestrazioni secondarie in Funzioni permanenti - Azure
description: Come chiamare le orchestrazioni presenti nell'estensione Funzioni permanenti per Funzioni di Azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 379f2cb238aef08faba8dd3c8e5d9da4542a1867
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231287"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Orchestrazioni secondarie in Funzioni permanenti (Funzioni di Azure)

Oltre a chiamare le funzioni di attività, le funzioni dell'agente di orchestrazione possono chiamare altre funzioni dell'agente di orchestrazione. For example, you can build a larger orchestration out of a library of smaller orchestrator functions. Oppure è possibile eseguire più istanze di una funzione dell'agente di orchestrazione in parallelo.

An orchestrator function can call another orchestrator function using the `CallSubOrchestratorAsync` or the `CallSubOrchestratorWithRetryAsync` methods in .NET, or the `callSubOrchestrator` or `callSubOrchestratorWithRetry` methods in JavaScript. L'articolo [Error Handling & Compensation](durable-functions-error-handling.md#automatic-retry-on-failure) (Gestione e compensazione degli errori) contiene informazioni sulla ripetizione automatica.

Le funzioni secondarie dell'agente di orchestrazione si comportano come le funzioni di attività dal punto di vista del chiamante. Possono restituire un valore, generare un'eccezione e possono essere attese dalla funzione dell'agente di orchestrazione padre. 
## <a name="example"></a>Esempio

L'esempio seguente illustra uno scenario di IoT ("Internet delle cose") in cui sono presenti più dispositivi di cui è necessario eseguire il provisioning. The following function represents the provisioning workflow that needs to be executed for each device:

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

Questa funzione dell'agente di orchestrazione può essere usata così com'è per il provisioning occasionale di dispositivi o può essere parte di un'orchestrazione di dimensioni maggiori. In the latter case, the parent orchestrator function can schedule instances of `DeviceProvisioningOrchestration` using the `CallSubOrchestratorAsync` (.NET) or `callSubOrchestrator` (JavaScript) API.

Di seguito è riportato un esempio che illustra come eseguire più funzioni dell'agente di orchestrazione in parallelo.

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

> [!NOTE]
> Sub-orchestrations must be defined in the same function app as the parent orchestration. If you need to call and wait for orchestrations in another function app, consider using the built-in support for HTTP APIs and the HTTP 202 polling consumer pattern. For more information, see the [HTTP Features](durable-functions-http-features.md) topic.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Learn how to set a custom orchestration status](durable-functions-custom-orchestration-status.md)