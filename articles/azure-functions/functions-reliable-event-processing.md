---
title: Elaborazione di eventi affidabili di funzioni di Azure
description: Evitare i messaggi dell'hub eventi mancanti in funzioni di Azure
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: d38ef46abae12886fb04a30f5efc26992cde4443
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955536"
---
# <a name="azure-functions-reliable-event-processing"></a>Elaborazione di eventi affidabili di funzioni di Azure

L'elaborazione degli eventi è uno degli scenari più comuni associati all'architettura senza server. Questo articolo descrive come creare un elaboratore di messaggi affidabile con funzioni di Azure per evitare la perdita di messaggi.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Problemi relativi ai flussi di eventi nei sistemi distribuiti

Si consideri un sistema che invia eventi a una frequenza costante di 100 eventi al secondo. A questa velocità, in pochi minuti più istanze di funzioni parallele possono utilizzare gli eventi 100 in ingresso ogni secondo.

Tuttavia, è possibile che siano soddisfatte le condizioni meno ottimali seguenti:

- Cosa accade se l'autore di eventi Invia un evento danneggiato?
- Cosa accade se l'istanza di funzioni rileva eccezioni non gestite?
- Che cosa succede se un sistema downstream passa alla modalità offline?

In che modo è possibile gestire queste situazioni conservando la velocità effettiva dell'applicazione?

Con le code, la messaggistica affidabile è naturalmente. Una volta abbinato a un trigger di funzioni, la funzione crea un blocco sul messaggio della coda. Se l'elaborazione non riesce, il blocco viene rilasciato per consentire a un'altra istanza di ritentare l'elaborazione. L'elaborazione continua fino a quando il messaggio non viene valutato correttamente o viene aggiunto a una coda non elaborabile.

Anche se un singolo messaggio della coda può rimanere in un ciclo di ripetizione, altre esecuzioni parallele continuano a rimuovere la coda dei messaggi rimanenti. Il risultato è che la velocità effettiva complessiva rimane sostanzialmente inalterata da un messaggio non valido. Tuttavia, le code di archiviazione non garantiscono l'ordine e non sono ottimizzate per le richieste di velocità effettiva elevata richieste dagli hub eventi.

Al contrario, Hub eventi di Azure non include un concetto di blocco. Per consentire funzionalità come la velocità effettiva elevata, più gruppi di consumer e la capacità di riproduzione, gli eventi di hub eventi si comportano in modo più simile a un lettore video. Gli eventi vengono letti da un singolo punto nel flusso per partizione. Dal puntatore è possibile leggere avanti o indietro da tale posizione, ma è necessario scegliere di spostare il puntatore per gli eventi da elaborare.

Quando si verificano errori in un flusso, se si decide di mantenendo il puntatore nello stesso punto, l'elaborazione degli eventi viene bloccata fino a quando il puntatore non è avanzato. In altre parole, se il puntatore viene interrotto per risolvere i problemi di elaborazione di un singolo evento, gli eventi non elaborati iniziano ad accumularsi.

Funzioni di Azure evita deadlock avanzando il puntatore del flusso indipendentemente dall'esito positivo o negativo. Poiché il puntatore continua a avanzare, le funzioni devono gestire gli errori in modo appropriato.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Modalità di utilizzo degli eventi di hub eventi in funzioni di Azure

Funzioni di Azure usa eventi dell'hub eventi durante il ciclo nei passaggi seguenti:

1. Un puntatore viene creato e reso permanente in archiviazione di Azure per ogni partizione dell'hub eventi.
2. Quando vengono ricevuti nuovi messaggi (in un batch per impostazione predefinita), l'host tenta di attivare la funzione con il batch di messaggi.
3. Se la funzione completa l'esecuzione (con o senza eccezione), il puntatore avanza e un checkpoint viene salvato nell'account di archiviazione.
4. Se le condizioni impediscono il completamento dell'esecuzione della funzione, l'host non è in grado di avanzare il puntatore. Se il puntatore non è avanzato, i controlli successivi terminano l'elaborazione degli stessi messaggi.
5. Ripetere i passaggi da 2 a 4

Questo comportamento rivela alcuni aspetti importanti:

- *Le eccezioni non gestite possono causare la perdita di messaggi.* Le esecuzioni che generano un'eccezione continueranno a avanzare il puntatore.
- *Funzioni garantisce il recapito at-least-once.* Il codice e i sistemi dipendenti potrebbero dover [tenere conto del fatto che lo stesso messaggio potrebbe essere ricevuto due volte](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Gestione delle eccezioni

Come regola generale, ogni funzione deve includere un [blocco try/catch](./functions-bindings-error-pages.md) al massimo livello di codice. In particolare, tutte le funzioni che utilizzano eventi di hub eventi devono avere un blocco `catch`. In questo modo, quando viene generata un'eccezione, il blocco catch gestisce l'errore prima che il puntatore avanza.

### <a name="retry-mechanisms-and-policies"></a>Meccanismi di ripetizione dei tentativi e criteri

Alcune eccezioni sono di natura temporanea e non vengono visualizzate nuovamente quando un'operazione viene ritentata in un secondo momento. Questo è il motivo per cui il primo passaggio consiste sempre nell'eseguire di nuovo l'operazione. È possibile scrivere le regole di elaborazione dei tentativi manualmente, ma sono così comuni che sono disponibili diversi strumenti. L'uso di queste librerie consente di definire criteri affidabili per i tentativi che consentono di mantenere l'ordine di elaborazione.

L'introduzione delle librerie di gestione degli errori alle funzioni consente di definire criteri di ripetizione dei tentativi di base e avanzati. Ad esempio, è possibile implementare un criterio che segue un flusso di lavoro illustrato dalle regole seguenti:

- Provare a inserire un messaggio tre volte (potenzialmente con un ritardo tra i tentativi).
- Se il risultato finale di tutti i tentativi è un errore, aggiungere un messaggio a una coda in modo che l'elaborazione possa continuare nel flusso.
- I messaggi danneggiati o non elaborati vengono quindi gestiti in un secondo momento.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) è un esempio di una raccolta di resilienza e di gestione degli errori temporanei per C# le applicazioni.

