---
title: Come disabilitare le funzioni in Funzioni di Azure
description: Informazioni su come disabilitare e abilitare funzioni in funzioni di Azure.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: bffb3136c77074ecd50e839fd7c73144ad910967
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970976"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Come disabilitare le funzioni in Funzioni di Azure

Questo articolo illustra come disabilitare una funzione in Funzioni di Azure. *Disabilitare* una funzione significa fare in modo che il runtime ignori il trigger automatico definito per la funzione. Ciò consente di impedire l'esecuzione di una funzione specifica senza arrestare l'intera app per le funzioni.

Il metodo consigliato per disabilitare una funzione consiste nell'usare un'impostazione dell'app nel formato `AzureWebJobs.<FUNCTION_NAME>.Disabled`. È possibile creare e modificare l'impostazione dell'applicazione in diversi modi, ad esempio usando l'interfaccia della riga di comando di [Azure](/cli/azure/) e dalla scheda **Gestisci** della funzione nella [portale di Azure](https://portal.azure.com). 

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

Nell'interfaccia della riga di comando di Azure usare il comando [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) per creare e modificare l'impostazione dell'app. Il comando seguente disabilita una funzione denominata `QueueTrigger` creando un'impostazione dell'app denominata `AzureWebJobs.QueueTrigger.Disabled` impostarla su `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Per riabilitare la funzione, eseguire di nuovo lo stesso comando con il valore `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Usare il portale

È anche possibile usare l'opzione di **stato della funzione** nella scheda **Gestisci** della funzione. Il Commuter funziona creando ed eliminando l'impostazione dell'app `AzureWebJobs.<FUNCTION_NAME>.Disabled`.

![Opzione Stato funzione](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>Altri metodi

Sebbene sia consigliabile utilizzare il metodo di impostazione dell'applicazione per tutti i linguaggi e per tutte le versioni di runtime, è possibile disabilitare le funzioni in diversi modi. Questi metodi, che variano in base al linguaggio e alla versione runtime, vengono mantenuti per compatibilità con le versioni precedenti. 

### <a name="c-class-libraries"></a>Libreria di classi C#

In una funzione di libreria di classi, è anche possibile usare l'attributo `Disable` per impedire che la funzione venga attivata. È possibile usare l'attributo senza un parametro di costruttore, come illustrato nell'esempio seguente:

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

### <a name="functions-1x---scripting-languages"></a>Funzioni 1.x: linguaggi di scripting

Nella versione 1. x, è anche possibile usare la proprietà `disabled` del file *Function. JSON* per indicare al runtime di non attivare una funzione. Questo metodo funziona solo per i linguaggi di scripting C# , ad esempio script e JavaScript. È possibile impostare la proprietà `disabled` su `true` o sul nome di un'impostazione dell'app:

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
Oppure 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

Nel secondo esempio la funzione viene disabilitata se è presente un'impostazione dell'app denominata IS_DISABLED che è impostata su `true` o 1.

È possibile modificare il file nella portale di Azure o usare l'opzione di **stato della funzione** nella scheda **Gestisci** della funzione. Il commutatore del portale funziona cambiando il file *Function. JSON* .

![Opzione Stato funzione](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo contiene informazioni sulla disabilitazione dei trigger automatici. Per altre informazioni, vedere [Trigger e associazioni](functions-triggers-bindings.md).
