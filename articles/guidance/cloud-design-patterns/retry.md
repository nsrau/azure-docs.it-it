---
title: Modello di ripetizione dei tentativi | Azure | Documentazione Microsoft
description: "È possibile abilitare un&quot;applicazione per gestire gli errori temporanei previsti durante il tentativo di connessione a un servizio o a una risorsa di rete ritentando in modo trasparente un&quot;operazione non riuscita in precedenza."
categories:
- resiliency
keywords: schema progettuale
author: dragon119
manager: bennage
ms.service: guidance
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.author: mwasson
ms.date: 11/14/2016
translationtype: Human Translation
ms.sourcegitcommit: c4963489ca53a90cab480116ec0a044501ccb5e9
ms.openlocfilehash: 4de11604fbd5974779c554d58bfe6bf04023f69e

---

# <a name="retry"></a>Retry

È possibile abilitare un'applicazione per gestire gli errori temporanei durante il tentativo di connessione a un servizio o a una risorsa di rete ritentando in modo trasparente un'operazione non riuscita. Ciò può migliorare la stabilità dell'applicazione. 

## <a name="context-and-problem"></a>Contesto e problema

Un'applicazione che comunica con gli elementi in esecuzione nel cloud deve essere in grado di rilevare gli errori temporanei che possono verificarsi in questo ambiente. Tra gli errori sono inclusi la perdita temporanea della connettività di rete per componenti e servizi, l'indisponibilità temporanea di un servizio o i timeout generati perché un servizio è occupato. 

Questi errori si risolvono di solito automaticamente e, se l'azione che ha generato l'errore viene ripetuta dopo un intervallo di tempo appropriato, è probabile che abbia esito positivo. Ad esempio, un servizio di database potrebbe implementare una strategia di limitazione durante l'elaborazione di un numero elevato di richieste simultanee, in modo da rifiutare temporaneamente eventuali ulteriori richieste fino alla riduzione del carico di lavoro. Un'applicazione che tenta di accedere al database in questa situazione potrebbe non riuscire a connettersi, ma un tentativo successivo potrebbe invece avere esito positivo.

## <a name="solution"></a>Soluzione

Nel cloud, gli errori temporanei sono abbastanza comuni e un'applicazione deve essere progettata per gestirli in modo elegante e trasparente. Si riducono così i possibili effetti degli errori sulle attività aziendali eseguite dall'applicazione.

Se un'applicazione rileva un errore durante il tentativo di inviare una richiesta a un servizio remoto, può adottare le strategie seguenti per gestire l'errore: 

- **Annullare**. Se l'errore risulta essere non temporaneo o indica che è improbabile che l'operazione venga eseguita correttamente se ripetuta, l'applicazione deve annullare l'operazione e segnalare un'eccezione. Ad esempio, un errore di autenticazione causato dall'immissione di credenziali non valide è improbabile che si risolva con ulteriori tentativi.

- **Riprovare**. Se l'errore specifico segnalato è insolito o raro, potrebbe essere stato causato da circostanze non comuni, ad esempio un pacchetto di rete danneggiato durante la trasmissione. In questo caso, l'applicazione può ritentare immediatamente la richiesta in errore, perché è improbabile che si ripeta lo stesso errore ed è invece probabile che la richiesta venga completata. 

- **Ritentare dopo un certo periodo di tempo.** Se l'errore è causato da uno degli errori di più comuni di connettività o di servizio occupato, potrebbe essere necessario aspettare un breve periodo di tempo per fare in modo che i problemi di connettività vengano risolti o che venga completato il backlog di lavoro. In questo caso, l'applicazione deve attendere per un periodo di tempo appropriato prima di ritentare la richiesta. 

Per gli errori temporanei più comuni, l'intervallo tra i tentativi deve essere scelto in modo da distribuire le richieste da più istanze dell'applicazione nel modo più uniforme possibile. Si riduce così la possibilità che un servizio occupato continui a essere sovraccaricato. Se molte istanze di un'applicazione inondano continuamente un servizio di richieste di nuovi tentativi, al servizio servirà più tempo per il ripristino.