Quando si utilizzano librerie di C# classi precompilate, i [filtri eccezioni](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch) consentono di eseguire codice ogni volta che si verifica un'eccezione non gestita.

Gli esempi che illustrano come usare i filtri eccezioni sono disponibili nel repository di [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) .

## <a name="non-exception-errors"></a>Errori non eccezioni

Si verificano alcuni problemi anche quando non è presente un errore. Si consideri, ad esempio, un errore che si verifica durante un'esecuzione. In questo caso, se una funzione non completa l'esecuzione, il puntatore di offset non viene mai progredito. Se il puntatore non avanza, qualsiasi istanza che viene eseguita dopo un'esecuzione non riuscita continua a leggere gli stessi messaggi. Questa situazione fornisce una garanzia "at-least-once".

La garanzia che ogni messaggio venga elaborato almeno una volta implica che alcuni messaggi possono essere elaborati più di una volta. È necessario che le app per le funzioni siano consapevoli di questa possibilità e siano basate sui [principi di idempotenza](./functions-idempotent.md).

## <a name="stop-and-restart-execution"></a>Arrestare e riavviare l'esecuzione

Sebbene alcuni errori possano essere accettabili, cosa accade se l'app riscontra errori significativi? Potrebbe essere necessario arrestare l'attivazione di eventi fino a quando il sistema non raggiunge uno stato integro. La possibilità di sospendere l'elaborazione viene spesso eseguita con un modello di interruttore. Il modello di interruttore consente all'app di "interrompere il circuito" del processo dell'evento e riprenderla in un secondo momento.

Per implementare un interruttore in un processo di evento sono necessari due elementi:

- Stato condiviso tra tutte le istanze per tenere traccia e monitorare l'integrità del circuito
- Processo master che può gestire lo stato del circuito (aperto o chiuso)

I dettagli di implementazione possono variare, ma per condividere lo stato tra le istanze è necessario un meccanismo di archiviazione. È possibile scegliere di archiviare lo stato in archiviazione di Azure, in una cache Redis o in qualsiasi altro account accessibile da una raccolta di funzioni.

[App](../logic-apps/logic-apps-overview.md) per la logica di Azure o [entità durevoli](./durable/durable-functions-overview.md) sono una soluzione naturale per gestire il flusso di lavoro e lo stato del circuito. Anche altri servizi possono funzionare, ma per questo esempio vengono usate le app per la logica. Usando le app per la logica, è possibile sospendere e riavviare l'esecuzione di una funzione che fornisce il controllo necessario per implementare il modello di interruttore.

### <a name="define-a-failure-threshold-across-instances"></a>Definire una soglia di errore tra le istanze

Per tenere conto di più istanze che elaborano simultaneamente gli eventi, è necessario rendere permanente lo stato esterno condiviso per monitorare l'integrità del circuito.

Una regola che è possibile scegliere di implementare potrebbe applicare quanto segue:

- Se sono presenti più di 100 errori finali entro 30 secondi per tutte le istanze, interrompere il circuito e arrestare l'attivazione dei nuovi messaggi.

I dettagli di implementazione variano in base alle esigenze, ma in generale è possibile creare un sistema che:

1. Registrare gli errori in un account di archiviazione (archiviazione di Azure, Redis e così via)
1. Quando viene registrato un nuovo errore, controllare il numero di sequenza per verificare se la soglia viene soddisfatta (ad esempio, più di 100 negli ultimi 30 secondi).
1. Se la soglia viene soddisfatta, creare un evento in griglia di eventi di Azure che informa il sistema di interrompere il circuito.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Gestione dello stato del circuito con app per la logica di Azure

La descrizione seguente evidenzia un modo in cui è possibile creare un'app per la logica di Azure per arrestare l'elaborazione di un'app per le funzioni.

App per la logica di Azure include connettori incorporati per diversi servizi, funzionalità orchestrazioni con stato ed è una scelta naturale per gestire lo stato del circuito. Dopo aver rilevato che è necessario interrompere il circuito, è possibile compilare un'app per la logica per implementare il flusso di lavoro seguente:

1. Attivare un flusso di lavoro di griglia di eventi e arrestare la funzione di Azure (con il connettore di risorse di Azure)
1. Invia un messaggio di posta elettronica di notifica che include un'opzione per riavviare il flusso di lavoro

Il destinatario di posta elettronica può esaminare l'integrità del circuito e, quando necessario, riavviare il circuito tramite un collegamento nel messaggio di posta elettronica di notifica. Quando il flusso di lavoro riavvia la funzione, i messaggi vengono elaborati dall'ultimo checkpoint dell'hub eventi.

Con questo approccio, nessun messaggio viene perso, tutti i messaggi vengono elaborati in ordine ed è possibile interrompere il circuito se necessario.

## <a name="resources"></a>Risorse

- [Esempi di elaborazione di eventi affidabili](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Interruttore di Azure Durable Functions](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

- [Gestione degli errori di Funzioni di Azure](./functions-bindings-error-pages.md)
- [Automatizzare il ridimensionamento delle immagini caricate con la griglia di eventi](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Creare una funzione che si integra con le app per la logica di Azure](./functions-twitter-email.md)
