<properties
    pageTitle="Monitorare le API con Gestione API di Azure, Hub eventi e Runscope"
    description="Applicazione di esempio che illustra il criterio log-to-eventhub tramite la connessione di Gestione API di Azure, Hub eventi di Azure e Runscope per operazioni di registrazione e monitoraggio HTTP"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager=""
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/30/2015"
    ms.author="v-darmi"/>

# Monitorare le API con Gestione API di Azure, Hub eventi e Runscope

Il [servizio Gestione API](api-management-key-concepts.md) offre molte capacità per migliorare l'elaborazione di richieste HTTP inviate all'API HTTP. L'esistenza di richieste e risposte è tuttavia temporanea. La richiesta viene effettuata e passa attraverso al servizio Gestione API fino all'API back-end. L'API elabora la richiesta e una risposta viene restituita al consumer dell'API. Il servizio Gestione API mantiene alcune statistiche importanti sulle API da visualizzare nel dashboard del portale di pubblicazione, ma eventuali altri dettagli verranno eliminati.

L'uso del [criterio](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) [log-to-eventhub](api-management-howto-policies.md) nel servizio Gestione API consente di inviare eventuali dettagli dalla richiesta e dalla risposta a un [Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md). È possibile che si voglia generare eventi dai messaggi HTTP inviati alle API per diversi motivi, ad esempio per ottenere audit trail di aggiornamenti, analisi di utilizzo, avvisi relativi alle eccezioni e integrazioni di terze parti.

Questo articolo illustra come acquisire l'intero messaggio di richiesta e risposta HTTP, inviarlo a un hub eventi e quindi inoltrare il messaggio a un servizio d terze parti che fornisce servizi di registrazione HTTP e monitoraggio.

## Vantaggi dell'invio dal servizio Gestione API
È possibile scrivere middleware HTTP in grado di collegarsi ai framework API HTTP per acquisire richieste e risposte HTTP e fornirle ai sistemi di registrazione e monitoraggio. Lo svantaggio di questo approccio consiste nel fatto che il middleware HTTP deve essere integrato nell'API back-end e deve corrispondere alla piattaforma dell'API. Se sono disponibili più API, ognuna dovrà distribuire il middleware. Spesso non è possibile aggiornare le API back-end.

L'uso del servizio Gestione API di Azure per l'integrazione con l'infrastruttura di registrazione offre una soluzione centralizzata e indipendente dalla piattaforma, oltre alla scalabilità, in parte grazie alla capacità di [replica geografica](api-management-howto-deploy-multi-region.md) di Gestione API di Azure.

## Vantaggi dell'invio a un Hub eventi di Azure
È legittimo domandarsi quali siano i vantaggi della creazione di un criterio specifico dell'Hub eventi di Azure. È possibile registrare le richieste in molte posizione diverse. L'invio delle richieste direttamente alla destinazione finale è una delle opzioni disponibili. Quando si effettuano richieste di registrazione da un servizio di gestione API, è tuttavia necessario valutare l'impatto della registrazione dei messaggi sulle prestazioni dell'API. Gli incrementi graduali nel carico possono essere gestiti da istanze a disponibilità crescente dei componenti di sistema o sfruttando i vantaggi della replica geografica. I brevi picchi di traffico possono tuttavia provocare un ritardo significativo delle richieste se le richieste all'infrastruttura di registrazione iniziano a rallentare a causa del carico.

Hub eventi di Azure è stato progettato per inserire volumi elevati di dati, con una capacità sufficiente per la gestione di un numero di eventi molto più elevato rispetto al numero di richieste HTTP elaborate dalla maggior parte delle API. L'Hub eventi è analogo a un buffer avanzato tra il servizio di gestione API e l'infrastruttura che archivierà ed elaborerà i messaggi. Ciò assicura che le prestazioni dell'API non saranno danneggiate dall'infrastruttura di registrazione.

