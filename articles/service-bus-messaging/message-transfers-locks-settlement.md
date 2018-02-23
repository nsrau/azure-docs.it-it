---
title: Trasferimenti, blocchi e finalizzazione dei messaggi del bus di servizio di Azure | Microsoft Docs
description: Panoramica dei trasferimenti e delle operazioni di finalizzazione dei messaggi del bus di servizio
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: 4789da3c84d52b2615bf4250a36093a74154e1d4
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="message-transfers-locks-and-settlement"></a>Trasferimenti, blocchi e finalizzazione dei messaggi

La funzionalità principale di un broker di messaggi come il bus di servizio è quella di accettare i messaggi in una coda o in un argomento e conservali rendendoli disponibili per un successivo recupero. *Invio* è il termine comunemente usato per il trasferimento di un messaggio in un broker di messaggi. *Ricezione* è il termine comunemente usato per il trasferimento di un messaggio in un client che esegue il recupero.

Quando un client invia un messaggio, vuole in genere sapere se il messaggio è stato correttamente trasferito e accettato dal broker o se si è verificato qualche tipo di errore. Questo riconoscimento positivo o negativo permette al client e al broker di comprendere lo stato del trasferimento del messaggio ed è noto come *finalizzazione*.

Analogamente, quando un broker trasferisce un messaggio a un client, il broker e il client vogliono sapere se il messaggio è stato elaborato correttamente e può quindi essere rimosso oppure se l'elaborazione o il recapito del messaggio non è riuscito e di conseguenza il messaggio deve essere recapitato di nuovo.

## <a name="settling-send-operations"></a>Finalizzazione delle operazioni di invio

Usando qualsiasi client API del bus di servizio supportato, le operazioni di invio nel bus di servizio vengono sempre finalizzate in modo esplicito, ovvero l'operazione API attende l'arrivo del risultato dell'accettazione dal bus di servizio e quindi completa l'operazione di invio.

Se il messaggio viene rifiutato dal bus di servizio, il rifiuto contiene un indicatore di errore e il testo con un valore "tracking-id". Il rifiuto include anche informazioni relative al fatto che sia possibile o meno provare a eseguire di nuovo l'operazione con la possibilità di avere un esito positivo. Nel client queste informazioni vengono trasformate in un'eccezione, generata per il chiamante dell'operazione di invio. Se il messaggio è stato accettato, l'operazione viene completata automaticamente.

