---
title: Team di sviluppo con Azure Dev Spaces usando Visual Studio Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: iainfoulds
ms.author: iainfou
ms.date: 07/09/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenitori
ms.openlocfilehash: 454a71f8b1d1afa12ff58dda6e871d10e724c681
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978253"
---
# <a name="team-development-with-azure-dev-spaces"></a>Team di sviluppo con Azure Dev Spaces

In questa esercitazione si apprenderà come usare più spazi di sviluppo che vengono eseguiti contemporaneamente in diversi ambienti di sviluppo, tenendo separato il lavoro in spazi di sviluppo separati nello stesso cluster.

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
1. Aprire l'app Web e scorrere il codice in entrambi i servizi. Nell'app Web dovrebbe essere visualizzato un messaggio concatenato dai due servizi: "Salve da webfrontend e Salve da mywebapi".

Ecco fatto! È ora disponibile un'applicazione multicontenitore in cui ogni contenitore può essere sviluppato e distribuito separatamente.

## <a name="learn-about-team-development"></a>Informazioni sui team di sviluppo

[!INCLUDE [](../../includes/team-development-1.md)]

Provalo:
1. Passare alla finestra di VS Code per `mywebapi` e apportare una modifica al codice per il gestore GET `/` predefinito, ad esempio:

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE [](../../includes/team-development-2.md)]

### <a name="well-done"></a>Ecco fatto!
La Guida introduttiva è stata completata. Si è appreso come:

> [!div class="checklist"]
> * Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
> * Sviluppare codice in modo iterativo nei contenitori.
> * Sviluppare in modo indipendente due servizi distinti e utilizzare l'individuazione del servizio DNS di Kubernetes per effettuare una chiamata a un altro servizio.
> * Sviluppare e testare il codice in modo produttivo in un ambiente di team.

Ora che si è preso visione di Azure Dev Spaces, [condividere lo spazio di sviluppo con un membro del team](how-to/share-dev-spaces.md) per dimostrare come sia facile collaborare.

## <a name="clean-up"></a>Eseguire la pulizia
Per eliminare completamente un'istanza di Azure Dev Spaces in un cluster, inclusi tutti gli spazi di sviluppo e i servizi in esecuzione al suo interno, usare il comando `az aks remove-dev-spaces`. Tenere presente che questa operazione è irreversibile. È possibile aggiungere di nuovo il supporto per Azure Dev Spaces nel cluster, ma sarà come iniziare da zero. I vecchi spazi e servizi non verranno ripristinati.

L'esempio seguente elenca i controller Azure Dev Spaces nella sottoscrizione attiva e quindi elimina il controller Azure Dev Spaces associato al cluster AKS "myaks" nel gruppo di risorse "myaks-rg".

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```