Dopo il passaggio a un hub eventi, i dati verranno resi persistenti e rimarranno in attesa di elaborazione da parte dei consumer dell'Hub eventi. Hub eventi non prevede requisiti specifici per la modalità di elaborazione dei dati, si impegna semplicemente nell'assicurare che il messaggio venga recapitato correttamente.

Gli hub eventi possono effettuare lo streaming di eventi a più gruppi di consumer. Ciò consente l'elaborazione degli eventi da parte di sistemi completamente diversi. Sarà quindi possibile supportare molti scenari di integrazione senza ritardare ulteriormente l'elaborazione della richiesta API entro il servizio Gestione API, perché è necessario generare solo un evento.

## Criterio per l'invio di messaggi applicazione/HTTP
Un hub eventi accetta i dati evento sotto forma di semplice stringa. I contenuti della stringa possono essere definiti completamente dall'utente. Per potere creare un pacchetto di una richiesta HTTP e inviarlo all'Hub eventi, è necessario formattare la stringa con le informazioni di richiesta o di risposta. In situazioni come queste, se è disponibile una formattazione esistente che può essere usata, potrebbe non essere necessario scrivere codice di analisi specifico. È stata inizialmente valutata la possibilità di usare [HAR](http://www.softwareishard.com/blog/har-12-spec/) per l'invio di richieste e risposte HTTP, ma questo formato è ottimizzato per l'archiviazione di una sequenza di richieste HTTP in un formato basato su JSON. Contiene alcuni elementi obbligatori che aggiungono una complessità superflua per lo scenario del passaggio del messaggio HTTP in rete.

Un'opzione alternativa consiste nell'usare il tipo di dati multimediali `application/http`, come illustrato nella specifica HTTP [RFC 7230](http://tools.ietf.org/html/rfc7230). Questo tipo di dati multimediali usa esattamente lo stesso formato adottato per inviare effettivamente i messaggi HTTP in rete, ma l'intero messaggio può essere inserito nel corpo di un'altra richiesta HTTP. In questo caso il corpo verrà usato come messaggio da inviare all'Hub eventi. Il parser disponibile nelle librerie [Microsoft ASP.NET Web API 2.2 Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) può essere usato per analizzare questo formato e convertirlo negli oggetti `HttpRequestMessage` e `HttpResponseMessage` nativi.

Per potere creare questo messaggio, è necessario sfruttare le [espressioni di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx) basate su C# disponibili in Gestione API di Azure. Ecco il criterio che invia un messaggio di richiesta HTTP all'Hub eventi di Azure.

       <log-to-eventhub logger-id="conferencelogger" partition-id="0">
       @{
           var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                       context.Request.Method,
                                                       context.Request.Url.Path + context.Request.Url.QueryString);

           var body = context.Request.Body?.As<string>(true);
           if (body != null && body.Length > 1024)
           {
               body = body.Substring(0, 1024);
           }

           var headers = context.Request.Headers
                                  .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                  .ToArray<string>();

           var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

           return "request:"   + context.Variables["message-id"] + "\n"
                               + requestLine + headerString + "\r\n" + body;
       }
       </log-to-eventhub>

### Dichiarazione di criteri
È necessario evidenziare alcuni aspetti di questa espressione di criteri. Il criterio log-to-eventhub ha un attributo denominato logger-id che fa riferimento al nome del logger creato nel servizio Gestione API. I dettagli relativi alla configurazione di un logger dell'Hub eventi nel servizio Gestione API sono disponibili nel documento [Come registrare eventi nell'Hub eventi di Azure in Gestione API di Azure](api-management-howto-log-event-hubs.md). Il secondo attributo è un parametro opzionale che indica all'Hub eventi la partizione in cui archiviare il messaggio. Hub eventi usa le partizioni per abilitare la scalabilità e richiede almeno due partizioni. Il recapito ordinato dei messaggi è garantito solo entro una partizione. Se non si indica all'Hub eventi la partizione in cui inserire il messaggio, verrà usato un algoritmo round-robin per distribuire il carico. È tuttavia possibile che ciò provochi l'elaborazione non ordinata di alcuni messaggi.

