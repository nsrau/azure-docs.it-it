---
title: Trigger timer per Funzioni di Azure
description: Informazioni su come usare trigger timer in Funzioni di Azure.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 566d6ccf43024692e19bcd6639fe5cfbbba0660d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056417"
---
# <a name="timer-trigger-for-azure-functions"></a>Trigger timer per Funzioni di Azure 

Questo articolo illustra come usare trigger timer in Funzioni di Azure. Un trigger timer consente di eseguire una funzione in base a una pianificazione. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Il trigger timer è disponibile nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) versione 2.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacchetti - Funzioni 2.x e successive

Il trigger timer è disponibile nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) versione 3.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Esempio

# <a name="c"></a>[C #](#tab/csharp)

Nell'esempio seguente viene illustrata una [funzione di C,](functions-dotnet-class-library.md) che viene eseguita ogni volta che i minuti hanno un valore divisibile per cinque (ad esempio, se la funzione inizia alle 18:57:00, le prestazioni successive saranno alle 19:00:00). L'oggetto [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) viene passato alla funzione.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L'esempio seguente mostra un'associazione di trigger timer in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione scrive un log che indica se la chiamata di funzione è dovuta un'occorrenza di pianificazione mancante. L'oggetto [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) viene passato alla funzione.

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
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

L'esempio seguente mostra un'associazione di trigger timer in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione scrive un log che indica se la chiamata di funzione è dovuta un'occorrenza di pianificazione mancante. Un [oggetto timer](#usage) viene passato alla funzione.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Ecco il codice JavaScript:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene utilizzata un'associazione del trigger timer la cui configurazione è descritta nel file *function.json.* La [funzione Python](functions-reference-python.md) effettiva che utilizza l'associazione è descritta nel file *.py __init.__* L'oggetto passato alla funzione è di tipo [azure.functions.TimerRequest .](/python/api/azure-functions/azure.functions.timerrequest) La logica della funzione scrive nei log indicando se la chiamata corrente è dovuta a un'occorrenza di pianificazione persa. 

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Ecco il codice Python:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

# <a name="java"></a>[Java](#tab/java)

