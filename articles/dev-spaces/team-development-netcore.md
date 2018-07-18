---
title: Team di sviluppo con Azure Dev Spaces usando .NET Core e Visual Studio Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenitori
manager: douge
ms.openlocfilehash: e186676b9c912d5c8453d9882e61b90c57a6362c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37933426"
---
# <a name="team-development-with-azure-dev-spaces"></a>Team di sviluppo con Azure Dev Spaces

In questa esercitazione si apprenderà come usare più spazi di sviluppo che vengono eseguiti contemporaneamente in diversi ambienti di sviluppo, tenendo separato il lavoro in spazi di sviluppo separati nello stesso cluster.

## <a name="call-a-service-running-in-a-separate-container"></a>Chiamare un servizio in esecuzione in un contenitore separato

In questa sezione si crea un secondo servizio, `mywebapi`, a cui `webfrontend` assegna un nome. Ogni servizio viene eseguito in contenitori separati. Verrà quindi eseguito il debug in entrambi i contenitori.

![Più contenitori](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Scaricare il codice di esempio per *mywebapi*
Per motivi di tempo, scarichiamo il codice di esempio da un repository GitHub. Passare a https://github.com/Azure/dev-spaces e selezionare **Clone or Download** (Clona o scarica) per scaricare il repository GitHub. Il codice per questa sezione è disponibile in `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Eseguire *mywebapi*
1. Aprire la cartella `mywebapi` in una *finestra di VS Code separata*.
1. Aprire il **riquadro comandi** (usando il menu **Visualizza | Riquadro comandi**) e usare il completamento automatico per digitare e selezionare questo comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 
1. Premere F5 e attendere la compilazione e la distribuzione del servizio. Il servizio è pronto quando viene visualizzata la barra di debug di VS Code.
1. L'URL dell'endpoint sarà simile a http://localhost:\<portnumber\>. **Suggerimento: la barra di stato di VS Code visualizza un URL selezionabile.** Potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito nello spazio di sviluppo in Azure. Il motivo dell'indirizzo localhost è che `mywebapi` non ha definito alcun endpoint pubblico ed è accessibile solo dall'interno dell'istanza di Kubernetes. Per praticità e per agevolare l'interazione con il servizio privato nel computer locale, Azure Dev Spaces crea un tunnel SSH temporaneo al contenitore in esecuzione in Azure.
1. Quando `mywebapi` è pronto, aprire il browser all'indirizzo localhost. Aggiungere `/api/values` all'URL per richiamare l'API GET predefinito per `ValuesController`. 
1. Se tutte le operazioni hanno avuto esito positivo, si dovrebbe ricevere una risposta dal servizio `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Creare una richiesta da *webfrontend* a *mywebapi*
Ora scriviamo il codice in `webfrontend` che crea una richiesta a `mywebapi`.
1. Passare alla finestra di VS Code per `webfrontend`.
1. *Sostituire* il codice per il metodo About:

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

L'esempio di codice precedente inoltra l'intestazione `azds-route-as` dalla richiesta in ingresso alla richiesta in uscita. Più avanti si apprenderà come ciò aiuti i team nello sviluppo collaborativo.

### <a name="debug-across-multiple-services"></a>Eseguire il debug tra più servizi
1. A questo punto `mywebapi` dovrebbe ancora essere in esecuzione con il debugger collegato. In caso contrario, premere F5 nel progetto `mywebapi`.
1. Impostare un punto di interruzione nel metodo `Get(int id)` che gestisce richieste GET `api/values/{id}`.
1. Nel progetto `webfrontend` impostare un punto di interruzione prima che invii una richiesta GET a `mywebapi/api/values`.
1. Premere F5 nel progetto `webfrontend`.
1. Richiamare l'app Web e scorrere il codice in entrambi i servizi.
1. Nell'app Web la pagina delle informazioni visualizza un messaggio concatenato dai due servizi: "Salve da webfrontend e Salve da mywebapi".


Ecco fatto! È ora disponibile un'applicazione multicontenitore in cui ogni contenitore può essere sviluppato e distribuito separatamente.

## <a name="learn-about-team-development"></a>Informazioni sui team di sviluppo

[!INCLUDE[](includes/team-development-1.md)]

Provalo. Passare alla finestra di VS Code per `mywebapi` e apportare una modifica al codice per il metodo `string Get(int id)`, ad esempio:

```csharp
[HttpGet("{id}")]
public string Get(int id)
{
    return "mywebapi now says something new";
}
```


[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
