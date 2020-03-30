---
title: Elaborazione affidabile degli eventi di Funzioni di AzureAzure Functions reliable event processing
description: Evitare messaggi mancanti dell'Hub eventi in Funzioni di AzureAvoid missing Event Hub messages in Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: e4f35495d8a01146068cffb9159c29c46c3c0d29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561868"
---
# <a name="azure-functions-reliable-event-processing"></a>Elaborazione affidabile degli eventi di Funzioni di AzureAzure Functions reliable event processing

L'elaborazione degli eventi è uno degli scenari più comuni associati all'architettura senza server. Questo articolo descrive come creare un elaboratore di messaggi affidabile con Funzioni di Azure per evitare la perdita di messaggi.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Sfide dei flussi di eventi nei sistemi distribuiti

Si consideri un sistema che invia eventi a una velocità costante di 100 eventi al secondo. A questa velocità, in pochi minuti più istanze parallele di Functions possono utilizzare i 100 eventi in ingresso ogni secondo.

Tuttavia, una delle seguenti condizioni meno ottimali sono possibili:

- Cosa succede se l'editore di eventi invia un evento danneggiato?
- Cosa succede se l'istanza di Functions rileva eccezioni non gestite?
- Cosa succede se un sistema downstream passa offline?

Come gestire queste situazioni mantenendo la velocità effettiva dell'applicazione?

Con le code, la messaggistica affidabile viene naturale. Quando viene associata a un trigger di funzioni, la funzione crea un blocco sul messaggio della coda. Se l'elaborazione non riesce, il blocco viene rilasciato per consentire a un'altra istanza di ritentare l'elaborazione. L'elaborazione continua fino a quando il messaggio non viene valutato correttamente o viene aggiunto a una coda non elaborabile.

Anche se un singolo messaggio della coda può rimanere in un ciclo di tentativi, altre esecuzioni parallele continuano a mantenere l'eliminazione dei messaggi rimanenti. Il risultato è che la velocità effettiva complessiva rimane in gran parte inalterata da un messaggio non valido. Tuttavia, le code di archiviazione non garantiscono l'ordinamento e non sono ottimizzate per le richieste di velocità effettiva elevata richieste dagli hub eventi.

Al contrario, gli hub eventi di Azure non includono un concetto di blocco. Per consentire funzionalità come la velocità elevata, più gruppi di consumer e la capacità di riproduzione, gli eventi degli hub di eventi si comportano più come un lettore video. Gli eventi vengono letti da un singolo punto nel flusso per partizione. Dal puntatore è possibile leggere in avanti o all'indietro da tale posizione, ma è necessario scegliere di spostare il puntatore per gli eventi da elaborare.

Quando si verificano errori in un flusso, se si decide di mantenere il puntatore nello stesso punto, l'elaborazione degli eventi viene bloccata fino a quando il puntatore non viene avanzato. In altre parole, se il puntatore viene arrestato per gestire i problemi di elaborazione di un singolo evento, gli eventi non elaborati iniziano ad accumularsi.

Funzioni di Azure evita i deadlock facendo avanzare il puntatore del flusso indipendentemente dall'esito positivo o negativo. Poiché il puntatore continua ad avanzare, le funzioni devono gestire gli errori in modo appropriato.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Come Funzioni di Azure utilizza gli eventi di Hub eventiHow Azure Functions consumes Event Hubs events

Funzioni di Azure utilizza gli eventi dell'Hub eventi durante il ciclo dei passaggi seguenti:Azure Functions consumes Event Hub events while cycling through the following steps:

1. Un puntatore viene creato e reso persistente in Archiviazione di Azure per ogni partizione dell'hub eventi.
2. Quando vengono ricevuti nuovi messaggi (in un batch per impostazione predefinita), l'host tenta di attivare la funzione con il batch di messaggi.
3. Se la funzione completa l'esecuzione (con o senza eccezioni), il puntatore avanza e un checkpoint viene salvato nell'account di archiviazione.
4. Se le condizioni impediscono il completamento dell'esecuzione della funzione, l'host non riesce a far avanzare il puntatore. Se il puntatore non è avanzato, i controlli successivi finiscono per elaborare gli stessi messaggi.
5. Ripetere i passaggi da 2 a 4

