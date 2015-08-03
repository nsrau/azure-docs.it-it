<properties
   pageTitle="Pacchetti NuGet | Microsoft Azure"
   description="Linee guida sui pacchetti NuGet per le operazioni relative ai criteri generali dei tentativi."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/09/2015"
   ms.author="masashin"/>

# Pacchetti NuGet

<p class="lead">Quando più componenti iniziano a comunicare, gli errori temporanei diventano più importanti per una gestione intelligente. Le operazioni di gestione degli errori temporanei gestite dal pacchetto NuGet dei criteri di ripetizione consentono di gestire i tentativi all’interno di una singola istanza.</p>

> Questo documento è basato su una bozza di un modello di prova. Non si tratta delle linee guida effettive riviste.

Il codice `TransientFaultHandling` dei modelli e delle procedure è consigliato per le operazioni generali dei criteri relativi ai tentativi.

```
Install-Package EnterpriseLibrary.WindowsAzure.TransientFaultHandling
```

## Configurazione

Nella sezione sono incluse le informazioni di configurazione per la funzionalità dei tentativi:

Parametro | Descrizione
-------------------- | ----------------------
MaximumExecutionTime | Tempo massimo di esecuzione per la richiesta, inclusi tutti i potenziali tentativi.
ServerTimeOut | Intervallo di timeout del server per la richiesta
RetryPolicy | Criterio dei tentativi. Vedere la sezione Criteri seguente

```csharp
/// <summary>
/// An interface required for request option types.
/// </summary>
public interface IRequestOptions
{
    IRetryPolicy RetryPolicy { get; set; }

    TimeSpan? ServerTimeout { get; set; }

    TimeSpan? MaximumExecutionTime { get; set; }
}
```

Programmatica:

- Supporto dell'impostazione sul client.
- Abilitazione dell'override nelle operazioni fornite dal client

File di configurazione:

```xml
<RetryPolicyConfiguration defaultRetryStrategy="Fixed Interval Retry Strategy">
    <linearInterval name="Fixed Interval Retry Strategy"
	retryInterval="00:00:01" maxRetryCount="10" />
    <exponentialBackoff name="Backoff Retry Strategy" minBackoff="00:00:01"
        maxBackoff="00:00:30" deltaBackoff="00:00:10" maxRetryCount="10"
        fastFirst="false"/>
</RetryPolicyConfiguration>
```

## Criteri

### Esponenziale

Utilizzato per la spaziatura esponenziale dei tentativi ripetuti di chiamate al servizio per evitare la limitazione del servizio.

__Approccio:__

Aumentare in modo esponenziale l'intervallo di backoff tra tentativi successivi. Aggiungere la sequenza casuale di (+ /-20%) all'intervallo di backoff per evitare la ripetizione contemporaneamente in tutti i client

__Configurazione:__

Parametro | Descrizione
-------------------- | -------------------------------------------------------
maxAttempt | Numero di tentativi.
deltaBackoff | Intervallo di backoff tra tentativi. Per i successivi tentativi verranno utilizzati i multipli di questi timespan.
MinBackoff | Aggiunto a tutti gli intervalli di ripetizione calcolati da deltaBackoff.
FastFirst | Primo tentativo immediato
MaxBackoff | Utilizzato se l’intervallo di tentativi calcolato è maggiore di MaxBackoff. Questo valore non può essere modificato.

__Logica di implementazione:__

```csharp
if(!ExponentialRetry.FastFirst){
    Random r = new Random();
    double increment = (Math.Pow(2, currentRetryCount) - 1) * r.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2));
    retryInterval = (increment < 0) ? ExponentialRetry.MaxBackoff :
    TimeSpan.FromMilliseconds(Math.Min(ExponentialRetry.MaxBackoff.TotalMilliseconds, ExponentialRetry.MinBackoff.TotalMilliseconds + increment));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## Lineare

Utilizzato per la spaziatura dei tentativi ripetuti di chiamate al servizio per evitare la limitazione del servizio.

__Approccio:__

Eseguire un numero specificato di tentativi utilizzando un intervallo di tempo fisso specificato tra i tentativi. Aggiungere la sequenza casuale di (+ /-20%) all'intervallo di backoff per evitare la ripetizione contemporanea in tutti i client

__Configurazione:__

Parametro | Descrizione
-------------------- | -------------------------------------------------------
maxAttempt | Numero di tentativi.
deltaBackoff | Intervallo di backoff tra tentativi.
FastFirst | Primo tentativo immediato

__Logica di implementazione:__

```csharp
if(!ExponentialRetry.FastFirst) {
    Random r = new Random();
    retryInterval = TimeSpan.FromMilliseconds(r.Next((int)(
    this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## Adattivo

Utilizzato per la spaziatura dei tentativi ripetuti di chiamate al servizio in base al codice di errore o ai metadati passati dal servizio nell'intestazione della risposta.

__Approccio:__

Eseguire un numero specificato di tentativi, utilizzando un intervallo di backoff calcolato in base al codice di errore o ai metadati passati dal servizio nell'intestazione della risposta


__Configurazione:__

Non configurabile

__Logica di implementazione:__

Basata sul codice di errore o sui metadati passati dal servizio nell'intestazione della risposta

__Interruzione del circuito:__

Basata su [Circuit Breaker](http://msdn.microsoft.com/library/dn589784.aspx)

## Estensibilità

Interfaccia pubblica che può essere implementato per fornire criteri di tentativi personalizzati

```csharp
public interface IRetryPolicy
{
    /// <summary>
    /// Generates a new retry policy for the current request attempt.
    /// </summary>
    IRetryPolicy CreateInstance();

    /// <summary>
    /// Determines whether the operation should be retried and the interval until the next retry.
    /// </summary>
    /// <param name="currentRetryCount">An integer specifying the number of retries for the given operation. A value of zero signifies this is the first error encountered.</param>
    /// <param name="statusCode">An integer containing the status code for the last operation.</param>
    /// <param name="retryInterval">A <see cref="TimeSpan"/> indicating the interval to wait until the next retry.</param>
    /// <returns><c>true</c> if the operation should be retried; otherwise, <c>false</c>.</returns>
    bool ShouldRetry(int currentRetryCount, int statusCode, out TimeSpan retryInterval);
}
```

## Telemetria

Tentativi di registrazione come eventi ETW tramite EventSource. Di seguito sono elencati i campi che devono essere registrati per ogni nuovo tentativo

Parametro | Descrizione
-------------------- | -------------------------------------------------------
requestId | ""
policyType | "RetryExponential"
operation | "Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
operationStartTime | "9/5/2014 10:00:13 PM"
operationEndTime | "9/5/2014 10:00:14 PM"
iteration | "0"
iterationSleep | "00:00:00.1000000"
lastExceptionType | "Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage | "The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"

<!---HONumber=July15_HO4-->