### Partizioni
Per assicurarsi che i messaggi vengano recapitati ai consumer in base all'ordine stabilito e sfruttare i vantaggi della capacità di distribuzione del carico delle partizioni, è possibile scegliere di inviare messaggi di richiesta HTTP a una partizione e messaggi di risposta HTTP a una seconda partizione. In questo modo si assicurerà una distribuzione uniforme del carico e sarà possibile garantire che tutte le richieste e le risposte vengano utilizzate nell'ordine stabilito. È possibile che una risposta venga utilizzata prima della risposta corrispondente, ma questo non costituisce un problema, perché è disponibile un meccanismo diverso per la correlazione delle richieste alle risposte e si sa che le richieste precedono sempre le risposte.

### Payload HTTP
Dopo avere compilato `requestLine`, verificare se il corpo della richiesta deve essere troncato. Il corpo della richiesta viene troncato solo a 1024. È possibile aumentare questo valore, ma i singoli messaggi dell'Hub eventi sono limitati a 256 KB, quindi è probabile che alcuni corpi di messaggio HTTP non rientrino in un singolo messaggio. Durante la registrazione e l'analisi, è possibile ottenere una quantità significativa di informazioni semplicemente dalla riga e dalle intestazioni della richiesta HTTP. Molte richieste API restituiscono inoltre corpi piccoli, quindi la perdita del valore di informazioni derivante dal troncamento di corpi di grandi dimensioni è abbastanza ridotta rispetto alla riduzione dei costi di trasferimento, elaborazione e archiviazione per la conservazione di tutti i contenuti del corpo. È infine necessario notare, in merito all'elaborazione del corpo, che occorre passare `true` al metodo As<string>() poiché si stanno leggendo i contenuti del corpo, ma è anche necessario che l'API back-end sia in grado di leggere il corpo. Se si passa true a questo metodo, il corpo verrà sottoposto a buffering, in modo che sia possibile leggerlo una seconda volta. È importante tenere in considerazione questo aspetto se si usa un'API che carica file di grandi dimensioni o usa polling di lunga durata. In questi casi è consigliabile evitare completamente la lettura del corpo.

### Intestazioni HTTP
Le intestazioni HTTP possono essere semplicemente trasferite nel formato del messaggio sotto forma di semplice coppia chiave/valore. Alcuni campi che richiedono una sicurezza specifica sono stati eliminati per evitare la diffusione non necessaria delle informazioni relative alle credenziali. È poco probabile che le chiavi API e altre credenziali vengano usate per finalità analitiche. Se si vuole effettuare un'analisi dell'utente e del prodotto specifico usato, sarà possibile ottenere queste informazioni dall'oggetto `context` e aggiungerle al messaggio.
### Metadati del messaggio
Durante la creazione del messaggio completo da inviare all'hub eventi, la prima riga non fa effettivamente parte del messaggio `application/http`. La prima riga include metadati aggiuntivi che indicano se il messaggio è un messaggio di richiesta o di risposta e l'ID del messaggio, che viene usato per correlare le richieste e l risposte. L'ID del messaggio viene creato mediante un altro criterio, analogo al seguente:

    <set-variable name="message-id" value="@(Guid.NewGuid())" />

È anche possibile creare il messaggio di richiesta, archiviarlo in una variabile fino alla restituzione della risposta e quindi semplicemente inviare la richiesta e la risposta come singolo messaggio, ma l'invio indipendente di richiesta e risposta e l'uso di un ID del messaggio per correlarle consente di ottenere una maggiore flessibilità a livello di dimensioni del messaggio, di sfruttare i vantaggi offerti dalle partizioni multiple e di mantenere al tempo stesso l'ordine dei messaggi, oltre a visualizzare più rapidamente la richiesta nel dashboard di registrazione. In alcuni scenari è anche possibile che non venga mai inviata all'hub eventi alcuna risposta valida, probabilmente a causa di un errore della richiesta nel servizio Gestione API, ma viene comunque mantenuto un record della richiesta.