Quando si usa il protocollo AMQP, ovvero il protocollo esclusivo per il client .NET Standard e il client Java e che costituisce [un'opzione per il client .NET Framework](service-bus-amqp-dotnet.md), le operazioni di trasferimento e finalizzazione dei messaggi vengono inserite in pipeline e sono completamente asincrone ed è consigliabile usare le varianti di API del modello di programmazione asincrono.

Un mittente può inviare diversi messaggi in transito in rapida successione senza dover attendere il riconoscimento di ogni messaggio, come avverrebbe invece con il protocollo SBMP o HTTP 1.1. Le operazioni di invio asincrone vengono completate man mano che i relativi messaggi vengono accettati e archiviati, nelle entità partizionate o quando le operazioni di invio a entità diverse si sovrappongono. Il completamento può avvenire anche fuori dall'ordine di invio originale.

La strategia per gestire il risultato delle operazioni di invio può avere un impatto immediato e significativo sulle prestazioni dell'applicazione. Gli esempi di questa sezione sono scritti in C# e sono applicabili anche a Java Futures.

Se l'applicazione genera picchi di messaggi, illustrati qui con un ciclo semplice, e si attende il completamento di ogni operazione di invio prima di inviare il messaggio successivo, le API sincrone o asincrone hanno un funzionamento simile e l'invio di 10 messaggi viene completato solo dopo 10 round trip completi sequenziali per la finalizzazione.

Presupponendo una latenza del round trip TCP di 70 millisecondi dovuta alla distanza tra un sito locale e il bus di servizio e consentendo solo 10 ms al bus di servizio per accettare e archiviare ogni messaggio, il ciclo seguente richiede almeno 8 secondi, senza contare il tempo di trasferimento del payload o i potenziali effetti di congestione della route:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Se l'applicazione avvia le 10 operazioni di invio asincrone in successione immediata e attende il rispettivo completamento separatamente, il tempo di round trip per tali 10 operazioni di invio si sovrappone. I 10 messaggi vengono trasferiti in successione immediata, potenzialmente anche condividendo frame TCP, e la durata complessiva del trasferimento dipende in gran parte dal tempo di rete necessario per trasferire i messaggi al broker.

Basandosi sugli stessi presupposti illustrati per il ciclo precedente, il tempo di esecuzione totale sovrapposto per il ciclo seguente potrebbe rimanere ben sotto un secondo:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks.ToArray());
```

È importante notare che tutti i modelli di programmazione asincroni usano una forma di coda di lavoro nascosta basata sulla memoria che contiene le operazioni in sospeso. Quando [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) o **Send** (Java) restituisce un risultato, l'attività di invio viene inserita nella coda di lavoro, ma l'azione del protocollo viene avviata solo quando è il turno dell'attività di essere eseguita. Per il codice che tende a eseguire il push di picchi di messaggi e per i casi in cui l'affidabilità è importante, è necessario fare attenzione che non ci siano troppi messaggi "in corso" contemporaneamente, in quanto tutti i messaggi inviati occupano memoria fino a quando non sono effettivamente in transito.

I semafori, come illustrato nel frammento di codice seguente in C#, sono oggetti di sincronizzazione che consentono, quando necessario, tale limitazione a livello di applicazione. Questo uso di un semaforo consente la presenza al massimo di 10 messaggi in corso contemporaneamente. Uno dei 10 blocchi tramite semaforo disponibili viene acquisito prima dell'invio e rilasciato al completamento dell'invio. L'undicesimo passaggio nel ciclo resta in attesa fino a quando uno degli invii precedenti viene completato e quindi rende disponibile il blocco:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks.ToArray());
```

Le applicazioni non devono **mai** avviare un'operazione di invio asincrona adottando l'approccio "fire-and-forget", ovvero senza recuperare il risultato dell'operazione. Ciò potrebbe caricare la coda di attività interna e invisibile fino a esaurire la memoria, impedendo all'applicazione di rilevare gli errori di invio:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Con un client AMQP di basso livello, il bus di servizio accetta anche i trasferimenti "pre-finalizzati". Un trasferimento pre-finalizzato è un'operazione di tipo fire-and-forget per la quale il risultato, qualunque esso sia, non viene segnalato al client e il messaggio viene considerato finalizzato quando inviato. La mancanza di feedback per il client significa anche che non ci sono dati disponibili per la diagnostica, quindi questa modalità non è idonea per richiedere assistenza al supporto tecnico di Azure.

## <a name="settling-receive-operations"></a>Finalizzazione delle operazioni di ricezione

Per le operazioni di ricezione, i client API del bus di servizio consentono due diverse modalità esplicite: *ricezione ed eliminazione* e *blocco di visualizzazione*.

La modalità di [ricezione ed eliminazione](/dotnet/api/microsoft.servicebus.messaging.receivemode) indica al broker di considerare tutti i messaggi inviati al client ricevente come finalizzati quando inviati. Ciò significa che il messaggio è considerato utilizzato non appena il broker lo ha inviato in transito. Se il trasferimento del messaggio non riesce, il messaggio viene perso.

Il vantaggio di questa modalità è il fatto che il ricevitore non deve eseguire altre azioni sul messaggio e non viene rallentato dall'attesa del risultato della finalizzazione. Se i dati contenuti nei singoli messaggi hanno un valore basso e/o sono significativi solo per un tempo molto breve, questa modalità è una scelta ragionevole.

