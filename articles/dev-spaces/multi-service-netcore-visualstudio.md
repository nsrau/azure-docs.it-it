---
title: Esecuzione di più servizi dipendenti con .NET Core e Visual Studio
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
ms.openlocfilehash: dd90dee2f973bb26a43706eb77f15778cb9116a0
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502971"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Sviluppo multiservizio con Azure Dev Spaces

Questa esercitazione illustra lo sviluppo di applicazioni multiservizio con Azure Dev Spaces, insieme ad alcuni vantaggi aggiuntivi offerti da Dev Spaces.

## <a name="call-another-container"></a>Chiamare un altro contenitore
In questa sezione si creerà un secondo servizio, `mywebapi`, e si farà in modo che venga chiamato da `webfrontend`. Ogni servizio viene eseguito in contenitori separati. Verrà quindi eseguito il debug in entrambi i contenitori.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Scaricare il codice di esempio per *mywebapi*
Per motivi di tempo, scarichiamo il codice di esempio da un repository GitHub. Passare a https://github.com/Azure/dev-spaces e selezionare **Clone or Download** (Clona o scarica) per scaricare il repository GitHub. Il codice per questa sezione è disponibile in `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Eseguire *mywebapi*
1. Aprire il progetto `mywebapi` in una *finestra di Visual Studio separata*.
1. Selezionare **Azure Dev Spaces** nell'elenco a discesa delle impostazioni di avvio, come in precedenza per il progetto `webfrontend`. Anziché creare un nuovo cluster servizio Azure Kubernetes ora, selezionare lo stesso già creato. Come in precedenza, lasciare lo spazio impostato sul valore predefinito `default` e fare clic su **OK**. Nella finestra Output si nota che Visual Studio inizia a "riscaldare" questo nuovo servizio nello spazio di sviluppo per velocizzare le operazioni quando si avvia il debug.
1. Premere F5 e attendere la compilazione e la distribuzione del servizio. Il servizio è pronto quando la barra di stato di Visual Studio diventa arancione
1. Prendere nota dell'URL dell'endpoint visualizzato nel riquadro **Azure Dev Spaces per il servizio Azure Kubernetes** nella finestra **Output**. Sarà simile a `http://localhost:<portnumber>`. Potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito nello spazio di sviluppo in Azure.
2. Quando `mywebapi` è pronto, aprire il browser all'indirizzo localhost e aggiungere `/api/values` all'URL per richiamare l'API GET predefinita per `ValuesController`. 
3. Se tutte le operazioni hanno avuto esito positivo, dovrebbe venire visualizzata una risposta da parte del servizio `mywebapi` simile a quanto segue.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Creare una richiesta da *webfrontend* a *mywebapi*
Ora scriviamo il codice in `webfrontend` che crea una richiesta a `mywebapi`. Passare alla finestra di Visual Studio con il progetto `webfrontend`. Nel file `HomeController.cs` *sostituire* il codice per il metodo About con il codice seguente:

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

L'esempio di codice precedente inoltra l'intestazione `azds-route-as` dalla richiesta in ingresso alla richiesta in uscita. Più avanti si vedrà come ciò agevoli un'esperienza di sviluppo più produttiva negli [scenari di team](team-development-netcore-visualstudio.md).

### <a name="debug-across-multiple-services"></a>Eseguire il debug tra più servizi
1. A questo punto `mywebapi` dovrebbe ancora essere in esecuzione con il debugger collegato. In caso contrario, premere F5 nel progetto `mywebapi`.
1. Impostare un punto di interruzione nel metodo `Get(int id)` nel file `Controllers/ValuesController.cs` che gestisce richieste GET `api/values/{id}`.
1. Nel punto del progetto `webfrontend` in cui è stato incollato il codice precedente, impostare un punto di interruzione prima che una richiesta GET venga inviata a `mywebapi/api/values`.
1. Premere F5 nel progetto `webfrontend`. Visual Studio apre di nuovo un browser sulla porta localhost appropriata e viene visualizzata l'app Web.
1. Fare clic sul collegamento **About** nella parte superiore della pagina per attivare il punto di interruzione nel progetto `webfrontend`. 
1. Premere F10 per continuare. Il punto di interruzione nel progetto `mywebapi` viene attivato.
1. Premere F5 per continuare e tornare al codice nel progetto `webfrontend`.
1. Se si preme F5 ancora una volta la richiesta viene completata e si torna a una pagina nel browser. Nell'app Web la pagina delle informazioni visualizza un messaggio concatenato dai due servizi: "Salve da webfrontend e Salve da mywebapi."

### <a name="well-done"></a>Ecco fatto!
È ora disponibile un'applicazione multicontenitore in cui ogni contenitore può essere sviluppato e distribuito separatamente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sullo sviluppo in team in Dev Spaces](team-development-netcore-visualstudio.md)
