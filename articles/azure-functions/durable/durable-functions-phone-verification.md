---
title: Interazione umana e timeout in Funzioni permanenti - Azure
description: Informazioni su come gestire l'interazione umana e i timeout nell'estensione Funzioni permanenti per Funzioni di Azure.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4e0f71369bc02fdce5625d9c74e1d52264ed86be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335755"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Interazione umana in Funzioni permanenti - Esempio di verifica telefonica

Questo esempio illustra come creare un'orchestrazione di [Funzioni permanenti](durable-functions-overview.md) che prevede interazione umana. Ogni volta che una persona reale è coinvolta in un processo automatizzato, il processo deve essere in grado di inviare notifiche alla persona e di ricevere risposte in modo asincrono. È inoltre necessario consentire la possibilità che la persona non sia disponibile. In questa parte i timeout diventano importanti.

L'esempio implementa un sistema di verifica telefonica basata su SMS. Questi tipi di flussi vengono spesso usati quando si verifica il numero di telefono di un cliente o per l'autenticazione a più fattori. Si tratta di un esempio potente perché l'intera implementazione viene eseguita utilizzando un paio di piccole funzioni. Non è necessario alcun archivio dati esterno, ad esempio un database.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Panoramica dello scenario

La verifica telefonica viene usata per assicurarsi che gli utenti finali dell'applicazione non siano spammer e che siano effettivamente chi affermano di essere. L'autenticazione a più fattori è un metodo di uso comune per proteggere gli account utente da pirati informatici. Il problema nell'implementazione di una verifica telefonica consiste nella necessità di un'**l'interazione con stato** con una persona fisica. A un utente finale viene in genere inviato un codice, ad esempio un numero di 4 cifre, e l'utente deve rispondere **in un intervallo di tempo ragionevole**.

Funzioni di Azure è un servizio normalmente senza stato (come molti altri endpoint cloud su altre piattaforme), quindi questi tipi di interazioni comportano la gestione esplicita di uno stato esternamente, ad esempio in un database o in un altro archivio permanente. L'interazione deve essere anche suddivisa in più funzioni che possono essere coordinate tra loro. È necessario ad esempio disporre almeno di una funzione per la scelta di un codice, la permanenza in un punto e l'invio al telefono dell'utente. È necessaria anche almeno un'altra funzione per ricevere una risposta da parte dell'utente e associarla alla chiamata di funzione originale al fine di convalidare il codice. Un timeout è un aspetto importante per garantire la protezione. Può diventare abbastanza complesso rapidamente.

La complessità dello scenario viene notevolmente ridotta grazie all'uso di Funzioni permanenti. Come si vedrà in questo esempio, una funzione dell'agente di orchestrazione può gestire l'interazione con stato in modo semplice e senza coinvolgere alcun archivio dati esterno. Poiché le funzioni dell'agente di orchestrazione sono *permanenti*, questi flussi interattivi sono anche estremamente affidabili.

## <a name="configuring-twilio-integration"></a>Configurazione dell'integrazione di Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funzioni

L'articolo illustra le funzioni seguenti nell'app di esempio:

* `E4_SmsPhoneVerification`: [funzione dell'agente](durable-functions-bindings.md#orchestration-trigger) di orchestrazione che esegue il processo di verifica del telefono, inclusa la gestione dei timeout e dei tentativi.
* `E4_SendSmsChallenge`: [funzione](durable-functions-bindings.md#activity-trigger) di attività che invia un codice tramite SMS.

### <a name="e4_smsphoneverification-orchestrator-function"></a>E4_SmsPhoneVerification funzione dell'agente di orchestrazione

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Anche se non è evidente, questa funzione dell'agente di orchestrazione è completamente deterministica. È deterministico `CurrentUtcDateTime` perché la proprietà viene utilizzata per calcolare l'ora di scadenza del timer e restituisce lo stesso valore a ogni riproduzione a questo punto nel codice dell'agente di orchestrazione. Questo comportamento è importante per `winner` garantire che gli `Task.WhenAny`stessi risultati da ogni chiamata ripetuta a .

# <a name="javascript"></a>[Javascript](#tab/javascript)

La funzione **E4_SmsPhoneVerification** usa il codice *function.json* standard per le funzioni dell'agente di orchestrazione.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Di seguito è riportato il codice che implementa la funzione:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Anche se non è evidente, questa funzione dell'agente di orchestrazione è completamente deterministica. È deterministico `currentUtcDateTime` perché la proprietà viene utilizzata per calcolare l'ora di scadenza del timer e restituisce lo stesso valore a ogni riproduzione a questo punto nel codice dell'agente di orchestrazione. Questo comportamento è importante per `winner` garantire che gli `context.df.Task.any`stessi risultati da ogni chiamata ripetuta a .

---

Dopo l'avvio, le operazioni di questa funzione dell'agente di orchestrazione sono le seguenti:

1. Acquisizione di un numero di telefono a cui *inviare* la notifica SMS.
2. Chiamata a **E4_SendSmsChallenge** per inviare un messaggio SMS all'utente e restituzione del codice di autenticazione di 4 cifre previsto.
3. Creazione di un timer permanente che attivi un intervallo di 90 secondi a partire dal momento corrente.
4. In parallelo con il timer, attesa di un evento **SmsChallengeResponse** da parte dell'utente.

L'utente riceve un messaggio SMS con un codice di quattro cifre Hanno 90 secondi per inviare lo stesso codice a quattro cifre all'istanza della funzione dell'agente di orchestrazione per completare il processo di verifica. Se invia il codice non corretto, l'utente ha a disposizione altri tre tentativi (all'interno dello stesso intervallo di 90 secondi).

> [!WARNING]
> È importante [annullare i timer](durable-functions-timers.md) se non è più necessario che scadano, come illustrato nell'esempio precedente, quando viene accettata una risposta alla richiesta.

## <a name="e4_sendsmschallenge-activity-function"></a>Funzione di attività E4_SendSmsChallenge

La funzione **E4_SendSmsChallenge** utilizza l'associazione Twilio per inviare il messaggio SMS con il codice a quattro cifre all'utente finale.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> È necessario installare `Microsoft.Azure.WebJobs.Extensions.Twilio` il pacchetto Nuget per eseguire il codice di esempio.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Il codice *function.json* viene definito come segue:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

Ed ecco il codice che genera il codice di verifica a quattro cifre e invia il messaggio SMS:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

---

## <a name="run-the-sample"></a>Eseguire l'esempio

Con le funzioni attivate da HTTP incluse nell'esempio, è possibile avviare l'orchestrazione inviando la richiesta HTTP POST seguente:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

La funzione dell'agente di orchestrazione riceve il numero di telefono indicato e invia immediatamente un messaggio SMS con un codice di verifica di 4 cifre generato casualmente &mdash; ad esempio *2168*. La funzione attende quindi 90 secondi per ricevere una risposta.

Per rispondere con il codice, è possibile utilizzare `{eventName}` `SmsChallengeResponse` [ `RaiseEventAsync` (.NET) o `raiseEvent` (JavaScript)](durable-functions-instance-management.md) all'interno di un'altra funzione o richiamare il webhook HTTP POST **sendEventUrl** a cui si fa riferimento nella risposta 202 precedente, sostituendo con il nome dell'evento, :

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Se si esegue l'invio prima della scadenza del timer, l'orchestrazione viene completato e il campo `output` viene impostato su `true`, che indica un esito positivo della verifica.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Se si lascia che il timer scada o se si immette il codice errato quattro volte, è possibile eseguire una query sullo stato e visualizzare un output `false` della funzione di orchestrazione, che indica che la verifica telefonica non è riuscita.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>Passaggi successivi

In questo esempio sono stati dimostrati alcuni `WaitForExternalEvent` delle `CreateTimer` funzionalità avanzate di funzioni durevoli, in particolare e API. È stato illustrato come queste funzionalità possono essere combinate con `Task.WaitAny` per implementare un sistema di timeout affidabile, spesso utile per l'interazione con utenti reali. Per altre informazioni su come usare Funzioni permanenti, fare riferimento alla serie di articoli in cui sono trattati in dettaglio argomenti specifici.

> [!div class="nextstepaction"]
> [Passare al primo articolo della serie](durable-functions-bindings.md)
