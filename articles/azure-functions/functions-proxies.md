---
title: Uso di proxy in Funzioni di Azure | Microsoft Docs
description: Informazioni generali sull&quot;uso dei proxy in Funzioni di Azure
services: functions
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/11/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 6a0a81a011d9a1cc1a8a81ba8ef92d90308c534d
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---
# <a name="working-with-azure-functions-proxies-preview"></a>Uso di proxy in Funzioni di Azure (anteprima)

> [!Note] 
> I prozy di Funzioni di Azure sono attualmente in anteprima. Sono gratuite in anteprima, ma si applicano le tariffe standard per le funzioni quando vengono eseguiti i proxy. Per altre informazioni, vedere [Piano tariffario di Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/).

Questo articolo illustra come configurare e usare i proxy in Funzioni di Azure. Questa funzionalità consente di specificare gli endpoint nell'app per le funzioni implementati da un'altra risorsa. È possibile usare questi proxy per suddividere un'API di grandi dimensioni in più app per le funzioni (come in un'architettura di microservizio), pur continuando a presentare una singola superficie API per i client.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## <a name="enable"></a>Abilitazione dei proxy di Funzioni di Azure

I proxy non sono abilitati per impostazione predefinita. È possibile creare i proxy quando la funzionalità è disabilitata, ma non è possibile eseguirli. Nei passaggi successivi verrà illustrato come abilitare i proxy:

1. Accedere al [Portale di Azure] e passare all'app per le funzioni.
2. Selezionare **Impostazioni dell'app per le funzioni**.
3. Attivare **Enable Azure Functions Proxies (preview)** (Abilita i proxy di Funzioni di Azure (anteprima)).

È inoltre possibile ritornare qui per aggiornare il runtime del proxy man mano che le nuove funzioni diventano disponibili.


## <a name="create"></a>Creazione di un proxy

In questa sezione verrà descritto come creare un proxy nel portale delle funzioni.

1. Accedere al [Portale di Azure] e passare all'app per le funzioni.
2. Nel riquadro di spostamento a sinistra selezionare **Nuovo proxy**.
3. Dare un nome al proxy.
4. Configurare l'endpoint esposto in questa app per le funzioni, specificando il **modello di route** e i **metodi HTTP**. Questi parametri si comportano in base alle regole dei [trigger HTTP].
5. Impostare l'**URL di back-end** su un altro endpoint. Potrebbe trattarsi di una funzione in un'altra app per le funzioni oppure di qualsiasi altra API. Il valore non deve essere statico e può fare riferimento alle [impostazioni dell'applicazione] e ai [parametri della richiesta del client originale].
6. Fare clic su Crea.

Il proxy è ora presente come un nuovo endpoint sull'app per le funzioni. Dalla prospettiva del client, è equivalente a un HttpTrigger nelle Funzioni di Azure. È possibile provare il nuovo proxy copiando l'URL del proxy ed eseguendo un test con il proprio client HTTP preferito.








## <a name="modify-requests-responses"></a>Modifica delle richieste e delle risposte

Proxy di Funzioni di Azure consente di modificare le richieste e risposte dal back-end. Queste trasformazioni possono usare le variabili come definito in [Uso di variabili].

### <a name="modify-backend-request"></a>Modifica della richiesta di back-end

Per impostazione predefinita, la richiesta di back-end viene inizializzata come una copia della richiesta originale. Oltre a impostare l'URL di back-end, è anche possibile apportare modifiche ai parametri del metodo HTTP, delle intestazioni e della stringa di query. I valori modificati possono fare riferimento alle [impostazioni dell'applicazione] e ai [parametri della richiesta del client originale].

Attualmente non esiste un'esperienza del portale per la modifica delle richieste di back-end. Vedere [Definizione di oggetto requestOverrides] per informazioni su come sfruttare questa funzionalità da proxies.json.

### <a name="modify-response"></a>Modifica della risposta

