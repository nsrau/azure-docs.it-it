<properties
	pageTitle="Trigger timer in Funzioni di Azure | Microsoft Azure"
	description="Informazioni su come usare trigger timer in Funzioni di Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande; glenga"/>

# Trigger timer in Funzioni di Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare trigger timer in Funzioni di Azure. Le funzioni di chiamata dei trigger timer si basano su pianificazione, tempo e ricorrenza.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## function.json per trigger timer

Il file *function.json* fornisce un'espressione di pianificazione. Ad esempio, la pianificazione seguente esegue la funzione ogni minuto:

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Il trigger timer gestisce automaticamente la scalabilità orizzontale di più istanze: solo un'unica istanza di una determinata funzione timer sarà in esecuzione in tutte le istanze.

## Formato dell'espressione schedule

L'espressione schedule è un'[espressione CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) che include 6 campi: `{second} {minute} {hour} {day} {month} {day of the week}`.

Si noti che molte espressioni cron disponibili online omettono il campo {second}, quindi se si copia il contenuto da una di queste espressioni sarà necessario inserire il campo aggiuntivo.

Di seguito sono riportati alcuni altri esempi di espressione schedule:

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

## Esempio di codice C# del trigger timer

Questo esempio di codice C# scrive un singolo log ogni volta che viene attivata la funzione.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Passaggi successivi

[AZURE.INCLUDE [Passaggi successivi](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->