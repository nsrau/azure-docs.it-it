---
title: Come disabilitare le funzioni in Funzioni di Azure
description: Informazioni su come disabilitare e abilitare le funzioni in Funzioni di Azure.Learn how to disable and enable functions in Azure Functions.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 11585e92e7d239731b02d06c5093f979cd65cfba
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686894"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Come disabilitare le funzioni in Funzioni di Azure

Questo articolo illustra come disabilitare una funzione in Funzioni di Azure. *Disabilitare* una funzione significa fare in modo che il runtime ignori il trigger automatico definito per la funzione. Ciò consente di impedire l'esecuzione di una funzione specifica senza arrestare l'intera app per le funzioni.

Il modo consigliato per disabilitare una funzione consiste `AzureWebJobs.<FUNCTION_NAME>.Disabled`nell'utilizzare un'impostazione dell'app nel formato . È possibile creare e modificare l'impostazione dell'applicazione in diversi modi, ad esempio usando [l'interfaccia della riga di comando](/cli/azure/) di Azure e dalla scheda **Gestisci** della funzione nel portale di [Azure.](https://portal.azure.com) 

> [!NOTE]  
> Quando si disabilita una funzione attivata HTTP utilizzando i metodi descritti in questo articolo, l'endpoint potrebbe comunque essere accessibile durante l'esecuzione nel computer locale.  

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

Nell'interfaccia della riga [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) di comando di Azure usare il comando per creare e modificare l'impostazione dell'app. Il comando seguente disabilita `QueueTrigger` una funzione denominata `AzureWebJobs.QueueTrigger.Disabled` creando `true`un'impostazione dell'app denominata set it to . 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Per riattivare la funzione, eseguire nuovamente lo `false`stesso comando con un valore di .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Utilizzare il portale

È inoltre possibile utilizzare l'opzione **Stato funzione** nella scheda **Gestisci** della funzione. L'opzione funziona creando `AzureWebJobs.<FUNCTION_NAME>.Disabled` ed eliminando l'impostazione dell'app.

![Opzione Stato funzione](media/disable-function/function-state-switch.png)

> [!NOTE]  
> La funzionalità di test integrata nel portale ignora l'impostazione. `Disabled` Ciò significa che una funzione disabilitata viene comunque eseguita quando viene avviata dalla finestra **Test** nel portale. 

## <a name="other-methods"></a>Altri metodi

Mentre il metodo di impostazione dell'applicazione è consigliato per tutte le lingue e tutte le versioni di runtime, esistono diversi altri modi per disabilitare le funzioni. Questi metodi, che variano in base al linguaggio e alla versione di runtime, vengono mantenuti per garantire la compatibilità con le versioni precedenti. 

### <a name="c-class-libraries"></a>Libreria di classi C#

In una funzione della libreria di `Disable` classi, è anche possibile utilizzare l'attributo per impedire l'attivazione della funzione. È possibile usare l'attributo senza un parametro di costruttore, come illustrato nell'esempio seguente:

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

Nella versione 1.x, è `disabled` anche possibile usare la proprietà del file *function.json* per indicare al runtime di non attivare una funzione. Questo metodo funziona solo per i linguaggi di script, ad esempio script c'è e JavaScript. La `disabled` proprietà può `true` essere impostata su o sul nome di un'impostazione dell'app:

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
o 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

Nel secondo esempio la funzione viene disabilitata se è presente un'impostazione dell'app denominata IS_DISABLED che è impostata su `true` o 1.

È possibile modificare il file nel portale di Azure o usare l'opzione **Stato funzione** nella scheda **Gestisci** della funzione. L'opzione del portale funziona modificando il file *function.json.*

![Opzione Stato funzione](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo contiene informazioni sulla disabilitazione dei trigger automatici. Per altre informazioni, vedere [Trigger e associazioni](functions-triggers-bindings.md).
