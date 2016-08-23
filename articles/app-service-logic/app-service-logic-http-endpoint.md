<properties
   pageTitle="App per la logica come endpoint che è possibile chiamare"
   description="Come creare e configurare gli endpoint del trigger e usarli in un'app per la logica nel servizio app di Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/10/2016"
   ms.author="jehollan"/>  


# App per la logica come endpoint che è possibile chiamare

Le app per la logica possono esporre un endpoint HTTP sincrono come trigger. È anche possibile usare il modello di endpoint che è possibile chiamare per richiamare app per la logica come un flusso di lavoro nidificato tramite l'azione "workflow" in un'app per la logica.

Sono disponibili tre tipi di trigger in grado di ricevere richieste:

* Richiesta
* ApiConnectionWebhook
* HttpWebhook

Per il resto dell'articolo verrà usata la **request** come esempio, ma tutti i principi si applicano in modo identico agli altri 2 tipi di trigger.

## Aggiunta di un trigger alla definizione
Il primo passaggio consiste nell'aggiungere un trigger per la definizione dell'app per la logica che possa ricevere le richieste in ingresso. È possibile eseguire la ricerca nella finestra di progettazione della "richiesta HTTP" per aggiungere la scheda di attivazione. È possibile definire uno schema JSON per il corpo della richiesta e consentire alla finestra di progettazione di generare i token per l'analisi e il passaggio dei dati dal trigger manual al flusso di lavoro. È consigliabile usare uno strumento come [jsonschema.net](http://jsonschema.net) per generare uno schema JSON da un payload del corpo di esempio.

![Scheda del trigger di richiesta][2]  

Dopo aver salvato la definizione dell'app per la logica, verrà generato un URL di callback simile al seguente:
 
``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?...
```

Questo URL contiene una chiave di firma di accesso condiviso nei parametri di query usati per l'autenticazione.

È inoltre possibile ottenere questo endpoint nel portale di Azure:

![][1]  

Oppure chiamando:

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

## Chiamata dell'endpoint del trigger dell'app per la logica

Dopo aver creato l'endpoint del trigger, è possibile attivarlo tramite un `POST` nell'URL completo. Nel corpo è possibile includere intestazioni aggiuntive e qualsiasi altro contenuto.

Se content-type è `application/json`, si potrà fare riferimento alle proprietà all'interno della richiesta. Altrimenti si dovrà considerare come una singola unità binaria che può essere passata ad altre API, ma alla quale non si può fare riferimento all'interno del flusso di lavoro senza convertire il contenuto. Ad esempio, se si passa il contenuto `application/xml` è possibile usare `@xpath()` per eseguire un'estrazione xpath o `@json()` per convertire XML in JSON. [Qui sono disponibili](app-service-logic-content-type.md) informazioni aggiuntive sull'uso dei tipi di contenuto.

È possibile anche specificare uno schema JSON nella definizione. Questo consente alla finestra di progettazione di generare i token che sarà possibile passare alla procedura. Il codice seguente, ad esempio, rende disponibili i token `title` e `name` nella finestra di progettazione:

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## Riferimento al contenuto della richiesta in ingresso

La funzione `@triggerOutputs()` eseguirà l'output del contenuto della richiesta in ingresso. Ad esempio, sarà simile a quanto segue:

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

È possibile usare il collegamento `@triggerBody()` per accedere in modo specifico alla proprietà `body`.

## Risposta alla richiesta

Per alcune richieste che avviano un'app per la logica, è possibile rispondere inviando contenuto al chiamante. È disponibile un nuovo tipo di azione, **response**, che può essere usato per costruire il codice di stato, il corpo e le intestazioni della risposta. Si noti che se non è presente **response**, l'endpoint dell'app per la logica risponderà *immediatamente* con **202 - Accettato**.

![Azione di risposta HTTP][3]  

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

Le risposte includono gli elementi seguenti:

| Proprietà | Descrizione |
| -------- | ----------- |
| statusCode | Codice di stato HTTP per rispondere alla richiesta in ingresso. Può essere qualsiasi codice di stato valido che inizia con 2xx, 4xx o 5xx. I codici di stato 3xx non sono consentiti. | 
| body | Oggetto body che può essere una stringa, un oggetto JSON o anche contenuto binario a cui si fa riferimento da un passaggio precedente. | 
| headers | Nella risposta è possibile includere un numero illimitato di intestazioni. | 

Tutti i passaggi nell'app per la logica necessari per la risposta devono essere completati entro *60 secondi* affinché la richiesta originale riceva la risposta **a meno che il flusso di lavoro non venga richiamato come un'app per la logica nidificata**. Se entro 60 secondi non si ottiene alcuna azione di risposta, si verificherà il timeout della richiesta in ingresso, che riceverà una risposta HTTP **408 Client timeout** (408 Timeout del client). Per le app per la logica nidificate, l'app per la logica padre rimarrà in attesa di una risposta fino al completamento, indipendentemente dalla quantità di tempo impiegato.

## Configurazione dell'endpoint avanzata

Le app per la logica dispongono di supporto integrato per l'endpoint di accesso diretto e usano sempre il metodo `POST` per avviare l'esecuzione. L'app per le API **Listener HTTP** prima supportava anche la modifica dei segmenti dell'URL e il metodo HTTP. È anche possibile configurare maggiore sicurezza o un altro dominio personalizzato aggiungendolo all'host dell'app per le API, ovvero l'app Web che ha ospitato l'app per le API.

Questa funzionalità è disponibile tramite **Gestione API**:
* [Modificare il metodo della richiesta](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Modificare i segmenti dell'URL della richiesta](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configurare i domini di Gestione API nella scheda **Configura** del portale di Azure classico
* Impostare i criteri per verificare l'autenticazione di base (**collegamento necessario**)

## Riepilogo della migrazione da 2014-12-01-preview

| 2014-12-01-preview | 2016-06-01 |
|---------------------|--------------------|
| Fare clic sull'app per le API **Listener HTTP** | Fare clic sul **trigger manual**. Non è necessaria un'app per le API. |
| Impostazione di Listener HTTP "*Send response automatically *" (Invia risposta automaticamente) | Includere o meno un'azione **response** nella definizione del flusso di lavoro |
| Configurare l'autenticazione di base o OAuth | tramite Gestione API |
| Configurare il metodo HTTP | tramite Gestione API |
| Configurare il percorso relativo | tramite Gestione API |
| Fare riferimento all'oggetto body in ingresso tramite `@triggerOutputs().body.Content` | Riferimento tramite `@triggerOutputs().body` |
| Azione **Send HTTP response** (Invia risposta HTTP) nel Listener HTTP | Fare clic su **Respond to HTTP request** (Rispondi alla richiesta HTTP). Non è necessaria un'app per le API.


[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png

<!---HONumber=AcomDC_0810_2016-->