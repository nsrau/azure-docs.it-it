---
title: Esecuzione di più servizi dipendenti con .NET Core e VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: ff2031395eb14cc1b9d8fda2150523c37fdcab69
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57870948"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Sviluppo multiservizio con Azure Dev Spaces

Questa esercitazione illustra lo sviluppo di applicazioni multiservizio con Azure Dev Spaces, insieme ad alcuni vantaggi aggiuntivi offerti da Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Chiamare un servizio in esecuzione in un contenitore separato

In questa sezione si creerà un secondo servizio, `mywebapi`, che verrà chiamato da `webfrontend`. Ogni servizio viene eseguito in contenitori separati. Verrà quindi eseguito il debug in entrambi i contenitori.

![Più contenitori](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Scaricare il codice di esempio per *mywebapi*
Per motivi di tempo, scarichiamo il codice di esempio da un repository GitHub. Passare a https://github.com/Azure/dev-spaces e selezionare **Clone or Download** (Clona o scarica) per scaricare il repository GitHub. Il codice per questa sezione è disponibile in `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Eseguire *mywebapi*
1. Aprire la cartella `mywebapi` in una *finestra di VS Code separata*.
1. Aprire il **riquadro comandi** (usando il menu **Visualizza | Riquadro comandi**) e usare il completamento automatico per digitare e selezionare questo comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Questo comando non deve essere confuso con il comando `azds prep` che configura il progetto per la distribuzione.
1. Premere F5 e attendere la compilazione e la distribuzione del servizio. Il servizio è pronto quando l'*applicazione viene avviata. Premere CTRL+C per arrestare.* viene visualizzato un messaggio nella console di debug.
1. L'URL dell'endpoint sarà simile a `http://localhost:<portnumber>`. **Suggerimento: la barra di stato di VS Code visualizza un URL selezionabile.** Potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito nello spazio di sviluppo in Azure. Il motivo dell'indirizzo localhost è che `mywebapi` non ha definito alcun endpoint pubblico ed è accessibile solo dall'interno dell'istanza di Kubernetes. Per praticità e per agevolare l'interazione con il servizio privato nel computer locale, Azure Dev Spaces crea un tunnel SSH temporaneo al contenitore in esecuzione in Azure.
1. Quando `mywebapi` è pronto, aprire il browser all'indirizzo localhost. Aggiungere `/api/values` all'URL per richiamare l'API GET predefinito per `ValuesController`.
1. Se tutte le operazioni hanno avuto esito positivo, si dovrebbe ricevere una risposta dal servizio `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Creare una richiesta da *webfrontend* a *mywebapi*
Ora scriviamo il codice in `webfrontend` che crea una richiesta a `mywebapi`.
1. Passare alla finestra di VS Code per `webfrontend`.
1. *Sostituire* il codice per il metodo About in `HomeController.cs`:

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

        return View();
    }
    ```

L'esempio di codice precedente inoltra l'intestazione `azds-route-as` dalla richiesta in ingresso alla richiesta in uscita. Più avanti verrà illustrato come ciò sia utile ai team per lo [sviluppo collaborativo](team-development-netcore.md).

### <a name="debug-across-multiple-services"></a>Eseguire il debug tra più servizi
1. A questo punto `mywebapi` dovrebbe ancora essere in esecuzione con il debugger collegato. In caso contrario, premere F5 nel progetto `mywebapi`.
1. Impostare un punto di interruzione nel metodo `Get(int id)` che gestisce richieste GET `api/values/{id}`.
1. Nel progetto `webfrontend` impostare un punto di interruzione prima che invii una richiesta GET a `mywebapi/api/values`.
1. Premere F5 nel progetto `webfrontend`.
1. Richiamare l'app Web e scorrere il codice in entrambi i servizi.
1. Nell'app Web la pagina delle informazioni visualizza un messaggio concatenato dai due servizi: "Salve da webfrontend e Salve da mywebapi."

### <a name="automatic-tracing-for-http-messages"></a>Traccia automatica per i messaggi HTTP
Come si sarà notato, nonostante *webfrontend* non contenga codice speciale per stampare la chiamata HTTP effettuata a *mywebapi*, nella finestra di output vengono visualizzati messaggi di traccia HTTP:
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --gyk-> webfrontend:
   GET /Home/About HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend-668b7ddb9f-n5rhj --pu5-> mywebapi:
   GET /api/values/1 HTTP/1.1

// Response from *mywebapi*
webfrontend-668b7ddb9f-n5rhj <-pu5-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-gyk-- webfrontend:
   HTTP/1.1 200 OK
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="utf-8" />
       <meta name="viewport" content="width=device-width, initial-sc...<[TRUNCATED]>
```
Questo è uno dei vantaggi collaterali offerti dalla strumentazione di Dev Spaces. Vengono inseriti componenti che monitorano l'esecuzione delle richieste HTTP nel sistema per facilitare il monitoraggio di chiamate multiservizio complesse durante lo sviluppo.


### <a name="well-done"></a>Ecco fatto!
È ora disponibile un'applicazione multicontenitore in cui ogni contenitore può essere sviluppato e distribuito separatamente.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sullo sviluppo in team in Dev Spaces](team-development-netcore.md)