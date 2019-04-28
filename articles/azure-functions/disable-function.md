---
title: Come disabilitare le funzioni in Funzioni di Azure
description: Informazioni su come disabilitare e abilitare le funzioni in Funzioni di Azure 1.x e 2.x.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
origin.date: 07/24/2018
ms.date: 08/31/2018
ms.author: v-junlch
ms.openlocfilehash: a32b4815a2716428ceeec034ddc5589e3aa062e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710562"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Come disabilitare le funzioni in Funzioni di Azure

Questo articolo illustra come disabilitare una funzione in Funzioni di Azure. *Disabilitare* una funzione significa fare in modo che il runtime ignori il trigger automatico definito per la funzione. La modalità di disabilitazione di una funzione dipende dalla versione del runtime e dal linguaggio di programmazione:

- Funzioni 1.x
  - Linguaggi di scripting
  - Libreria di classi C#
- Funzioni 2.x
  - Una sola modalità per tutti i linguaggi
  - Una modalità facoltativa per le librerie di classi C#

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

![Opzione Stato funzione](./media/disable-function/function-state-switch.png)

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



## <a name="functions-2x---all-languages"></a>Funzioni 2.x: tutti i linguaggi

In Funzioni versione 2.x si disabilita una funzione usando un'impostazione dell'app. Ad esempio, per disabilitare una funzione denominata `QueueTrigger`, si crea un'impostazione dell'app denominata `AzureWebJobs.QueueTrigger.Disabled` e si imposta il parametro su `true`. Per abilitare la funzione, impostare il parametro su `false`. È anche possibile usare l'opzione **Stato funzione** nella scheda **Gestione** della funzione. L'opzione funziona creando ed eliminando l'impostazione dell'app `AzureWebJobs.<functionname>.Disabled`.

![Opzione Stato funzione](./media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Funzioni 2.x: librerie di classi C#

In una libreria di classi di Funzioni 2.x è consigliabile usare il metodo applicabile per tutti i linguaggi. Tuttavia, se si preferisce, è possibile [usare l'attributo Disable come in Funzioni 1.x](#functions-1x---c-class-libraries).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo contiene informazioni sulla disabilitazione dei trigger automatici. Per altre informazioni, vedere [Trigger e associazioni](functions-triggers-bindings.md).

