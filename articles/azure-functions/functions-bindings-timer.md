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
	ms.date="05/16/2016"
	ms.author="chrande"/>

# Trigger timer in Funzioni di Azure

Questo articolo illustra come configurare trigger timer in Funzioni di Azure. Le funzioni di chiamata dei trigger timer si basano su pianificazione, tempo e ricorrenza.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## function.json per trigger timer

Il file *function.json* specifica un'espressione schedule e un'opzione che indica se la funzione deve essere attivata subito.

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
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

L'espressione schedule può essere un'[espressione CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) che include 6 campi: {secondo} {minuto} {ora} {giorno} {mese} {giorno della settimana}. In molti documenti con l'espressione cron disponibili online viene omesso il campo {secondo}, quindi, se si copia il contenuto da uno di questi documenti, sarà necessario inserire il campo aggiuntivo.

L'espressione schedule può essere anche nel formato *hh:mm:ss* per specificare il ritardo tra ogni attivazione della funzione.

Ecco alcuni esempi di espressione schedule.

Per attivare una volta ogni 5 minuti:

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

Per attivare immediatamente e poi ogni due ore:

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

Per attivare ogni 15 secondi:

```json
"schedule": "00:00:15",
"runOnStartup": false,
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

<!---HONumber=AcomDC_0525_2016-->