---
title: Come disabilitare le funzioni in Funzioni di Azure
description: Informazioni su come disabilitare e abilitare le funzioni in Funzioni di Azure 1.x e 2.x.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: 498bb8c0f1e7bb674605d4a98f0be0f3e0b9a7c9
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650487"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Come disabilitare le funzioni in Funzioni di Azure

Questo articolo illustra come disabilitare una funzione in Funzioni di Azure. *Disabilitare* una funzione significa fare in modo che il runtime ignori il trigger automatico definito per la funzione. La modalità di disabilitazione di una funzione dipende dalla versione del runtime e dal linguaggio di programmazione:

* Funzioni 2. x:
  * Una sola modalità per tutti i linguaggi
  * Una modalità facoltativa per le librerie di classi C#
* Funzioni 1. x:
  * Linguaggi di scripting
  * Libreria di classi C#

## <a name="functions-2x---all-languages"></a>Funzioni 2.x: tutti i linguaggi

In functions 2. x, una funzione viene disabilitata usando un'impostazione dell'app nel `AzureWebJobs.<FUNCTION_NAME>.Disabled`formato. È possibile creare e modificare l'impostazione dell'applicazione in diversi modi, ad esempio usando l'interfaccia della riga di comando di [Azure](/cli/azure/) e dalla scheda **Gestisci** della funzione nella [portale di Azure](https://portal.azure.com). 

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Nell'interfaccia della riga di comando di Azure [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) usare il comando per creare e modificare l'impostazione dell'app. Il comando che segue Disabilita una funzione denominata `QueueTrigger` creando un'impostazione dell'app denominata `AzureWebJobs.QueueTrigger.Disabled` impostarla su `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Per riabilitare la funzione, eseguire di nuovo lo stesso comando con un valore `false`di.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portale

È anche possibile usare l'opzione **Stato funzione** nella scheda **Gestione** della funzione. L'opzione funziona creando ed eliminando l'impostazione dell'app `AzureWebJobs.<FUNCTION_NAME>.Disabled`.

![Opzione Stato funzione](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Funzioni 2.x: librerie di classi C#

In una libreria di classi di Funzioni 2.x è consigliabile usare il metodo applicabile per tutti i linguaggi. Tuttavia, se si preferisce, è possibile [usare l'attributo Disable come in Funzioni 1.x](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Funzioni 1.x: linguaggi di scripting

Per i linguaggi di scripting come Script C# e JavaScript, si usa la proprietà `disabled` del file *function.json* per indicare al runtime di non attivare una funzione. Questa proprietà può essere impostata su `true` o sul nome di un'impostazione dell'app:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
oppure 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

Nel secondo esempio la funzione viene disabilitata se è presente un'impostazione dell'app denominata IS_DISABLED che è impostata su `true` o 1.

È possibile modificare il file nel portale di Azure o usare l'opzione **Stato funzione** nella scheda **Gestione** della funzione. L'opzione del portale funziona modificando il file *function.json*.

![Opzione Stato funzione](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Funzioni 1.x: librerie di classi C#

In una libreria di classi di Funzioni 1.x, si usa un attributo `Disable` per impedire che una funzione venga attivata. È possibile usare l'attributo senza un parametro di costruttore, come illustrato nell'esempio seguente:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Se si usa l'attributo senza un parametro di costruttore è necessario ricompilare e ridistribuire il progetto per modificare lo stato disabilitato della funzione. Un modo più flessibile per usare l'attributo consiste nell'includere un parametro di costruttore che fa riferimento a un'impostazione booleana dell'app, come illustrato nell'esempio seguente:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Questo metodo consente di abilitare e disabilitare la funzione modificando l'impostazione dell'app, senza ricompilare o ridistribuire il progetto. Se si modifica un'impostazione, l'app per le funzioni viene riavviata e la modifica dello stato disabilitato viene rilevata immediatamente.

> [!IMPORTANT]
> L'uso dell'attributo `Disabled` è l'unico modo per disabilitare una funzione di libreria di classi. Il file *function.json* generato per una funzione di libreria di classi non è pensato per essere modificato direttamente. Qualsiasi modifica apportata alla proprietà `disabled` in tale file non ha alcun effetto.
>
> Lo stesso vale per l'opzione **Stato funzione** nella scheda **Gestione** poiché funziona modificando il file *function.json*.
>
> Si noti inoltre che il portale potrebbe indicare che la funzione è disabilitata quando in realtà non lo è.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo contiene informazioni sulla disabilitazione dei trigger automatici. Per altre informazioni, vedere [Trigger e associazioni](functions-triggers-bindings.md).