Questo comportamento rivela alcuni punti importanti:This behavior reveals a few important points:

- *Eccezioni non gestite possono causare la perdita di messaggi.* Le esecuzioni che generano un'eccezione continueranno a generare un avanzamento del puntatore.
- *Funzioni garantisce la consegna almeno una volta.* Potrebbe essere necessario tenere [conto del fatto che lo stesso messaggio potrebbe essere ricevuto due volte.](./functions-idempotent.md)

## <a name="handling-exceptions"></a>Gestione delle eccezioni

Come regola generale, ogni funzione deve includere un [blocco try/catch](./functions-bindings-error-pages.md) al livello di codice più alto. In particolare, tutte le funzioni che `catch` utilizzano gli eventi di Hub eventi devono avere un blocco. In questo modo, quando viene generata un'eccezione, il blocco catch gestisce l'errore prima dell'avanzamento del puntatore.

### <a name="retry-mechanisms-and-policies"></a>Meccanismi e criteri di ripetizione dei tentativiRetry mechanisms and policies

Alcune eccezioni sono di natura transitoria e non riapparire quando un'operazione viene tentata di nuovo pochi istanti dopo. Questo è il motivo per cui il primo passaggio consiste sempre nel ritentare l'operazione. È possibile scrivere le regole di elaborazione dei tentativi manualmente, ma sono così comuni che sono disponibili numerosi strumenti. L'utilizzo di queste librerie consente di definire criteri di ripetizione dei tentativi affidabili, che consentono anche di mantenere l'ordine di elaborazione.

L'introduzione di librerie di gestione degli errori alle funzioni consente di definire criteri di ripetizione dei tentativi di base e avanzati. Ad esempio, è possibile implementare un criterio che segue un flusso di lavoro illustrato dalle regole seguenti:For example, you could implement a policy that follows a workflow illustrated by the following rules:

- Provare a inserire un messaggio tre volte (potenzialmente con un ritardo tra i tentativi).
- Se il risultato finale di tutti i tentativi è un errore, aggiungere un messaggio a una coda in modo che l'elaborazione possa continuare nel flusso.
- I messaggi danneggiati o non elaborati vengono quindi gestiti in un secondo momento.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) è un esempio di una libreria di resilienza e di gestione degli errori transitori per le applicazioni C.

Quando si utilizzano librerie di classi C , i [filtri eccezioni](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch) consentono di eseguire il codice ogni volta che si verifica un'eccezione non gestita.

Esempi che illustrano come usare i filtri eccezioni sono disponibili nel repository [azure WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki)

## <a name="non-exception-errors"></a>Errori di non eccezione

Alcuni problemi si verificano anche quando non è presente un errore. Si consideri, ad esempio, un errore che si verifica durante un'esecuzione. In questo caso, se una funzione non completa l'esecuzione, il puntatore di offset non viene mai progredito. Se il puntatore non avanza, qualsiasi istanza eseguita dopo un'esecuzione non riuscita continua a leggere gli stessi messaggi. Questa situazione fornisce una garanzia "almeno una volta".

La garanzia che ogni messaggio viene elaborato almeno una volta implica che alcuni messaggi possono essere elaborati più di una volta. Le app per le funzioni devono essere consapevoli di questa possibilità e devono essere compilate intorno ai [principi di idempotency](./functions-idempotent.md).

## <a name="stop-and-restart-execution"></a>Interrompere e riavviare l'esecuzione

Anche se alcuni errori possono essere accettabili, cosa succede se l'app si verificano errori significativi? È possibile interrompere l'attivazione degli eventi finché il sistema non raggiunge uno stato integro. Avere la possibilità di sospendere l'elaborazione si ottiene spesso con un modello di interruttore. Il modello di interruttore consente all'app di "interrompere il circuito" del processo di evento e di riprendere in un secondo momento.

Esistono due parti necessarie per implementare un interruttore in un processo di evento:There are two pieces required to implement a circuit breaker in an event process:

