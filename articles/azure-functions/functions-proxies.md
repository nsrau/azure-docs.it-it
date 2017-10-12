---
title: Usare i proxy in Funzioni di Azure | Documentazione Microsoft
description: Informazioni generali sull'uso dei proxy in Funzioni di Azure
services: functions
documentationcenter: 
author: mattchenderson
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/11/2017
ms.author: mahender
ms.openlocfilehash: 63119978c59186a71e7e30438e8e37c0b2e1b454
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="work-with-azure-functions-proxies-preview"></a>Usare i proxy di Funzioni di Azure (anteprima)

> [!NOTE] 
> I prozy di Funzioni di Azure sono attualmente in anteprima. Sono gratuite in anteprima, ma si applicano le tariffe standard per le funzioni quando vengono eseguiti i proxy. Per altre informazioni, vedere [Prezzi di Funzioni](https://azure.microsoft.com/pricing/details/functions/).

Questo articolo illustra come configurare e usare i proxy in Funzioni di Azure. Questa funzionalità consente di specificare gli endpoint nell'app per le funzioni implementati da un'altra risorsa. È possibile usare questi proxy per suddividere un'API di grandi dimensioni in più app per le funzioni (come in un'architettura di microservizio), pur continuando a presentare una singola superficie API per i client.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## <a name="enable"></a>Abilitare i proxy di Funzioni di Azure

I proxy non sono abilitati per impostazione predefinita. È possibile creare i proxy quando la funzionalità è disabilitata, ma non è possibile eseguirli. Per abilitare i proxy, seguire questa procedura:

1. Aprire il [Portale di Azure] e passare all'app per le funzioni.
2. Selezionare **Impostazioni dell'app per le funzioni**.
3. Impostare **Abilita Proxy di Funzioni di Azure (anteprima)** su **On**.

È inoltre possibile ritornare qui per aggiornare il runtime del proxy man mano che le nuove funzioni diventano disponibili.


## <a name="create"></a>Creare un proxy

In questa sezione viene descritto come creare un proxy nel portale Funzioni.

1. Aprire il [Portale di Azure] e passare all'app per le funzioni.
2. Nel riquadro sinistro selezionare **Nuovo proxy**.
3. Dare un nome al proxy.
4. Configurare l'endpoint esposto in questa app per le funzioni, specificando il **Modello di route** e i **Metodi HTTP**. Questi parametri si comportano in base alle regole dei [trigger HTTP].
5. Impostare l'**URL di back-end** su un altro endpoint. Questo endpoint potrebbe essere una funzione in un'altra app per le funzioni oppure di qualsiasi altra API. Il valore non deve essere statico e può fare riferimento alle [impostazioni dell'applicazione] e ai [parametri della richiesta del client originale].
6. Fare clic su **Crea**.

Il proxy è ora presente come un nuovo endpoint sull'app per le funzioni. Dalla prospettiva del client, è equivalente a un HttpTrigger nelle Funzioni di Azure. È possibile provare il nuovo proxy copiando l'URL del proxy ed eseguendo un test con il proprio client HTTP preferito.

## <a name="modify-requests-responses"></a>Modificare richieste e risposte

I proxy di Funzioni di Azure consentono di modificare le richieste al back-end e le risposte dal back-end. Queste trasformazioni possono usare le variabili come definito in [Usare le variabili].

### <a name="modify-backend-request"></a>Modificare la richiesta al back-end

Per impostazione predefinita, la richiesta al back-end viene inizializzata come una copia della richiesta originale. Oltre a impostare l'URL di back-end, è possibile apportare modifiche ai parametri del metodo HTTP, delle intestazioni e della stringa di query. I valori modificati possono fare riferimento alle [impostazioni dell'applicazione] e ai [parametri della richiesta del client originale].

Attualmente non esiste un'esperienza del portale per la modifica delle richieste al back-end. Per informazioni su come applicare questa funzionalità da proxies.json, vedere [Definire un oggetto requestOverrides].

### <a name="modify-response"></a>Modificare la risposta