Il criterio per l'invio del messaggio di risposta HTTP è molto simile alla risposta, quindi la configurazione completa del criterio sarà analoga alla seguente:

      <policies>
      	<inbound>
      		<set-variable name="message-id" value="@(Guid.NewGuid())" />
      		<log-to-eventhub logger-id="conferencelogger" partition-id="0">
              @{
                  var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                              context.Request.Method,
                                                              context.Request.Url.Path + context.Request.Url.QueryString);

                  var body = context.Request.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Request.Headers
                                       .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                       .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                       .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "request:"   + context.Variables["message-id"] + "\n"
                                      + requestLine + headerString + "\r\n" + body;
              }
          </log-to-eventhub>
      	</inbound>
      	<backend>
      		<forward-request follow-redirects="true" />
      	</backend>
      	<outbound>
      		<log-to-eventhub logger-id="conferencelogger" partition-id="1">
              @{
                  var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                                      context.Response.StatusCode,
                                                      context.Response.StatusReason);

                  var body = context.Response.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Response.Headers
                                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                                  .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "response:"  + context.Variables["message-id"] + "\n"
                                      + statusLine + headerString + "\r\n" + body;
             }
          </log-to-eventhub>
      	</outbound>
      </policies>

Il criterio `set-variable` crea un valore accessibile dal criterio `log-to-eventhub` nella sezione `<inbound>` e nella sezione `<outbound>`.

## Ricezione di eventi dall'Hub eventi
Gli eventi dall'Hub eventi di Azure vengono ricevuti mediante il [protocollo AMQP](http://www.amqp.org/). Il team del bus di servizio Microsoft ha reso disponibili le librerie client per semplificare l'utilizzo degli eventi. Sono supportati due approcci diversi, ovvero la modalità *consumer diretto* e l'uso della classe `EventProcessorHost`. Gli esempi relativi a questi due approcci sono disponibili nella [Guida alla programmazione di Hub eventi](../event-hubs/event-hubs-programming-guide.md). In breve, `Direct Consumer` offre il controllo completo e `EventProcessorHost` esegue alcune operazioni di base ma include alcune ipotesi in merito al modo in cui gli eventi verranno elaborati.

### EventProcessorHost
Per semplificare, in questo esempio verrà usato l'approccio `EventProcessorHost`, anche se è possibile che non sia ottimale per questo scenario specifico. `EventProcessorHost` esegue le operazioni necessarie per gestire automaticamente eventuali errori di threading relativi a una classe specifica del processore di eventi. In questo scenario, tuttavia, si converte semplicemente il messaggio in un altro formato e lo si passa a un altro servizio mediante un metodo asincrono. Non è necessario aggiornare lo stato condiviso e quindi non si rischia che si verifichino problemi di threading. Nella maggior parte degli scenari la scelta migliore è probabilmente costituita da `EventProcessorHost`, che è sicuramente l'opzione più semplice.

### IEventProcessor
Il concetto centrale dell'uso di `EventProcessorHost` consiste nel creare un'implementazione dell'interfaccia `IEventProcessor`, che include il metodo `ProcessEventAsync`. Gli elementi fondamentali del metodo sono illustrati di seguito:

  async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages) {

           foreach (EventData eventData in messages)
           {
               _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

               try
               {
                   var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
                   await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
               }
               catch (Exception ex)
               {
                   _Logger.LogError(ex.Message);
               }
           }
            ... checkpointing code snipped ...
        }

Un elenco di oggetti EventData viene passato al metodo e viene eseguita l'iterazione dell'elenco. I byte di ogni metodo vengono analizzati in un oggetto HttpMessage e questo oggetto viene passato a un'istanza di IHttpMessageProcessor.

### HttpMessage
L'istanza `HttpMessage` contiene tre parti di dati:

      public class HttpMessage
       {
           public Guid MessageId { get; set; }
           public bool IsRequest { get; set; }
           public HttpRequestMessage HttpRequestMessage { get; set; }
           public HttpResponseMessage HttpResponseMessage { get; set; }

        ... parsing code snipped ...

      }