La modalità [blocco di visualizzazione](/dotnet/api/microsoft.servicebus.messaging.receivemode) indica al broker che il client ricevente richiede la finalizzazione esplicita dei messaggi ricevuti. Il messaggio viene reso disponibile per l'elaborazione da parte del ricevitore, mentre viene applicato un blocco esclusivo nel servizio in modo che altri ricevitori concorrenti non possano visualizzarlo. La durata del blocco viene definita inizialmente a livello di coda o di sottoscrizione e può essere estesa dal client proprietario del blocco, tramite l'operazione [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_).

Quando un messaggio è bloccato, gli altri client che ricevono dalla stessa coda o sottoscrizione possono accettare i blocchi e recuperare i successivi messaggi disponibili non attivamente bloccati. Quando il blocco su un messaggio viene rilasciato in modo esplicito o scade, il messaggio ritorna all'inizio o in prossimità dell'inizio dell'ordine di recupero per essere recapitato nuovamente.

Quando il messaggio viene rilasciato ripetutamente dai ricevitori o i ricevitori lasciano scadere il blocco per un numero definito di volte ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), il messaggio viene rimosso automaticamente dalla coda o dalla sottoscrizione e inserito nella coda di messaggi non recapitabili associata.

Il client ricevente avvia la finalizzazione di un messaggio ricevuto con un riconoscimento positivo quando chiama il metodo [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) a livello di API. Ciò indica al broker che il messaggio è stato elaborato correttamente e viene rimosso dalla coda o dalla sottoscrizione. Il broker risponde all'intenzione di finalizzazione del ricevitore indicando se la finalizzazione è stata possibile.

Quando il client ricevente non riesce a elaborare un messaggio, ma vuole che il messaggio venga recapitato nuovamente, può chiedere esplicitamente che il messaggio venga rilasciato e sbloccato immediatamente chiamando [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) oppure può non fare niente e lasciare che il blocco scada.

Se un client ricevente non riesce a elaborare un messaggio e sa che un nuovo tentativo di eseguire l'operazione e recapitare il messaggio non sarà di aiuto, può rifiutare il messaggio, che viene quindi spostato nella coda di messaggi non recapitabili, chiamando [DeadLetter](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), che consente anche di impostare una proprietà personalizzata, incluso un codice motivo che può essere recuperato con il messaggio dalla coda di messaggi non recapitabili.

Un caso speciale di finalizzazione è il differimento, illustrato in un articolo separato.

Le operazioni **Complete** e **Deadletter**, come pure l'operazione **RenewLock**, potrebbero non riuscire a causa di problemi di rete, se il blocco applicato è scaduto o se ci sono altre condizioni sul lato del servizio che impediscono la finalizzazione. In uno degli ultimi casi, il servizio invia un riconoscimento negativo (NAK) che genera un'eccezione nei client API. Se il motivo è un'interruzione della connessione di rete, il blocco viene eliminato perché il bus di servizio non supporta il ripristino dei collegamenti AMQP esistenti in una connessione diversa.

Se il metodo **Complete** ha esito negativo, cosa che accade in genere nelle fasi finali di gestione dei messaggi e in alcuni casi dopo alcuni minuti di lavoro di elaborazione, l'applicazione ricevente può decidere se mantenere lo stato del lavoro e ignorare lo stesso messaggio quando viene recapitato una seconda volta oppure se scartare il risultato del lavoro e riprovare quando il messaggio viene recapitato nuovamente.

Il meccanismo tipico per l'identificazione di recapiti di messaggi duplicati consiste nel verificare il valore di message-id, che può e deve essere impostato dal mittente su un valore univoco, possibilmente allineato con un identificatore del processo di origine. Probabilmente, un pianificatore di processi imposta il valore di message-id sull'identificatore del processo che sta cercando di assegnare a un ruolo di lavoro con il ruolo di lavoro specificato e il ruolo di lavoro ignora la seconda occorrenza dell'assegnazione del processo, se tale processo è già stato eseguito.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