Per impostazione predefinita, la risposta del client viene inizializzata come una copia della risposta back-end. È possibile apportare modifiche al codice di stato, al motivo, alle intestazioni e al corpo della risposta. I valori modificati possono fare riferimento alle [impostazioni dell'applicazione], ai [parametri della richiesta del client originale] e ai [paramenti della risposta back-end].

Attualmente non esiste un'esperienza del portale per la modifica delle risposte del back-end. Per informazioni su come applicare questa funzionalità da proxies.json, vedere [Definire un oggetto responseOverrides].

## <a name="using-variables"></a>Usare le variabili

La configurazione di un proxy non deve essere statica. È possibile condizionarla per fare in modo che usi le variabili della richiesta originale, la risposta back-end o le impostazioni dell'applicazione.

### <a name="request-parameters"></a>Parametri di riferimento della richiesta

I parametri della richiesta possono essere usati come input per la proprietà URL di back-end o come parte della modifica di richieste e risposte. Alcuni parametri possono essere associati dal modello di route specificato nella configurazione del proxy di base, mentre altri derivano dalle proprietà della richiesta in ingresso.

#### <a name="route-template-parameters"></a>Parametri del modello di route
È possibile fare riferimento ai parametri usati nel modello di route in base al nome. I nomi dei parametri sono racchiusi tra parentesi graffe ({}).

Ad esempio, se un proxy ha un modello di route come `/pets/{petId}`, l'URL di back-end può includere il valore `{petId}`, come in `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Se il modello di route termina con un carattere jolly, ad esempio `/api/{*restOfPath}`, il valore `{restOfPath}` è una rappresentazione di stringa dei segmenti del percorso rimanente della richiesta in ingresso.

#### <a name="additional-request-parameters"></a>Parametri aggiuntivi della richiesta
Oltre ai parametri del modello di route, i valori seguenti possono essere usati nei valori di configurazione:

* **{request.method}**: il metodo HTTP usato nella richiesta originale.
* **{request.headers.\<HeaderName\>}**: un'intestazione che può essere letta dalla richiesta originale. Sostituire *\<HeaderName\>* con il nome dell'intestazione che si desidera leggere. Se l'intestazione non è inclusa nella richiesta, il valore sarà una stringa vuota.
* **{request.querystring.\<ParameterName\>}**: un parametro di stringa di query che può essere letto dalla richiesta originale. Sostituire *\<ParameterName\>* con il nome del parametro che si desidera leggere. Se il parametro non è incluso nella richiesta, il valore sarà una stringa vuota.

### <a name="response-parameters"></a>Parametri di riferimento della risposta dal back-end

I parametri di risposta possono essere usati come parte della modifica della risposta al client. I valori seguenti possono essere usati nei valori di configurazione:

* **{backend.response.statusCode}**: il codice di stato HTTP restituito nella risposta dal back-end.
* **{backend.response.statusReason}**: la frase per il motivo HTTP restituita nella risposta dal back-end.
* **{backend.response.headers.\<HeaderName\>}**: un'intestazione che può essere letta dalla risposta dal back-end. Sostituire *\<HeaderName\>* con il nome dell'intestazione che si desidera leggere. Se l'intestazione non è inclusa nella richiesta, il valore sarà una stringa vuota.

### <a name="use-appsettings"></a>Impostazioni di riferimento dell'applicazione

È anche possibile fare riferimento alle [impostazioni dell'applicazione definite per l'app per le funzioni](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop) racchiudendo il nome dell'impostazione tra i segni di percentuale (%).

Ad esempio, per l'URL di back-end di *https://%ORDER_PROCESSING_HOST%/api/orders*, "%ORDER_PROCESSING_HOST%" verrà sostituito con il valore dell'impostazione ORDER_PROCESSING_HOST.

> [!TIP] 
> Usare le impostazioni dell'applicazione per gli host di back-end quando si dispone di più distribuzioni o ambienti di test. In questo modo, è possibile assicurarsi di comunicare sempre con il back-end corretto per quell'ambiente.

## <a name="advanced-configuration"></a>Configurazione avanzata

I proxy configurati vengono archiviati in un file proxies.json, situato nella radice di una directory dell'app per le funzioni. È possibile modificare manualmente questo file e distribuirlo come parte dell'app quando si usa uno dei [metodi di distribuzione](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) supportati da Funzioni. La funzione deve essere [abilitata](#enable) per poter elaborare il file. 

> [!TIP] 
> Se non è stato configurato uno dei metodi di distribuzione, è anche possibile usare il file proxies.json nel portale. Passare all'app per le funzioni e selezionare **Funzionalità della piattaforma** ed **Editor del servizio app**. Questo consentirà di visualizzare la struttura dell'intero file dell'app per le funzioni e di apportare modifiche.

Il file proxies.JSON è definito da un oggetto proxy, composto da proxy denominati e dalle relative definizioni. È facoltativamente possibile fare riferimento a uno [schema JSON](http://json.schemastore.org/proxies) per il completamento del codice se l'editor lo supporta. Un esempio di file apparirà come segue:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Ogni proxy ha un nome descrittivo, come *proxy1* nell'esempio precedente. L'oggetto di definizione del proxy corrispondente viene definito dalle proprietà seguenti:

* **matchCondition**: obbligatorio, un oggetto che definisce le richieste che attivano l'esecuzione di questo proxy. Contiene due proprietà condivise con i [trigger HTTP]:
    * _methods_: una matrice di metodi HTTP a cui il proxy risponde. Se non viene specificata, il proxy risponderà a tutti i metodi HTTP nel route.
    * _route_: obbligatorio, definisce il modello di route, controllando a quali URL delle richieste il proxy risponde. A differenza dei trigger HTTP, non vi è alcun valore predefinito.
* **backendUri**: l'URL della risorsa di back-end a cui la richiesta deve essere trasmessa tramite proxy. Questo valore può fare riferimento alle impostazioni dell'applicazione e ai parametri della richiesta del client originale. Se questa proprietà non è inclusa, Funzioni di Azure risponde con un HTTP 200 OK.
* **requestOverrides**: un oggetto che definisce le trasformazioni alla richiesta al back-end. Vedere [Definire un oggetto requestOverrides].
* **responseOverrides**: un oggetto che definisce le trasformazioni alla risposta del client. Vedere [Definire un oggetto responseOverrides].

> [!NOTE] 
> I proxy di Funzioni di Azure della proprietà di route non rispettano la proprietà routePrefix della configurazione di host di Funzioni. Se si desidera includere un prefisso, ad esempio /api, deve essere incluso nella proprietà di route.

### <a name="requestOverrides"></a>Definire un oggetto requestOverrides

L'oggetto requestOverrides definisce le modifiche apportate alla richiesta quando viene chiamata la risorsa back-end. L'oggetto viene definito dalle proprietà seguenti:

* **backend.request.method**: il metodo HTTP usato per chiamare il back-end.
* **backend.request.querystring.\<ParameterName\>**: un parametro di stringa di query che può essere impostato per la chiamata al back-end. Sostituire *\<ParameterName\>* con il nome del parametro che si desidera impostare. Se viene generata una stringa vuota, il parametro non viene incluso nella richiesta al back-end.
* **backend.request.headers.\<HeaderName\>**: un'intestazione che può essere impostata per la chiamata al back-end. Sostituire *\<HeaderName\>* con il nome dell'intestazione che si desidera impostare. Se viene fornita una stringa vuota, il parametro non viene incluso nella richiesta al back-end.

I valori possono fare riferimento alle impostazioni dell'applicazione e ai parametri della richiesta del client originale.

Un esempio di configurazione apparirà come segue:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>Definire un oggetto responseOverrides

L'oggetto responseOverrides definisce le modifiche apportate alla risposta passata al client. L'oggetto viene definito dalle proprietà seguenti:

* **response.statusCode**: il codice di stato HTTP da restituire al client.
* **response.statusReason**: la frase del motivo HTTP da restituire al client.
* **response.body**: la rappresentazione di stringa del corpo da restituire al client.
* **response.headers.\<HeaderName\>**: un'intestazione che può essere impostata per la risposta al client. Sostituire *\<HeaderName\>* con il nome dell'intestazione che si desidera impostare. Se viene fornita una stringa vuota, l'intestazione non viene inclusa nella richiesta al back-end.

I valori possono fare riferimento alle impostazioni dell'applicazione, ai parametri della richiesta del client originale e ai paramenti della risposta back-end.

Un esempio di configurazione apparirà come segue:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> In questo esempio, il corpo viene impostato direttamente, pertanto non p necessaria lacuna proprietà `backendUri`. L'esempio illustra come usare i proxy di Funzioni di Azure per le API di simulazione.

[Portale di Azure]: https://portal.azure.com
[trigger HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definire un oggetto requestOverrides]: #requestOverrides
[Definire un oggetto responseOverrides]: #responseOverrides
[impostazioni dell'applicazione]: #use-appsettings
[Usare le variabili]: #using-variables
[parametri della richiesta del client originale]: #request-parameters
[paramenti della risposta back-end]: #response-parameters