Se la richiesta ha ancora esito negativo, l'applicazione può attendere ed eseguire un altro tentativo. Se necessario, questo processo può essere ripetuto con ritardi crescenti tra i tentativi, fino al raggiungimento di un numero massimo di tentativi di riesecuzione delle richieste. Il ritardo può essere aumentato in modo incrementale o esponenziale, a seconda del tipo di errore e delle probabilità che venga risolto durante questo periodo. 

Il diagramma seguente illustra come richiamare un'operazione in un servizio ospitato usando questo modello. Se la richiesta ha esito negativo dopo un numero predefinito di tentativi, l'applicazione deve trattare l'errore come eccezione e gestirla di conseguenza. 

![Figura 1 - Richiamare un'operazione in un servizio ospitato usando il modello di ripetizione dei tentativi](images/retry-pattern.png)

L'applicazione deve eseguire il wrapping di tutti i tentativi di accesso a un servizio remoto all'interno di codice per implementare criteri di ripetizione dei tentativi corrispondenti a una delle strategie sopra elencate. Le richieste inviate a servizi diversi possono essere soggette a criteri diversi. Alcuni fornitori offrono librerie che implementano criteri di ripetizione dei tentativi, con i quali l'applicazione può specificare il numero massimo di tentativi, l'intervallo tra i tentativi e altri parametri.

Un'applicazione deve registrare i dettagli degli errori e delle operazioni con esito negativo. Queste informazioni sono utili per gli operatori. Se un servizio è frequentemente non disponibile oppure occupato, spesso la causa è l'esaurimento delle risorse. È possibile ridurre la frequenza degli errori ampliando il servizio. Ad esempio, se un servizio di database risulta continuamente sovraccarico, potrebbe essere utile partizionare il database e distribuire il carico su più server.

>  Microsoft Azure offre un supporto completo per il modello di ripetizione dei tentativi. Il [blocco per la gestione degli errori temporanei](https://msdn.microsoft.com/library/hh680934.aspx) disponibile nel sito Patterns & Practice consente a un'applicazione di gestire gli errori temporanei in molti servizi di Azure usando varie strategie di ripetizione dei tentativi. La [versione 6 di Microsoft Entity Framework](https://msdn.microsoft.com/en-us/data/dn456835.aspx) offre strumenti per la ripetizione dei tentativi di esecuzione delle operazioni su database. Molte delle API del bus di servizio di Azure e di Archiviazione di Azure, inoltre, implementano la logica di ripetizione dei tentativi in modo trasparente. 

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Prima di decidere come implementare questo modello, è opportuno considerare quanto segue. 

I criteri di ripetizione dei tentativi devono essere adattati ai requisiti aziendali dell'applicazione e alla natura dell'errore. Per alcune operazioni non critiche, è preferibile un'interruzione rapida piuttosto che ripetere vari tentativi con effetti sulla velocità effettiva dell'applicazione. Per un'applicazione Web interattiva che accede a un servizio remoto, ad esempio, è preferibile interrompere l'esecuzione dopo un numero limitato di tentativi e con un breve ritardo tra i tentativi, quindi visualizzare un messaggio appropriato all'utente (ad esempio, "riprovare più tardi"). Per un'applicazione che esegue operazioni in batch, potrebbe essere invece più appropriato aumentare il numero di tentativi con un ritardo esponenzialmente maggiore tra i vari tentativi.

Criteri di ripetizione aggressivi con un ritardo minimo tra tentativi, così come un numero elevato di tentativi, potrebbero peggiorare ulteriormente la situazione per un servizio occupato eseguito ai limiti della capacità. Questo tipo di criteri di ripetizione possono influire anche sulla velocità di risposta dell'applicazione, se tenta continuamente di eseguire un'operazione che non riesce.

Se una richiesta non riesce dopo un numero significativo di tentativi, è consigliabile che l'applicazione eviti ulteriori richieste per la stessa risorsa e che invece segnali immediatamente un errore. Al termine del periodo, l'applicazione può provare a consentire una o più richieste per verificare se riescono. Per altri dettagli su questa strategia, vedere [Modello a interruttore](circuit-breaker.md).

Valutare se l'operazione è idempotente. In caso affermativo, la ripetizione dei tentativi è intrinsecamente sicura. In caso contrario, i tentativi potrebbero causare più esecuzioni dell'operazione, con effetti collaterali imprevisti. Ad esempio, un servizio potrebbe ricevere la richiesta ed elaborarla correttamente, ma non riuscire invece a inviare una risposta. A questo punto, la logica di ripetizione dei tentativi potrebbe inviare nuovamente la richiesta, partendo dal presupposto che la prima richiesta non sia stata ricevuta.    

Una richiesta per un servizio può avere esito negativo per svariati motivi e generare eccezioni diverse a seconda della natura dell'errore. Alcune eccezioni indicano un errore che può essere risolto rapidamente, mentre altre indicano errori di più lunga durata. È utile che i criteri per la ripetizione dei tentativi adattino l'intervallo di tempo tra i tentativi in base al tipo dell'eccezione.

Valutare gli effetti della ripetizione di un'operazione che fa parte di una transazione sulla coerenza complessiva delle transazioni. Mettere a punto i criteri di ripetizione dei tentativi per le operazioni transazionali per ottenere la massima probabilità di successo e ridurre la necessità di annullare tutti i passaggi della transazione. 

Assicurarsi di testare in modo completo tutto il codice per la ripetizione dei tentativi per un'ampia gamma di condizioni di errore. Verificare che non ci siano effetti gravi sulle prestazioni o l'affidabilità dell'applicazione, che non causi un carico eccessivo su servizi e risorse o che non si generino colli di bottiglia o race condition. 

Implementare la logica di ripetizione dei tentativi solo nei casi in cui è noto il contesto completo di un'operazione con esito negativo. Ad esempio, se un'attività che contiene criteri di ripetizione richiama un'altra attività che contiene a sua volta criteri di ripetizione, questo livello aggiuntivo di tentativi può introdurre lunghi ritardi per l'elaborazione. Potrebbe essere più appropriato configurare l'attività di livello più basso in modo che generi errore rapidamente e segnali il motivo dell'errore all'attività che l'ha richiamata. Questa attività di livello superiore può quindi gestire l'errore in base ai propri criteri. 

È importante registrare tutti gli errori di connettività che causano un nuovo tentativo, in modo da poter identificare i problemi sottostanti con l'applicazione, i servizi o le risorse. 

Esaminare gli errori che è più probabile che si verifichino per un servizio o una risorsa per stabilire se è probabile che siano durevoli o terminali. In questo caso, è preferibile gestire l'errore come un'eccezione. L'applicazione può segnalare o registrare l'eccezione e quindi tentare di continuare richiamando un servizio alternativo (se disponibile) o offrendo funzionalità ridotte. Per altre informazioni su come rilevare e gestire gli errori di lunga durata, vedere il [modello di interruttore](circuit-breaker.md).

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo modello quando un'applicazione può subire errori temporanei quando interagisce con un servizio remoto o accede a una risorsa remota. Questi errori sono prevedibilmente di breve durata e la ripetizione di una richiesta non riuscita in precedenza potrebbe avere esito positivo durante un tentativo successivo. 

Questo modello potrebbe non essere utile:
- Quando è probabile che un errore sia di lunga durata, in quanto questo può influire sulla velocità di risposta di un'applicazione. L'applicazione potrebbe sprecare tempo e risorse nel tentativo di ripetere una richiesta che probabilmente non riuscirà. 
- Per la gestione degli errori che non sono dovuti a problematiche temporanee, ad esempio le eccezioni interne causate da errori nella logica di business di un'applicazione. 
- Come alternativa alla risoluzione dei problemi di scalabilità in un sistema. Se un'applicazione riscontra errori frequenti per condizioni di occupato, questo spesso è segno della necessità di potenziare il servizio o la risorsa a cui si accede.

## <a name="example"></a>Esempio

Questo esempio in C# illustra un'implementazione del modello di ripetizione dei tentativi. Il metodo `OperationWithBasicRetryAsync` richiama un servizio esterno in modo asincrono tramite il metodo `TransientOperationAsync` (i dettagli di questo metodo sono specifici del servizio e vengono omessi nel codice di esempio). 

```csharp
private int retryCount = 3;
...

public async Task OperationWithBasicRetryAsync()
{
  int currentRetry = 0;

  for (; ;)
  {
    try
    {
      // Calling external service.
      await TransientOperationAsync();
                    
      // Return or break.
      break;
    }
    catch (Exception ex)
    {
      Trace.TraceError("Operation Exception");

      currentRetry++;

      // Check if the exception thrown was a transient exception
      // based on the logic in the error detection strategy.
      // Determine whether to retry the operation, as well as how 
      // long to wait, based on the retry strategy.
      if (currentRetry > this.retryCount || !IsTransient(ex))
      {
        // If this isn't a transient error 
        // or we shouldn't retry, rethrow the exception. 
        throw;
      }
    }

    // Wait to retry the operation.
    // Consider calculating an exponential delay here and 
    // using a strategy best suited for the operation and fault.
    Await.Task.Delay();
  }
}

// Async method that wraps a call to a remote service (details not shown).
private async Task TransientOperationAsync()
{
  ...
}
```

L'istruzione che richiama questo metodo è contenuta in un blocco try/catch di cui viene eseguito il wrapping in un ciclo for. Il ciclo for restituisce il controllo se la chiamata del metodo `TransientOperationAsync` ha esito positivo senza generare un'eccezione. Se il metodo `TransientOperationAsync` ha esito negativo, il blocco catch esamina il motivo dell'errore. Se si ritiene che l'errore sia temporaneo, il codice attende per un breve intervallo di tempo prima di ritentare l'operazione. 

Il ciclo for tiene anche traccia del numero di tentativi di esecuzione dell'operazione e se il codice ha esito negativo tre volte, l'eccezione viene considerata di più lunga durata. Se l'eccezione non è temporanea o è di lunga durata, il gestore catch genera un'eccezione. Questa eccezione causa l'uscita dal ciclo for e deve essere intercettata dal codice che richiama il metodo `OperationWithBasicRetryAsync`.

Il metodo `IsTransient`, illustrato di seguito, verifica la presenza di un set specifico di eccezioni rilevanti per l'ambiente in cui viene eseguito il codice. La definizione di eccezione temporanea varia in base alle risorse a cui si accede e all'ambiente in cui viene eseguita l'operazione.

```csharp
private bool IsTransient(Exception ex)
{
  // Determine if the exception is transient.
  // In some cases this is as simple as checking the exception type, in other 
  // cases it might be necessary to inspect other properties of the exception.
  if (ex is OperationTransientException)
    return true;

  var webException = ex as WebException;
  if (webException != null)
  {
    // If the web exception contains one of the following status values 
    // it might be transient.
    return new[] {WebExceptionStatus.ConnectionClosed, 
                  WebExceptionStatus.Timeout, 
                  WebExceptionStatus.RequestCanceled }.
            Contains(webException.Status);
  }

  // Additional exception checking logic goes here.
  return false;
}
```

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

- [Modello di interruttore](circuit-breaker.md). Il modello di ripetizione dei tentativi è utile per la gestione degli errori temporanei. Se si prevede che un errore sia di maggiore durata, potrebbe essere più appropriato implementare il modello di interruttore. Il modello di ripetizione dei tentativi può essere usato anche in combinazione con un interruttore per ottenere un approccio completo alla gestione degli errori.
- [Blocco di applicazioni per la gestione degli errori temporanei](https://msdn.microsoft.com/library/hh680934.aspx)
- [Resilienza della connessione / Logica di ripetizione dei tentativi](https://msdn.microsoft.com/en-us/data/dn456835.aspx)



<!--HONumber=Nov16_HO3-->


