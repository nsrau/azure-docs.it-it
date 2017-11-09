---
title: Controllo delle versioni in Funzioni permanenti - Azure
description: Informazioni su come implementare il controllo delle versioni nell'estensione Funzioni permanenti per Funzioni di Azure.
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
ms.openlocfilehash: 0a86e4a87f5ec23c284aa4e5cfb2c67622b3ebe9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Controllo delle versioni in Funzioni permanenti (Funzioni di Azure)

Nella durata di un'applicazione è comune che vengano aggiunte, rimosse e modificate funzioni. [Funzioni permanenti](durable-functions-overview.md) consente di concatenare diverse funzioni in modi non possibili in precedenza, influenzando in tal modo la gestione del controllo delle versioni.

## <a name="how-to-handle-breaking-changes"></a>Come gestire le modifiche di rilievo

Sono disponibili alcuni esempi di modifiche di rilievo che è necessario tenere presenti. Questo articolo illustra i più comuni, basati tutti sul fatto che tutte le funzioni di orchestrazione, sia nuove che esistenti, sono interessate dalle modifiche apportate al codice di funzione.

### <a name="changing-activity-function-signatures"></a>Modifica delle firme delle funzioni di attività

Una modifica della firma fa riferimento a una modifica di nome, input oppure output di una funzione. Se questo tipo di modifica viene apportato a una funzione di attività, potrebbe interrompere la funzione dell'agente di orchestrazione dipendente. Se si aggiorna la funzione dell'agente di orchestrazione per gestire questa modifica, si potrebbero interrompere le istanze esistenti in corso.

Si supponga ad esempio di disporre della funzione seguente.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Questa semplice funzione usa i risultati di **Foo** e li passa a **Bar**. Si supponga che sia necessario modificare il valore restituito di **Foo** da `bool` a `int` per supportare un'ampia gamma di valori di risultati. Il risultato sarà analogo a questo:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Questa modifica funziona correttamente per tutte le nuove istanze della funzione dell'agente di orchestrazione, ma interrompe tutte le istanze in corso. Si consideri ad esempio il caso in cui un'istanza di orchestrazione chiama **Foo** e riceve un valore booleano e quindi i checkpoint. Se la modifica della firma viene distribuita a questo punto, l'istanza per cui è stato applicato il checkpoint ha immediatamente esito negativo quando riprende e riesegue la chiamata a `context.CallActivityAsync<int>("Foo")`. Ciò si verifica perché il risultato nella tabella di cronologia è `bool`, ma il nuovo codice tenta di deserializzarlo in `int`.

Questo è solo uno dei molti modi diversi in cui una modifica della firma può interrompere le istanze esistenti. In generale, se è necessario modificare il modo in cui un agente di orchestrazione chiama una funzione, la modifica può diventare un problema.

### <a name="changing-orchestrator-logic"></a>Modifica della logica dell'agente di orchestrazione

L'altra classe di problemi di controllo delle versioni deriva dalla modifica del codice di funzione dell'agente di orchestrazione in un modo che disorienta la logica di riesecuzione per le istanze in corso.

Si consideri la funzione dell'agente di orchestrazione seguente:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Si supponga ora che si desideri apportare una modifica apparentemente innocua per aggiungere un'altra chiamata di funzione.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Questa modifica aggiunge una chiamata di funzione a **SendNotification** tra **Foo** e **Bar**. Non sono presenti modifiche della firma. Il problema si verifica quando un'istanza esistente riprende dopo la chiamata a **Bar**. Durante la riesecuzione, se la chiamata originale a **Foo** ha restituito `true` la riesecuzione dell'agente di orchestrazione eseguirà la chiamata a **SendNotification** che non si trova nella cronologia di esecuzione. Di conseguenza, il framework di attività permanenti ha esito negativo e genera un'eccezione `NonDeterministicOrchestrationException` perché ha rilevato una chiamata a **SendNotification** quando era prevista la visualizzazione di una chiamata a **Bar**.

## <a name="mitigation-strategies"></a>Strategie di mitigazione

Di seguito vengono indicate alcune strategie per affrontare le problematiche di controllo delle versioni.

* Non eseguire alcuna operazione
* Arrestare tutte le istanze in corso
* Eseguire distribuzioni side-by-side

### <a name="do-nothing"></a>Non eseguire alcuna operazione

Il modo più semplice per gestire una modifica di rilievo è quello di consentire che le istanze di orchestrazione in corso abbiano esito negativo. Le nuove istanze eseguono correttamente il codice modificato.

L'eventuale presenza di un problema dipende dall'importanza delle istanze in corso. In fase di sviluppo attivo e quando la presenza di istanze in corso non è importante, questa situazione potrebbe essere corretta. È necessario tuttavia gestire eccezioni ed errori nella pipeline di diagnostica. Se si desidera evitare queste situazioni, prendere in considerazione le altre opzioni di controllo delle versioni.

### <a name="stop-all-in-flight-instances"></a>Arrestare tutte le istanze in corso

Un'altra opzione è quella di arrestare tutte le istanze in corso. Questa operazione può essere eseguita cancellando il contenuto della **coda di controllo** e della **coda di elementi di lavoro**. Le istanze verranno bloccate nel punto in cui si trovano, ma i dati di telemetria non verranno confusi con messaggi di errore. Questa soluzione è ideale in fase di sviluppo rapido di prototipi.

> [!WARNING]
> I dettagli di queste code possono cambiare nel tempo, pertanto non è consigliabile basarsi su questa tecnica per i carichi di lavoro di produzione.

### <a name="side-by-side-deployments"></a>Eseguire distribuzioni side-by-side

Il modo migliore per garantire che le modifiche di rilievo vengano distribuite in modo sicuro è quello di eseguire una distribuzione side-by-side con le versioni precedenti. Questa operazione può essere eseguita tramite una delle tecniche seguenti:

* Distribuire tutti gli aggiornamenti come funzioni completamente nuove (nuovi nomi).
* Distribuire tutti gli aggiornamenti come una nuova app per le funzioni con un account di archiviazione diverso.
* Distribuire una nuova copia dell'app per le funzioni, ma con un nome `TaskHub` aggiornato. Questa è la tecnica consigliata.

### <a name="how-to-change-task-hub-name"></a>Come modificare il nome dell'hub attività

L'hub attività può essere configurato nel file *host.json* come indicato di seguito:

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

Il valore predefinito è `DurableFunctionsHub`.

Tutte le entità di Archiviazione di Azure sono denominate in base al valore di configurazione `HubName`. Se si assegna un nuovo nome all'hub attività, verificare che per la nuova versione dell'applicazione vengano create code separate e la tabella di cronologia.

È consigliabile distribuire la nuova versione dell'app per le funzioni a un nuovo [slot di distribuzione](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Gli slot di distribuzione consentono di eseguire side-by-side più copie dell'app per le funzioni con un solo slot come slot di *produzione* attivo. Quando è possibile esporre la nuova logica di orchestrazione all'infrastruttura esistente, l'operazione può essere semplice come la sostituzione della nuova versione nello slot di produzione.

> [!NOTE]
> Questa strategia funziona meglio quando si usano trigger HTTP e di webhook per le funzioni dell'agente di orchestrazione. Per i trigger non HTTP, ad esempio code o hub eventi, la definizione del trigger deve derivare da un'impostazione dell'app che viene aggiornata come parte dell'operazione di sostituzione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come gestire i problemi di prestazioni e di scalabilità](durable-functions-perf-and-scale.md)