Per impostazione predefinita, la risposta del client viene inizializzata come una copia della risposta back-end. È possibile apportare modifiche al codice di stato, al motivo, alle intestazioni e al corpo della risposta. I valori modificati possono fare riferimento alle [impostazioni dell'applicazione], ai [parametri della richiesta del client originale] e ai [paramenti della risposta back-end].

Attualmente non esiste un'esperienza del portale per la modifica delle risposte. Vedere [Definizione di oggetto responseOverrides] per informazioni su come sfruttare questa funzionalità da proxies.json.






## <a name="using-variables"></a>Uso di variabili

La configurazione di un proxy non deve essere statica. È possibile condizionarla per fare in modo che usi le variabili della richiesta originale, la risposta back-end o le impostazioni dell'applicazione.

### <a name="request-parameters"></a>Fare riferimento ai parametri della richiesta

I parametri della richiesta possono essere usati come input per la proprietà URL di back-end o come parte della modifica delle richieste e risposte. Alcuni parametri possono essere associati dal modello di route specificato nella configurazione del proxy di base, mentre altri derivano dalle proprietà della richiesta in ingresso.

#### <a name="route-template-parameters"></a>Parametri del modello di route
È possibile fare riferimento ai parametri usati nel modello di route in base al nome, racchiuso tra parentesi graffe "{}".

Ad esempio, se un proxy ha un modello di route come `/pets/{petId}`, l'URL di back-end può includere il valore `{petId}`, come in `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Se il modello di route termina con un carattere jolly, ad esempio `/api/{*restOfPath}`, il valore `{restOfPath}` sarà una rappresentazione di stringa dei segmenti del percorso rimanente della richiesta in ingresso.

#### <a name="additonal-request-parameters"></a>Parametri della richiesta aggiuntivi
Oltre ai parametri del modello di route, i valori seguenti possono essere usati nei valori di configurazione:

* **{request.method}**: il metodo HTTP usato nella richiesta originale.
* **{request.headers.&lt;HeaderName&gt;}**: un'intestazione che può essere letta dalla richiesta originale. Sostituire "&lt;HeaderName&gt;" con il nome dell'intestazione che si desidera leggere. Se l'intestazione non è inclusa nella richiesta, il valore sarà una stringa vuota.
* **{request.querystring.&lt;ParameterName&gt;}**: un parametro di stringa di query che può essere letto dalla richiesta originale. Sostituire "&lt;ParameterName&gt;" con il nome del parametro che si desidera leggere. Se il parametro non è incluso nella richiesta, il valore sarà una stringa vuota.

### <a name="response-parameters"></a>Riferimento ai parametri di risposta back-end

I parametri di risposta possono essere usati come parte della modifica della risposta al client. I valori seguenti possono essere usati nei valori di configurazione:

* **{backend.response.statusCode}** : il codice di stato HTTP restituito nella risposta back-end.
* **{backend.response.statusReason}** : il motivo HTTP restituito nella risposta back-end.
* **{backend.response.headers.&lt;HeaderName&gt;}**: un'intestazione che può essere letta dalla risposta back-end. Sostituire "&lt;HeaderName&gt;" con il nome dell'intestazione che si desidera leggere. Se l'intestazione non è inclusa nella richiesta, il valore sarà una stringa vuota.

### <a name="use-appsettings"></a>Riferimento alle impostazioni dell'applicazione

È inoltre possibile fare riferimento alle [impostazioni dell'applicazione definite per l'app per le funzioni](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop) racchiudendo il nome dell'impostazione tra i segni di percentuale "%".

Ad esempio, per l'URL di back-end di `https://%ORDER_PROCESSING_HOST%/api/orders`, "%ORDER_PROCESSING_HOST%" verrà sostituito con il valore dell'impostazione ORDER_PROCESSING_HOST.

> [!TIP] 
> Usare le impostazioni dell'applicazione per gli host di back-end quando si dispone di più distribuzioni o ambienti di test. In questo modo, è possibile assicurarsi di comunicare sempre con il back-end corretto per quell'ambiente.





## <a name="advanced-configuration"></a>Configurazione avanzata

I proxy configurati vengono archiviati in un file proxies.json, situato nella radice di una directory dell'app per le funzioni. È possibile modificare manualmente questo file e distribuirlo come parte dell'app quando si usa uno dei [metodi di distribuzione](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) che la funzione supporta. La funzione deve essere [abilitata](#enable) per poter elaborare il file. 

> [!TIP] 
> Se non è stato configurato uno dei metodi di distribuzione, è anche possibile usare il file proxies.json nel portale. Passare all'app per le funzioni e selezionare "Platform feature" (Funzionalità della piattaforma) e "Editor del servizio app". Questo consentirà di visualizzare la struttura dell'intero file dell'app per le funzioni e di apportare modifiche.

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

Ogni proxy ha un nome descrittivo, ad esempio "proxy1" come nell'esempio precedente. L'oggetto di definizione del proxy corrispondente viene definito dalle proprietà seguenti:

* **matchCondition**: obbligatorio, un oggetto che definisce le richieste che attiveranno l'esecuzione di questo proxy. Contiene due proprietà condivise con i [trigger HTTP]:
    * _methods_: è una matrice dei metodi HTTP a cui il proxy risponderà. Se non viene specificata, il proxy risponderà a tutti i metodi HTTP sul percorso.
    * _route_: obbligatorio, definisce il modello di route, controllando a quali URL delle richieste il proxy risponderà. A differenza dei trigger HTTP, non vi è alcun valore predefinito.
* **backendUri**: l'URL della risorsa di back-end a cui la richiesta deve essere trasmessa tramite proxy. Questo valore può fare riferimento alle impostazioni dell'applicazione e ai parametri della richiesta del client originale. Se questa proprietà non è inclusa, Funzioni di Azure risponderà con un HTTP 200 OK.
* **requestOverrides**: un oggetto che definisce le trasformazioni alla richiesta back-end. Vedere [Definizione di oggetto requestOverrides].
* **responseOverrides**: un oggetto che definisce le trasformazioni alla risposta del client. Vedere [Definizione di oggetto responseOverrides].

> [!Note] 
> I proxy di Funzioni di Azure della proprietà di route non rispettano la proprietà routePrefix della configurazione di host di funzioni. Se si desidera includere un prefisso, ad esempio /api, deve essere incluso nella proprietà di route.

### <a name="requestOverrides"></a>Definizione di oggetto requestOverrides

L'oggetto requestOverrides definisce le modifiche apportate alla richiesta quando viene chiamata la risorsa back-end. L'oggetto viene definito dalle proprietà seguenti:

* **backend.request.method**: è il metodo HTTP che verrà usato per chiamare il back-end.
* **backend.request.querystring.&lt;ParameterName&gt;**: un parametro di stringa di query che può essere impostato per la chiamata al back-end. Sostituire "&lt;ParameterName&gt;" con il nome del parametro che si desidera impostare. Se viene generata una stringa vuota, il parametro non verrà incluso nella richiesta back-end.
* **backend.request.headers.&lt;HeaderName&gt;**: un'intestazione che può essere impostata per la chiamata al back-end. Sostituire "&lt;HeaderName&gt;" con il nome dell'intestazione che si desidera impostare. Se viene generata una stringa vuota, l'intestazione non verrà inclusa nella richiesta back-end.

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
                "backend.request.headers.Accept" : "application/xml",
                "backend.request.headers.x-functions-key" : "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>Definizione di oggetto responseOverrides

L'oggetto requestOverrides definisce le modifiche apportate alla risposta passata al client. L'oggetto viene definito dalle proprietà seguenti:

* **response.statusCode**: il codice di stato HTTP da restituire al client.
* **response.statusReason**: il motivo HTTP da restituire al client.
* **response.body** : la rappresentazione di stringa del corpo da restituire al client.
* **response.headers.&lt;HeaderName&gt;**: un'intestazione che può essere impostata per la risposta al client. Sostituire "&lt;HeaderName&gt;" con il nome dell'intestazione che si desidera impostare. Se viene generata una stringa vuota, l'intestazione non verrà inclusa nella risposta.

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
                "response.headers.Content-Type" : "text/plain"
            }
        }
    }
}
```
> [!Note] 
> In questo esempio, il corpo viene impostato direttamente, pertanto non p necessaria lacuna proprietà `backendUri`. Questo è un esempio di come è possibile usare Proxy di Funzioni di Azure per le API di simulazione.

[Portale di Azure]: https://portal.azure.com
[trigger HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
[Modifying the backend request]: #modify-backend-request
[Modifying the response]: #modify-response
[Definizione di oggetto requestOverrides]: #requestOverrides
[Definizione di oggetto responseOverrides]: #responseOverrides
[impostazioni dell'applicazione]: #use-appsettings
[Uso di variabili]: #using-variables
[parametri della richiesta del client originale]: #request-parameters
[paramenti della risposta back-end]: #response-parameters
