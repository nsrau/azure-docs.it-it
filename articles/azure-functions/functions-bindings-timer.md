---
title: Trigger timer in Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come usare trigger timer in Funzioni di Azure.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
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
ms.author: chrande; glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2542d8c750fc7e1bcc31a9c0eb1672402facfd58
ms.openlocfilehash: 146884833e968767c14d7e4f924762a592e427e2
ms.lasthandoff: 03/01/2017


---
# <a name="schedule-code-execution-with-azure-functions"></a>Pianificare l'esecuzione di codice con Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare trigger timer in Funzioni di Azure e su come scrivere il relativo codice. Funzioni di Azure è dotata di un binding trigger timer che consente di eseguire il codice della funzione secondo una pianificazione definita. 

Il trigger timer supporta la scalabilità orizzontale a più istanze. Una singola istanza di una particolare funzione timer viene eseguita su tutte le istanze.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a id="trigger"></a>

## <a name="timer-trigger"></a>Trigger timer
Il trigger timer per una funzione usa l'oggetto JSON seguente nella matrice `bindings` di function.json:

```json
{
    "schedule": "<CRON expression - see below>",
    "name": "<Name of trigger parameter in function signature>",
    "type": "timerTrigger",
    "direction": "in"
}
```

Il valore di `schedule` è un'[espressione CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) che include i&6; campi seguenti: 

    {second} {minute} {hour} {day} {month} {day-of-week}
&nbsp;
>[!NOTE]   
>In molte delle espressioni CRON disponibili online il campo `{second}` viene omesso. Se si copia da una di esse, è necessario apportare una modifica per il campo `{second}` aggiuntivo. Per esempi specifici, vedere [Esempi di pianificazione](#examples) di seguito.

Il fuso orario predefinito usato con le espressioni CRON è Coordinated Universal Time (UTC). Per fare in modo che l'espressione CRON sia basata su un altro fuso orario, creare una nuova impostazione di app per l'app per le funzioni denominata `WEBSITE_TIME_ZONE`. Impostare il valore sul nome del fuso orario prescelto come illustrato nell'[indice dei fusi orari di Microsoft](https://msdn.microsoft.com/library/ms912391.aspx). 

Ad esempio, *Ora solare fuso orientale* (EST) è UTC-05:00. Per attivare il timer trigger ogni giorno alle 10:00 EST, usare la seguente espressione CRON che rappresenta il fuso orario UTC:

```json
"schedule": "0 0 15 * * *",
```    

In alternativa, è possibile aggiungere una nuova impostazione di app per l'app per le funzioni denominata `WEBSITE_TIME_ZONE` e impostare il valore su **Ora solare fuso orientale**.  La seguente espressione CRON può quindi essere usata per 10:00 EST: 

```json
"schedule": "0 0 10 * * *",
```    


<a name="examples"></a>

## <a name="schedule-examples"></a>Esempi di pianificazione
Di seguito sono riportati alcuni esempi di espressioni CRON che possibile usare per la proprietà `schedule`. 

Per attivare una volta ogni&5; minuti:

```json
"schedule": "0 */5 * * * *"
```

Per attivare una volta all'inizio di ogni ora:

```json
"schedule": "0 0 * * * *",
```

Per attivare una volta ogni&2; ore:

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

<a name="usage"></a>

## <a name="trigger-usage"></a>Utilizzo dei trigger
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

<a name="sample"></a>

## <a name="trigger-sample"></a>Esempio di trigger
Si supponga che il trigger timer seguente sia presente nella matrice `bindings` di function.json:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Vedere l'esempio specifico del linguaggio che legge l'oggetto timer per verificare se viene eseguito in ritardo.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Esempio di trigger in C# #
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

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Esempio di trigger in F# #
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Esempio di trigger in Node.js
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

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