- Stato condiviso in tutte le istanze per tenere traccia e monitorare l'integrità del circuito
- Processo principale in grado di gestire lo stato del circuito (aperto o chiuso)

I dettagli di implementazione possono variare, ma per condividere lo stato tra istanze è necessario un meccanismo di archiviazione. È possibile scegliere di archiviare lo stato in Archiviazione di Azure, in una cache Redis o in qualsiasi altro account accessibile da una raccolta di funzioni.

[Le app per](../logic-apps/logic-apps-overview.md) la logica di Azure o [le entità durevoli](./durable/durable-functions-overview.md) sono adatte in modo naturale per gestire il flusso di lavoro e lo stato del circuito. Altri servizi possono funzionare altrettanto bene, ma le app per la logica vengono usate per questo esempio. Usando le app per la logica, è possibile sospendere e riavviare l'esecuzione di una funzione, fornendo il controllo necessario per implementare il modello di interruttore.

### <a name="define-a-failure-threshold-across-instances"></a>Definire una soglia di errore tra le istanzeDefine a failure threshold across instances

Per tenere conto di più istanze che elaborano gli eventi contemporaneamente, è necessario rendere persistente lo stato esterno condiviso per monitorare l'integrità del circuito.

Una regola che è possibile scegliere di implementare potrebbe imporre che:A rule you may choose to implement might enforce that:

- Se sono presenti più di 100 errori entro 30 secondi in tutte le istanze, interrompere il circuito e interrompere l'attivazione dei nuovi messaggi.

I dettagli di implementazione variano in base alle proprie esigenze, ma in generale è possibile creare un sistema che:The implementation details will vary given your needs, but in general you can create a system that:

1. Registrare gli errori in un account di archiviazione (Archiviazione di Azure, Redis e così via)
1. Quando viene registrato un nuovo errore, controllare il conteggio in sequenza per verificare se la soglia è soddisfatta (ad esempio, più di 100 negli ultimi 30 secondi).
1. Se viene raggiunta la soglia, generare un evento in Griglia di eventi di Azure indicando al sistema di interrompere il circuito.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Gestione dello stato del circuito con le app per la logica di AzureManaging circuit state with Azure Logic Apps

La descrizione seguente evidenzia un modo per creare un'app per la logica di Azure per arrestare un'app Funzioni dall'elaborazione.

App per la logica di Azure include connettori incorporati a diversi servizi, orchestrazioni con stato delle funzionalità ed è una scelta naturale per gestire lo stato del circuito. Dopo aver rilevato la necessità di interruzione del circuito, è possibile creare un'app per la logica per implementare il flusso di lavoro seguente:After detecting the circuit needs to break, you can build a logic app to implement the following workflow:

1. Attivare un flusso di lavoro Griglia di eventi e arrestare la funzione di Azure (con il connettore di risorse di Azure)Trigger an Event Grid workflow and stop the Azure Function (with the Azure Resource connector)
1. Inviare un messaggio di posta elettronica di notifica che include un'opzione per riavviare il flusso di lavoroSend a notification email that includes an option to restart the workflow

Il destinatario dell'e-mail può esaminare l'integrità del circuito e, se necessario, riavviare il circuito tramite un collegamento nell'e-mail di notifica. Quando il flusso di lavoro riavvia la funzione, i messaggi vengono elaborati dall'ultimo checkpoint dell'Hub eventi.

Utilizzando questo approccio, nessun messaggio viene perso, tutti i messaggi vengono elaborati in ordine ed è possibile interrompere il circuito per tutto il tempo necessario.

## <a name="resources"></a>Risorse

- [Esempi di elaborazione di eventi affidabili](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Interruttore di circuito per le funzioni durevoli di AzureAzure Durable Functions Circuit Breaker](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

- [Gestione degli errori di Funzioni di Azure](./functions-bindings-error-pages.md)
- [Automatizzare il ridimensionamento delle immagini caricate utilizzando Griglia di eventi](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Creare una funzione che si integra con le app per la logica di Azure](./functions-twitter-email.md)