La funzione di esempio seguente si attiva e viene eseguita ogni cinque minuti. L’`@TimerTrigger`annotazione sulla funzione definisce la pianificazione usando lo stesso formato di stringa delle [espressioni CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C #](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Il costruttore dell'attributo accetta un'espressione CRON o un valore `TimeSpan`. È possibile `TimeSpan` usare solo se l'app per le funzioni è in esecuzione in un piano di servizio app. `TimeSpan`non è supportato per le funzioni Consumo o Elastic Premium.

L'esempio seguente mostra un'espressione CRON:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script di C.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

L’`@TimerTrigger`annotazione sulla funzione definisce la pianificazione usando lo stesso formato di stringa delle [espressioni CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente vengono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `TimerTrigger` .

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su "timerTrigger". Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su "in". Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**name** | n/d | Nome della variabile che rappresenta l'oggetto timer nel codice della funzione. | 
|**schedule**|**ScheduleExpression**|[Espressione CRON](#ncrontab-expressions) o valore [TimeSpan](#timespan). `TimeSpan` può essere usato solo per un'app per le funzioni in esecuzione in un piano di servizio app. Puoi inserire l'espressione di pianificazione in un'impostazione dell'app e impostare questa proprietà sul nome dell'impostazione dell'app racchiusa tra **%** segni, come in questo esempio: "%ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Se `true`, la funzione viene richiamata all'avvio del runtime. Ad esempio, il runtime viene avviato quando l'app per le funzioni si riattiva dopo un periodo di inattività, quando l'app per le funzioni viene riavviata a causa di modifiche alle funzioni e quando l'app per le funzioni viene ridimensionata. Così **runOnStartup** dovrebbe raramente `true`se mai essere impostato su , soprattutto in produzione. |
|**useMonitor**|**UseMonitor**|Impostata su `true` o `false` per indicare se monitorare la pianificazione. Il monitoraggio della pianificazione rende persistenti le occorrenze della pianificazione per garantire che la pianificazione venga gestita correttamente anche quando le istanze dell'app per le funzioni vengono riavviate. Se non è impostato `true` in modo esplicito, l'impostazione predefinita è per le pianificazioni con un intervallo di ricorrenza maggiore o uguale a 1 minuto. Per le pianificazioni attivate più di una volta al minuto, il valore predefinito è `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> L'impostazione di **runOnStartup** su `true` non è consigliata in ambienti di produzione. Con questa impostazione, il codice viene eseguito in momenti estremamente imprevedibili. In alcune impostazioni di produzione queste esecuzioni aggiuntive possono determinare costi molto più elevati per le app ospitate in piani a consumo. Ad esempio, con **runOnStartup** abilitato il trigger viene richiamato ogni volta che l'app per le funzioni viene ridimensionata. Prima di abilitare **runOnStartup** in un ambiente di produzione assicurarsi di avere ben compreso il comportamento in produzione delle proprie funzioni.   

## <a name="usage"></a>Uso

Quando viene richiamata una funzione trigger timer, un oggetto timer viene passato alla funzione. Il codice JSON seguente è una rappresentazione di esempio dell'oggetto timer.

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

La proprietà `IsPastDue` è `true` quando la chiamata della funzione corrente avviene successivamente al momento pianificato. Ad esempio, un riavvio dell'app per le funzioni può causare la mancata riuscita di una chiamata.

## <a name="ncrontab-expressions"></a>Espressioni NCRONTAB 

Funzioni di Azure usa la libreria [NCronTab](https://github.com/atifaziz/NCrontab) per interpretare le espressioni NCRONTAB. Un'espressione NCRONTAB è simile a un'espressione CRON, ad eccezione del fatto che include un sesto campo aggiuntivo all'inizio da utilizzare per la precisione temporale in secondi:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Ogni campo può avere uno dei tipi di valori seguenti:

|Type  |Esempio  |Quando viene attivato  |
|---------|---------|---------|
|Valore specifico |<nobr>"0 5 * * * *"</nobr>|Alle hh.05.00, dove hh corrisponde a ogni ora (una volta all'ora)|
|Tutti i valori (`*`)|<nobr>"0 * 5 * * *"</nobr>|Alle 5.mm.00 ogni giorno, dove mm è ogni minuto dell'ora (60 volte al giorno)|
|Intervallo (operatore `-`)|<nobr>"5-7 * * * * *"</nobr>|Alle hh.mm.05, hh.mm.06 e hh.mm.07, dove hh.mm è ogni minuto di ogni ora (3 volte al minuto)|
|Set di valori (operatore `,`)|<nobr>"5,8,10 * * * * *"</nobr>|Alle hh.mm.05, hh.mm.08 e hh.mm.10, dove hh.mm è ogni minuto di ogni ora (3 volte al minuto)|
|Valore di intervallo (operatore `/`)|<nobr>"0 */5 * * * *"</nobr>|all'hh:00:00, hh:05:00, hh:10:00 e così via fino a hh:55:00 dove hh è ogni ora (12 volte all'ora)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Esempi di NCRONTAB

Ecco alcuni esempi di espressioni NCRONTAB che è possibile usare per il trigger del timer in Funzioni di Azure.Here are some examples of NCRONTAB expressions you can use for the timer trigger in Azure Functions.

|Esempio|Quando viene attivato  |
|---------|---------|
|`"0 */5 * * * *"`|Una volta ogni cinque minuti|
|`"0 0 * * * *"`|Una volta all'inizio di ogni ora|
|`"0 0 */2 * * *"`|Una volta ogni due ore|
|`"0 0 9-17 * * *"`|Una volta ogni ora dalle 9 alle 17|
|`"0 30 9 * * *"`|Alle 9.30 di ogni giorno|
|`"0 30 9 * * 1-5"`|Alle 9.30 di ogni giorno feriale|
|`"0 30 9 * Jan Mon"`|Alle 9.30 di ogni lunedì di gennaio|


### <a name="ncrontab-time-zones"></a>Fusi orari NCRONTAB

I numeri in un'espressione CRON fanno riferimento a una data e a un'ora e non a un intervallo di tempo. Ad esempio, il valore 5 nel campo `hour` fa riferimento alle 5.00 e non a ogni 5 ore.

Il fuso orario predefinito usato con le espressioni CRON è Coordinated Universal Time (UTC). Per fare in modo che l'espressione CRON sia basata su un altro fuso orario, creare un'impostazione per l'app per le funzioni denominata `WEBSITE_TIME_ZONE`. Impostare il valore sul nome del fuso orario prescelto come illustrato nell'[indice dei fusi orari di Microsoft](https://technet.microsoft.com/library/cc749073).

  > [!NOTE]
  > `WEBSITE_TIME_ZONE`non è attualmente supportato nel piano di consumo Linux.

Ad esempio, *Ora solare fuso orientale* (EST) è UTC-05:00. Per attivare il timer alle 10:00 AM EST ogni giorno, utilizzare la seguente espressione NCRONTAB che tiene conto del fuso orario UTC:

```
"0 0 15 * * *"
``` 

In alternativa, è possibile creare un'impostazione per l'app per le funzioni denominata `WEBSITE_TIME_ZONE` e impostare il valore su **Ora solare fuso orientale**.  Utilizza quindi la seguente espressione NCRONTAB: 

```
"0 0 10 * * *"
``` 

Quando si usa `WEBSITE_TIME_ZONE`, l'ora viene regolata per modifiche all'ora nel fuso orario specifico, ad esempio l'ora legale. 

## <a name="timespan"></a>TimeSpan

 `TimeSpan` può essere usato solo per un'app per le funzioni in esecuzione in un piano di servizio app.

Diversamente da un'espressione CRON, un valore `TimeSpan` specifica l'intervallo di tempo tra ogni chiamata di funzione. Quando una funzione viene completata dopo essere stata eseguita più a lungo dell'intervallo specificato, il timer richiama immediatamente di nuovo la funzione.

Espresso come stringa, il formato di `TimeSpan` è `hh:mm:ss`, dove `hh` è minore di 24. Quando le prime due cifre sono un numero uguale o maggiore di 24, il formato è `dd:hh:mm`. Di seguito sono riportati alcuni esempi:

|Esempio |Quando viene attivato  |
|---------|---------|
|"01:00:00" | Ogni ora        |
|"00:01:00"|Ogni minuto         |
|"24:00:00" | Ogni 24 giorni        |
|"1.00:00:00" | Ogni giorno        |

## <a name="scale-out"></a>Scalabilità orizzontale

Se un'app per le funzioni viene scalata orizzontalmente a più istanze, viene eseguita un'unica istanza di una funzione attivata dal timer in tutte le istanze.

## <a name="function-apps-sharing-storage"></a>App per le funzioni che condividono un account di archiviazione

Se si condividono account di archiviazione tra app per le funzioni non distribuite nel servizio app, potrebbe essere necessario assegnare in modo esplicito l'ID host a ogni app.

| Versione di Funzioni | Impostazione                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x (e versioni successive)  | La variabile di ambiente `AzureFunctionsWebHost__hostid` |
| 1.x               | `id`in *host.json*                                  |

È possibile omettere il valore di identificazione o impostare manualmente la configurazione di identificazione di ogni app per le funzioni su un valore diverso.

Il trigger del timer usa un blocco di archiviazione per garantire che sia presente una sola istanza del timer quando un'app per le funzioni viene ridimensionata in più istanze. Se due app per le funzioni condividono la stessa configurazione di identificazione e ognuna usa un trigger timer, viene eseguito un solo timer.

## <a name="retry-behavior"></a>Comportamento in caso di nuovo tentativo

Diversamente dal trigger di coda, il trigger timer non viene ripetuto se una funzione non riesce. Quando una funzione non riesce, non viene chiamata di nuovo fino alla volta successiva nella pianificazione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per informazioni su cosa fare quando il trigger timer non funziona come previsto, vedere [Investigating and reporting issues with timer triggered functions not firing](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing) (Analisi e segnalazione di problemi quando non vengono eseguite le funzioni attivate da timer).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare a una guida introduttiva che usa un trigger timer](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
