---
title: Trigger delle app per le API del servizio app | Microsoft Docs
description: Come implementare i trigger in un'app per le API in Servizio app di Azure.
services: logic-apps
documentationcenter: .net
author: guangyang
manager: wpickett
editor: jimbe

ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: rachelap

---
# Trigger delle app per le API del servizio app di Azure
> [!NOTE]
> Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per le API.
> 
> 

## Overview
Questo articolo spiega come implementare i trigger delle app per le API e usarli da un'app per la logica.

Tutti i frammenti di codice in questo argomento sono stati copiati dall'[esempio di codice di app per le API FileWatcher](http://go.microsoft.com/fwlink/?LinkId=534802).

Per la corretta compilazione ed esecuzione del codice riportato in questo articolo, sarà inoltre necessario scaricare il pacchetto NuGet seguente: [http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/).

## Cosa sono i trigger delle app per le API?
In genere, le app per le API generano eventi in modo che i client dell'app per le API possano eseguire l'azione appropriata in risposta all'evento. Il meccanismo basato sull'API REST che supporta questo scenario è definito come trigger dell'app per le API.

Si supponga, ad esempio, che il codice client usi l'[app per le API Twitter Connector](../app-service-logic/app-service-logic-connector-twitter.md) e che il codice debba eseguire un'azione in base ai nuovi tweet che contengono parole specifiche. In questo caso, è possibile configurare un trigger di polling o di push.

## Trigger di polling e trigger di push
Attualmente sono supportati due tipi di trigger:

* Trigger di polling: il client esegue il polling dell'app per le API per la notifica di un evento generato
* Trigger di push: il client riceve una notifica dall'app per le API quando viene generato un evento

### Trigger di polling
Un trigger di polling viene implementato come un'API REST normale e prevede che i client, ad esempio un'app per la logica, ne eseguano il polling per ricevere la notifica. Mentre il client può mantenere il suo stato, il trigger di polling stesso è senza stato.

Le informazioni seguenti relative ai pacchetti di richiesta e di risposta illustrano alcuni aspetti fondamentali del contratto del trigger di polling:

* Richiesta
  * Metodo HTTP: GET
  * Parametri
    * triggerState: questo parametro facoltativo consente ai client di specificare il proprio stato in modo che il trigger di polling possa decidere correttamente se restituire o meno una notifica in base allo stato specificato.
    * Parametri specifici dell'API
* Response
  * Codice di stato **200**: la richiesta è valida ed è presente una notifica dal trigger. Il contenuto della notifica si troverà nel corpo della risposta. Un'intestazione "Retry-After" nella risposta indica che è necessario recuperare i dati della notifica aggiuntivi con una chiamata di richiesta successiva.
  * Codice di stato **202**: la richiesta è valida ma non sono presenti nuove notifiche dal trigger.
  * Codice di stato **4xx**: la richiesta non è valida. Il client non deve ripetere la richiesta.
  * Codice di stato **5xx**: durante la richiesta si è verificato un errore interno del server e/o un problema temporaneo. Il client deve ripetere la richiesta.

Il frammento di codice seguente descrive come implementare un trigger di polling.

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

Per testare il trigger di polling, seguire questa procedura:

