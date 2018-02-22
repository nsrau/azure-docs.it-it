---
title: Trigger timer per Funzioni di Azure
description: Informazioni su come usare trigger timer in Funzioni di Azure.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: 
ms.openlocfilehash: eeb8833470b2ba003ba74b1db57bbd2bbbb7f65d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Trigger timer per Funzioni di Azure 

Questo articolo illustra come usare trigger timer in Funzioni di Azure. Un trigger timer consente di eseguire una funzione in base a una pianificazione. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#trigger---c-example)
* [Script C# (file con estensione csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>Esempio in C#

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che viene eseguita ogni cinque minuti:

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Esempio di script C#

L'esempio seguente mostra un'associazione di trigger timer in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione scrive un log che indica se la chiamata di funzione è dovuta un'occorrenza di pianificazione mancante.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Ecco il codice script C#:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>Esempio F#

L'esempio seguente mostra un'associazione di trigger timer in un file *function.json* e una [funzione script F#](functions-reference-fsharp.md) che usa l'associazione. La funzione scrive un log che indica se la chiamata di funzione è dovuta un'occorrenza di pianificazione mancante.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Ecco il codice script F#:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>Esempio JavaScript

L'esempio seguente mostra un'associazione di trigger timer in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione scrive un log che indica se la chiamata di funzione è dovuta un'occorrenza di pianificazione mancante.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Ecco il codice script JavaScript:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes"></a>Attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs), definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions).

Il costruttore dell'attributo accetta un'espressione CRON, come illustrato nell'esempio seguente:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
   ...
}
 ```

È possibile specificare un oggetto `TimeSpan` invece di un'espressione CRON se l'app per le funzioni viene eseguita in un piano di servizio app (non un piano a consumo).

Per un esempio completo, vedere l'[esempio in C#](#c-example).

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `TimerTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su "timerTrigger". Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su "in". Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | n/d | Nome della variabile che rappresenta l'oggetto timer nel codice della funzione. | 
|**schedule**|**ScheduleExpression**|Nel piano a consumo è possibile definire le pianificazioni con un'espressione CRON. Se si usa un piano di servizio app, è anche possibile usare una stringa `TimeSpan`. Le sezioni seguenti illustrano le espressioni CRON. È possibile inserire l'espressione schedule in un'impostazione dell'app e impostare questa proprietà su un valore racchiuso tra simboli **%**, come in questo esempio: "%NomeImpostazioneAppConEspressioneCRON%". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="cron-format"></a>Formato CRON 

Un'[espressione CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) per il trigger timer di Funzioni di Azure include questi sei campi: 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>In molte delle espressioni CRON disponibili online il campo `{second}` viene omesso. Se si copia una di queste espressioni, aggiungere il campo `{second}` mancante.

### <a name="cron-time-zones"></a>Fusi orari CRON

Il fuso orario predefinito usato con le espressioni CRON è Coordinated Universal Time (UTC). Per fare in modo che l'espressione CRON sia basata su un altro fuso orario, creare una nuova impostazione di app per l'app per le funzioni denominata `WEBSITE_TIME_ZONE`. Impostare il valore sul nome del fuso orario prescelto come illustrato nell'[indice dei fusi orari di Microsoft](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx). 

Ad esempio, *Ora solare fuso orientale* (EST) è UTC-05:00. Per attivare il timer trigger ogni giorno alle 10:00 EST, usare la seguente espressione CRON che rappresenta il fuso orario UTC:

```json
"schedule": "0 0 15 * * *",
``` 

In alternativa, è possibile aggiungere una nuova impostazione di app per l'app per le funzioni denominata `WEBSITE_TIME_ZONE` e impostare il valore su **Ora solare fuso orientale**.  La seguente espressione CRON può quindi essere usata per 10:00 EST: 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>Esempi CRON

Ecco alcuni esempi di espressioni CRON che è possibile usare per il trigger timer in Funzioni di Azure. 

Per attivare una volta ogni 5 minuti:

```json
"schedule": "0 */5 * * * *"
```

Per attivare una volta all'inizio di ogni ora:

```json
"schedule": "0 0 * * * *",
```

Per attivare una volta ogni 2 ore:

```json
"schedule": "0 0 */2 * * *",
```

Per attivare una volta ogni ora dalle 9 alle 17:

```json
"schedule": "0 0 9-17 * * *",
```

Per attivare alle 9:30 ogni giorno:

```json
"schedule": "0 30 9 * * *",
```

Per attivare alle 9:30 ogni giorno feriale:

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="usage"></a>Uso

Quando viene richiamata una funzione di trigger timer, l'[oggetto timer](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) viene passato alla funzione. Il codice JSON seguente è una rappresentazione di esempio dell'oggetto timer. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

## <a name="scale-out"></a>Scalabilità orizzontale

Il trigger timer supporta la scalabilità orizzontale a più istanze. Una singola istanza di una particolare funzione timer viene eseguita su tutte le istanze.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare a una guida introduttiva che usa un trigger timer](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
