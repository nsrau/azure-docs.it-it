<properties 
	pageTitle="Come usare Controllo API per tenere traccia delle chiamate in Gestione API di Azure" 
	description="Informazioni su come tenere traccia delle chiamate usando Controllo API in Gestione API di Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Come usare Controllo API per tenere traccia delle chiamate in Gestione API di Azure

Gestione API (Anteprima) fornisce uno strumento per il controllo delle API per aiutare gli sviluppatori ad eseguire il debug e la risoluzione dei problemi correlati alle API. È possibile usare Controllo API sia a livello di codice dalle applicazioni che direttamente dal portale di sviluppo. Questa guida contiene una procedura dettagliata sull'uso di Controllo API.

## Contenuto dell'argomento

-   [Usare Controllo API per tenere traccia di una chiamata][Usare Controllo API per tenere traccia di una chiamata]
-   [Esaminare la traccia][Esaminare la traccia]
-   [Passaggi successivi][Passaggi successivi]

## <a name="trace-call"> </a>Usare Controllo API per tenere traccia di una chiamata

Per usare Controllo API, aggiungere un'intestazione di richiesta **ocp-apim-trace: true** alla chiamata di operazioni, quindi scaricare ed esaminare la traccia usando l'URL indicato dall'intestazione di risposta **ocp-apim-trace-location**. Questa operazione può essere eseguita sia a livello di codice che direttamente dal portale per sviluppatori.

In questa esercitazione viene illustrato come usare Controllo API per tenere traccia delle operazioni usando l'API Echo.

> Ogni istanza del servizio Gestione API è preconfigurata con un'API Echo utilizzabile per sperimentare e ottenere altre informazioni su Gestione API. L'API Echo restituisce qualunque input gli venga inviato. Per usarla, è possibile richiamare un qualsiasi verbo HTTP. Il valore restituito sarà semplicemente ciò che si è inviato.

Per iniziare, fare clic su **Portale di sviluppo** nel portale di Azure per il servizio Gestione API. Verrà visualizzato il portale amministrativo di Gestione API.

> Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][Creare un'istanza del servizio Gestione API] nell'esercitazione [Introduzione a Gestione API di Azure][Introduzione a Gestione API di Azure].

![Portale per sviluppatori di Gestione API][Portale per sviluppatori di Gestione API]

È possibile chiamare le operazioni direttamente dal portale per sviluppatori, che consente di visualizzare e testare le operazioni di un'API in tutta comodità. In questo passaggio dell'esercitazione verrà chiamato il metodo **Get Resource** dell'**API Echo**.

Fare clic su **API** dal menu superiore, quindi fare clic su **API Echo**.

![API Echo][API Echo]

> Se è stata configurata una sola API o se ne è visibile solo una per l'account, facendo clic sulle API vengono visualizzate le operazioni per l'API.

Selezionare l'operazione **GET su risorsa** e fare clic su **Apri console**.

![Open console][Open console]

Mantenere i valori predefiniti dei parametri e selezionare la chiave della sottoscrizione dall'elenco a discesa **subscription-key**.

Digitare **ocp-apim-trace: true** nella casella di testo **Intestazioni della richiesta** e fare clic su **GET HTTP**.

![GET HTTP][GET HTTP]

Le intestazioni di risposta conterranno un elemento **ocp-apim-trace-location** con un valore simile a quello dell'esempio seguente.

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

È possibile scaricare la traccia dal percorso specificato e quindi rivederla, come illustrato nel passaggio seguente.

## <a name="inspect-trace"> </a>Esaminare la traccia

Per rivedere i valori nella traccia, scaricare il file di traccia dell'URL **ocp-apim-trace-location**. Si tratta di un file di testo in formato JSON, contenente voci simili a quelle illustrate nell'esempio seguente.

    {
      "validityGuid":"a43a07a03de04fcb8b1425df38514742",
      "logEntries":[
        {
            "timestamp":"2014-05-03T21:00:13.2182473Z",
            "source":"Microsoft.WindowsAzure.ApiManagement.Proxy.Gateway.Handlers.DebugLoggingHandler",
            "data":{
            "originalRequest":{
                "method":"GET",
                "url":"https://contosoltd.current.int-azure-api.net/echo/resource?param1=sample&subscription-key=...",
                "headers":[
                {
                    "name":"ocp-apim-tracing",
                    "value":"true"
                },
                {
                    "name":"Host",
                    "value":"contosoltd.current.int-azure-api.net"
                }
                ]
            }
            }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"request handler",
          "data":{
            "configuration":{
              "api":{
                "from":"echo",
                "to":"http://echoapi.cloudapp.net/api"
              },
              "operation":{
                "method":"GET",
                "uriTemplate":"/resource"
              },
              "user":{
                "id":1,
                "groups":[
              
                ]
              },
              "product":{
                "id":1
              }
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "message":"Sending request to the service.",
            "request":{
              "method":"GET",
              "url":"http://echoapi.cloudapp.net/api/resource?param1=sample&subscription-key=...",
              "headers":[
                {
                  "name":"X-Forwarded-For",
                  "value":"138.91.78.77"
                },
                {
                  "name":"ocp-apim-tracing",
                  "value":"true"
                }
              ]
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "status":{
              "code":200,
              "reason":"OK"
            },
            "headers":[
              {
                "name":"Pragma",
                "value":"no-cache"
              },
              {
                "name":"Host",
                "value":"echoapi.cloudapp.net"
              },
              {
                "name":"X-Forwarded-For",
                "value":"138.91.78.77"
              },
              {
                "name":"ocp-apim-tracing",
                "value":"true"
              },
              {
                "name":"Content-Length",
                "value":"0"
              },
              {
                "name":"Cache-Control",
                "value":"no-cache"
              },
              {
                "name":"Expires",
                "value":"-1"
              },
              {
                "name":"Server",
                "value":"Microsoft-IIS/8.0"
              },
              {
                "name":"X-AspNet-Version",
                "value":"4.0.30319"
              },
              {
                "name":"X-Powered-By",
                "value":"Azure API Management - http://api.azure.com/,ASP.NET"
              },
              {
                "name":"Date",
                "value":"Sat, 03 May 2014 21:00:17 GMT"
              }
            ]
          }
        }
      ]
    }

## <a name="next-steps"> </a>Passaggi successivi

-   Vedere gli altri argomenti nell'esercitazione [Introduzione alla configurazione API avanzata][Introduzione alla configurazione API avanzata].

  [Usare Controllo API per tenere traccia di una chiamata]: #trace-call
  [Esaminare la traccia]: #inspect-trace
  [Passaggi successivi]: #next-steps
  [Creare un'istanza del servizio Gestione API]: ../api-management-get-started/#create-service-instance
  [Introduzione a Gestione API di Azure]: ../api-management-get-started
  [Portale per sviluppatori di Gestione API]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
  [API Echo]: ./media/api-management-howto-api-inspector/api-management-echo-api.png
  [Open console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
  [GET HTTP]: ./media/api-management-howto-api-inspector/api-management-http-get.png
  [Introduzione alla configurazione API avanzata]: ../api-management-get-started-advanced

<!--HONumber=46--> 

<!--HONumber=46--> 