1. Distribuire l'app per le API impostando l'autenticazione sul livello di accesso **Pubblico (anonimo)**.
2. Chiamare l'operazione **touch** per creare un file. L'immagine seguente illustra una richiesta di esempio tramite Postman. ![Chiamata dell'operazione Touch tramite Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. Chiamare il trigger di polling con il parametro **triggerState** impostato su un timestamp prima di procedere al passaggio 2. L'immagine seguente illustra la richiesta di esempio tramite Postman. ![Chiamata del trigger di polling tramite Postman](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### Trigger di push
Un trigger di push viene implementato come un'API REST normale che esegue il push delle notifiche ai client registrati per ricevere una notifica quando vengono generati eventi specifici.

Le informazioni seguenti relative ai pacchetti di richiesta e di risposta illustrano alcuni aspetti fondamentali del contratto del trigger di push:

* Richiesta
  * Metodo HTTP: PUT
  * Parametri
    * triggerId: obbligatorio. Si tratta di una stringa opaca (ad esempio un GUID) che rappresenta la registrazione di un trigger di push.
    * callbackUrl: obbligatorio. Si tratta dell'URL del callback da richiamare quando viene generato l'evento. La chiamata è una semplice chiamata HTTP POST.
    * Parametri specifici dell'API
* Response
  * Codice di stato **200**: la richiesta di registrazione del client è riuscita.
  * Codice di stato **4xx**: la richiesta non è valida. Il client non deve ripetere la richiesta.
  * Codice di stato **5xx**: durante la richiesta si è verificato un errore interno del server e/o un problema temporaneo. Il client deve ripetere la richiesta.
* Callback
  * Metodo HTTP: POST
  * Corpo della richiesta: contenuto della notifica.

Il frammento di codice seguente descrive come implementare un trigger di push:

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

Per testare il trigger di polling, seguire questa procedura:

1. Distribuire l'app per le API impostando l'autenticazione sul livello di accesso **Pubblico (anonimo)**.
2. Passare a [http://requestb.in/](http://requestb.in/) per creare un oggetto RequestBin da usare come URL di callback.
3. Chiamare il trigger di push con un GUID come **triggerId** e un URL RequestBin come **callbackUrl**. ![Chiamata del trigger di push tramite Postman](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. Chiamare l'operazione **touch** per creare un file. L'immagine seguente illustra una richiesta di esempio tramite Postman. ![Chiamata dell'operazione Touch tramite Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. Controllare l'oggetto RequestBin per assicurarsi che il callback del trigger di push venga richiamato con l'output delle proprietà. ![Chiamata del trigger di polling tramite Postman](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### Descrivere i trigger nella definizione dell'API
Dopo aver implementato i trigger e distribuito l'app per le API in Azure, passare al pannello **Definizione API** nel portale di anteprima di Azure. Si noterà che i trigger vengono riconosciuti automaticamente nell'interfaccia utente che è basata sulla definizione API Swagger 2.0 dell'app per le API.

![Pannello Definizione API](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

Se si fa clic sul pulsante **Scarica Swagger** e si apre il file JSON, verranno visualizzati risultati simili ai seguenti:

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

La proprietà **x-ms-schedular-trigger** rappresenta il modo in cui i trigger sono descritti nella definizione dell'API e viene aggiunta automaticamente dal gateway dell'app per le API quando si richiede la definizione dell'API tramite il gateway, se la richiesta soddisfa uno dei criteri seguenti. È anche possibile aggiungere questa proprietà manualmente.

* Trigger di polling
  * Se il metodo HTTP è **GET**.
  * Se la proprietà **operationId** contiene la stringa **trigger**.
  * Se la proprietà **parameters** include un parametro con una proprietà **name** impostata su **triggerState**.
* Trigger di push
  * Se il metodo HTTP è **PUT**.
  * Se la proprietà **operationId** contiene la stringa **trigger**.
  * Se la proprietà **parameters** include un parametro con una proprietà **name** impostata su **triggerId**.

## Usare i trigger di app per le API nelle app per la logica
### Elencare e configurare i trigger di app per le API nella finestra di progettazione di app per la logica
Se si crea un'app per la logica nello stesso gruppo di risorse dell'app per le API, sarà possibile aggiungerla all'area di disegno della finestra di progettazione, semplicemente facendo clic su di essa. Vedere le immagini seguenti per un esempio:

![Trigger nella finestra di progettazione di app per la logica](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![Configurazione del trigger di polling nella finestra di progettazione di app per la logica](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![Configurazione del trigger di push nella finestra di progettazione di app per la logica](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## Ottimizzare i trigger di app per le API per le app per la logica
Dopo aver aggiunto i trigger a un'app per le API, è possibile eseguire alcune operazioni per migliorare l'esperienza durante l'uso dell'app per le API in un'app per la logica.

Ad esempio, impostare il parametro **triggerState** per i trigger di polling sull'espressione seguente nell'app per la logica. Questa espressione deve valutare l'ultima chiamata del trigger dall'app per la logica e restituire il relativo valore.

    @coalesce(triggers()?.outputs?.body?['triggerState'], '')

NOTA: per una spiegazione delle funzioni usate nell'espressione precedente, fare riferimento alla documentazione relativa al [linguaggio di definizione del flusso di lavoro delle app per la logica](https://msdn.microsoft.com/library/azure/dn948512.aspx).

Gli utenti dell'app per la logica dovranno specificare l'espressione precedente per il parametro **triggerState** durante l'utilizzo del trigger. Questo valore può essere impostato come predefinito dalla finestra di progettazione di app per la logica tramite la proprietà di estensione **x-ms-scheduler-recommendation**. È possibile impostare la proprietà di estensione **x-ms-visibility** sul valore *internal* in modo che il parametro stesso non venga visualizzato nella finestra di progettazione. Questo scenario è mostrato nel frammento di codice seguente.

    "/api/Messages/poll": {
      "get": {
        "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

Per i trigger di push, il parametro **triggerId** deve identificare in modo univoco l'app per la logica. In base a una procedura consigliata, è opportuno impostare questa proprietà sul nome del flusso di lavoro tramite l'espressione seguente:

    @workflow().name

Grazie all'uso delle proprietà di estensione **x-ms-scheduler-recommendation** e **x-ms-visibility** nella propria definizione API, l'app per le API può indicare alla finestra di progettazione dell'app per la logica di impostare automaticamente questa espressione.

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### Aggiungere le proprietà di estensione nella definizione dell'API
Per aggiungere ulteriori informazioni sui metadati, ad esempio le proprietà di estensione **x-ms-scheduler-recommendation** e **x-ms-visibility**, alla definizione dell'API sono disponibili due modi, uno statico e uno dinamico.

Per i metadati statici, è possibile modificare direttamente il file */metadata/apiDefinition.swagger.json* nel progetto e aggiungere manualmente le proprietà.

Per le app per le API che usano metadati dinamici, è possibile modificare il file SwaggerConfig.cs e aggiungervi un filtro per le operazioni in grado di aggiungere queste estensioni.

    GlobalConfiguration.Configuration 
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


Di seguito è riportato un esempio di come è possibile implementare questa classe per semplificare lo scenario relativo ai metadati dinamici.

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }


<!---HONumber=AcomDC_0831_2016-->