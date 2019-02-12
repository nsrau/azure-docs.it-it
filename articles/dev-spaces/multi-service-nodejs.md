---
title: Esecuzione di più servizi dipendenti con Node.js e VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, contenitori
ms.openlocfilehash: f3dbe8c62cb1fcc0585b5abccc51a620ea713543
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55664768"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Sviluppo multiservizio con Azure Dev Spaces

Questa esercitazione illustra lo sviluppo di applicazioni multiservizio con Azure Dev Spaces, insieme ad alcuni vantaggi aggiuntivi offerti da Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Chiamare un servizio in esecuzione in un contenitore separato

In questa sezione si creerà un secondo servizio, `mywebapi`, al quale `webfrontend` assegnerà un nome. Ogni servizio viene eseguito in contenitori separati. Verrà quindi eseguito il debug in entrambi i contenitori.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Aprire il codice di esempio per *mywebapi*
Il codice di esempio `mywebapi` per questa guida dovrebbe già essere disponibile in una cartella denominata `samples` (in caso contrario, passare a https://github.com/Azure/dev-spaces e selezionare **Clone or Download**, Clona o scarica, per scaricare il repository GitHub). Il codice per questa sezione è disponibile in `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Eseguire *mywebapi*
1. Aprire la cartella `mywebapi` in una *finestra di VS Code separata*.
1. Aprire il **riquadro comandi** (usando il menu **Visualizza | Riquadro comandi**) e usare il completamento automatico per digitare e selezionare questo comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Questo comando non deve essere confuso con il comando `azds prep` che configura il progetto per la distribuzione.
1. Premere F5 e attendere la compilazione e la distribuzione del servizio. Il servizio è pronto quando viene visualizzata la barra di debug di VS Code.
1. Prendere nota dell'URL dell'endpoint che sarà simile a http://localhost:\<portnumber\>. **Suggerimento: la barra di stato di VS Code visualizza un URL selezionabile.** Potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito nell'ambiente di sviluppo in Azure. Il motivo dell'indirizzo localhost è che `mywebapi` non ha definito alcun endpoint pubblico ed è accessibile solo dall'interno dell'istanza di Kubernetes. Per praticità e per agevolare l'interazione con il servizio privato nel computer locale, Azure Dev Spaces crea un tunnel SSH temporaneo al contenitore in esecuzione in Azure.
1. Quando `mywebapi` è pronto, aprire il browser all'indirizzo localhost. Dovrebbe venire visualizzata una risposta dal servizio `mywebapi` ("Salve da mywebapi").


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Creare una richiesta da *webfrontend* a *mywebapi*
Ora scriviamo il codice in `webfrontend` che crea una richiesta a `mywebapi`.
1. Passare alla finestra di VS Code per `webfrontend`.
1. Aggiungere queste righe di codice nella parte superiore di `server.js`:
    ```javascript
    var request = require('request');
    ```

3. *Sostituire* il codice per il gestore GET `/api`. Quando si gestisce una richiesta, questa a sua volta effettua una chiamata a `mywebapi`, quindi restituisce i risultati da entrambi i servizi.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
 4. *Rimuovere* la riga `server.close()` alla fine di `server.js`

L'esempio di codice precedente inoltra l'intestazione `azds-route-as` dalla richiesta in ingresso alla richiesta in uscita. Più avanti si apprenderà come ciò aiuti i team nello sviluppo collaborativo.

### <a name="debug-across-multiple-services"></a>Eseguire il debug tra più servizi
1. A questo punto `mywebapi` dovrebbe ancora essere in esecuzione con il debugger collegato. In caso contrario, premere F5 nel progetto `mywebapi`.
1. Impostare un punto di interruzione nel gestore GET `/` predefinito.
1. Nel progetto `webfrontend`, impostare un punto di interruzione prima che venga inviata una richiesta GET a `http://mywebapi`.
1. Premere F5 nel progetto `webfrontend`.
1. Aprire l'app Web e scorrere il codice in entrambi i servizi. Nell'app Web dovrebbe essere visualizzato un messaggio concatenato dai due servizi: "Salve da webfrontend e Salve da mywebapi."

### <a name="automatic-tracing-for-http-messages"></a>Traccia automatica per i messaggi HTTP
Come si sarà notato, nonostante *webfrontend* non contenga codice speciale per stampare la chiamata HTTP effettuata a *mywebapi*, nella finestra di output vengono visualizzati messaggi di traccia HTTP:
```
// The request from your browser
webfrontend.<id>.<region>.aksapp.io --hyh-> webfrontend:
   GET /api?_=1544485357627 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --1b1-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-1b1-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
webfrontend.<id>.<region>.aksapp.io <-hyh-- webfrontend:
   HTTP/1.1 200 OK
   Hello from webfrontend and Hello from mywebapi
```
Questo è uno dei vantaggi collaterali offerti dalla strumentazione di Dev Spaces. Vengono inseriti componenti che monitorano l'esecuzione delle richieste HTTP nel sistema per facilitare il monitoraggio di chiamate multiservizio complesse durante lo sviluppo.

### <a name="well-done"></a>Ecco fatto!
È ora disponibile un'applicazione multicontenitore in cui ogni contenitore può essere sviluppato e distribuito separatamente.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sullo sviluppo in team in Dev Spaces](team-development-nodejs.md)