L'istanza `HttpMessage` contiene un GUID `MessageId` che consente di connettere la richiesta HTTP alla risposta HTTP corrispondente e un valore booleano che indica se l'oggetto contiene un'istanza di HttpRequestMessage e HttpResponseMessage. Usando le classi HTTP predefinite da `System.Net.Http`, è possibile sfruttare i vantaggi del codice di analisi `application/http` incluso in `System.Net.Http.Formatting`.

### IHttpMessageProcessor
L'istanza `HttpMessage` viene quindi inoltrata all'implementazione di `IHttpMessageProcessor`, che è un'interfaccia creata per disaccoppiare la ricezione e l'interpretazione dell'evento dall'Hub eventi di Azure e l'effettiva elaborazione dell'evento.


## Inoltro del messaggio HTTP
Per questo esempio è possibile provare a effettuare il push della richiesta HTTP in [Runscope](http://www.runscope.com). Runscope è un servizio basato sul cloud specializzato nel debug, nella registrazione e nel monitoraggio HTTP. È disponibile un livello gratuito, quindi è possibile provare a usarlo per visualizzare il passaggio in tempo reale delle richieste HTTP nel servizio Gestione API.

L'implementazione `IHttpMessageProcessor` ha un aspetto analogo al seguente,

      public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
       {
           private HttpClient _HttpClient;
           private ILogger _Logger;
           private string _BucketKey;
           public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
           {
               _HttpClient = httpClient;
               var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
               _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
               _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
               _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
               _Logger = logger;
           }

           public async Task ProcessHttpMessage(HttpMessage message)
           {
               var runscopeMessage = new RunscopeMessage()
               {
                   UniqueIdentifier = message.MessageId
               };

               if (message.IsRequest)
               {
                   _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
                   runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
               }
               else
               {
                   _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
                   runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
               }

               var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
               messagesLink.BucketKey = _BucketKey;
               messagesLink.RunscopeMessage = runscopeMessage;
               var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
               _Logger.LogDebug("Request sent to Runscope");
           }
       }

È possibile sfruttare una [libreria client esistente per Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha), che semplifica il push delle istanze `HttpRequestMessage` e `HttpResponseMessage` nel servizio. Per accedere all'API Runscope, saranno necessari un account e una chiave API. Le istruzioni per ottenere una chiave API sono disponibili nello screencast relativo alla [creazione di applicazioni per l'accesso all'API Runscope](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api).

## Esempio completo
Il [codice sorgente](https://github.com/darrelmiller/ApimEventProcessor) e i test per l'esempio sono disponibili su Github. Per eseguire l'esempio, è necessario avere un [servizio Gestione API](api-management-get-started.md), un [hub eventi connesso](api-management-howto-log-event-hubs.md) e un [account di archiviazione](../storage/storage-create-storage-account.md).

L'esempio è costituito da una semplice applicazione console che rimane in attesa di eventi provenienti dall'Hub eventi, quindi li converte in oggetti `HttpRequestMessage` e `HttpResponseMessage` e li inoltra all'API Runscope.

L'immagine animata seguente illustra l'effettuazione di una richiesta a un'API nel portale per sviluppatori, la ricezione, l'elaborazione e l'inoltro del messaggio nell'applicazione console e quindi la visualizzazione della richiesta e della risposta in Runscope Traffic Inspector.

![Illustrazione dell'inoltro di una richiesta a Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## Riepilogo
Il servizio Gestione API di Azure è la posizione ideale per acquisire il traffico HTTP verso e dalle API. Hub eventi di Azure è una soluzione a scalabilità elevata e costi ridotti per l'acquisizione e l'inserimento del traffico in sistemi di elaborazione secondari per operazioni di registrazione e monitoraggio e per altre analisi avanzate. La connessione a sistemi di monitoraggio del traffico di terze parti come Runscope è semplice quanto scrivere qualche dozzina di righe di codice.

<!---HONumber=Nov15_HO2-->