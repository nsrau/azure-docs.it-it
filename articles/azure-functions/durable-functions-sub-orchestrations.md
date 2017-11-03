---
title: Orchestrazioni secondarie in Funzioni permanenti - Azure
description: Come chiamare le orchestrazioni presenti nell'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 5184bef81d1cd6ca7b41c1634def24031a4a5942
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2017
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Orchestrazioni secondarie in Funzioni permanenti (Funzioni di Azure)

Oltre a chiamare le funzioni di attività, le funzioni dell'agente di orchestrazione possono chiamare altre funzioni dell'agente di orchestrazione. È possibile ad esempio compilare un'orchestrazione più grande da una raccolta di funzioni dell'agente di orchestrazione. Oppure è possibile eseguire più istanze di una funzione dell'agente di orchestrazione in parallelo.

Una funzione dell'agente di orchestrazione può chiamare un'altra funzione dell'agente di orchestrazione chiamando il metodo [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) o [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_). L'articolo [Error Handling & Compensation](durable-functions-error-handling.md#automatic-retry-on-failure) (Gestione e compensazione degli errori) contiene informazioni sulla ripetizione automatica.

Le funzioni secondarie dell'agente di orchestrazione si comportano come le funzioni di attività dal punto di vista del chiamante. Possono restituire un valore, generare un'eccezione e possono essere attese dalla funzione dell'agente di orchestrazione padre.

## <a name="example"></a>Esempio

L'esempio seguente illustra uno scenario di IoT ("Internet delle cose") in cui sono presenti più dispositivi di cui è necessario eseguire il provisioning. Esiste un'orchestrazione che deve essere eseguita per ognuno dei dispositivi, che potrebbe essere simile alla seguente:

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string deviceId = ctx.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await ctx.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await ctx.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await ctx.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

Questa funzione dell'agente di orchestrazione può essere usata così com'è per il provisioning occasionale di dispositivi o può essere parte di un'orchestrazione di dimensioni maggiori. Nel secondo caso, la funzione dell'agente di orchestrazione padre può pianificare le istanze di `DeviceProvisioningOrchestration` usando l'API `CallSubOrchestratorAsync`.

Di seguito è riportato un esempio che illustra come eseguire più funzioni dell'agente di orchestrazione in parallelo.

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string[] deviceIds = await ctx.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = ctx.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Learn what task hubs are and how to configure them](durable-functions-task-hubs.md) (Informazioni sugli hub attività e le modalità di configurazione)
