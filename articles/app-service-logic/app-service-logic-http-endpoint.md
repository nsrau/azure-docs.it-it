<properties
   pageTitle="App per la logica come endpoint che è possibile chiamare"
   description="Come creare e configurare il listener HTTP e usarlo in un'app per la logica nel servizio app di Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/17/2016"
   ms.author="stepsic"/>


# App per la logica come endpoint che è possibile chiamare

La versione precedente dello schema di app per la logica (*2014-12-01-preview*) richiede un'app per le API denominata **Listener HTTP** per esporre un endpoint HTTP che può essere chiamato in modo sincrono. Con lo schema più recente (*2015-08-01-preview*) le app per la logica possono esporre un endpoint HTTP sincrono.

## Aggiunta di un trigger alla definizione
Il primo passaggio consiste nell'aggiungere un trigger per la definizione dell'app per la logica che possa ricevere le richieste in ingresso. Sono disponibili 3 tipi di trigger che possono ricevere richieste: * manual * apiConnectionWebhook * httpWebhook

Per il resto dell'articolo si userà **manual** come esempio, ma tutte le entità si applicano in modo identico agli altri 2 tipi di trigger. Dopo l'aggiunta di questo trigger, la definizione del flusso di lavoro sarà simile alla seguente:

```
{
    "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "triggers" : {
        "myendpointtrigger" : {
            "type" : "manual"
        }
    }
}
```

Questo comando crea un endpoint che si può chiamare con un URL simile al seguente:
 
```
https://prod-03.brazilsouth.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?...
```

È possibile ottenere questo endpoint nell'interfaccia utente oppure chiamando:

```
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

## Chiamata dell'endpoint del trigger dell'app per la logica
Dopo aver creato l'endpoint del trigger, è possibile salvarlo nel sistema back-end e chiamarlo tramite `POST` nell'URL completo. Nel corpo è possibile includere parametri di query, intestazioni e qualsiasi contenuto aggiuntivo.

Se content-type è `application/json`, si potrà fare riferimento alle proprietà all'interno della richiesta. In caso contrario, verrà considerata come una singola unità binaria che può essere passata ad altre API, ma alla quale non si può fare riferimento dall'interno.

## Riferimento al contenuto della richiesta in ingresso
La funzione `@triggerOutputs()` restituirà il contenuto della richiesta in ingresso. Ad esempio, sarà simile a quanto segue:

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

Si tratta di una leggera differenza rispetto alla versione *2014-12-01-preview* in cui si accede al corpo di un listener HTTP tramite una funzione analoga alla seguente: `@triggerOutputs().body.Content`.

## Risposta alla richiesta
Per alcune richieste che avviano un'app per la logica, è possibile rispondere inviando contenuto al chiamante. È disponibile un nuovo tipo di azione chiamato **response** che può essere usata per costruire il codice di stato, il corpo e le intestazioni della risposta. Si noti che se non è presente una forma di **response**, l'endpoint dell'app per la logica risponderà *immediatamente* con **202 Accepted**, l'equivalente di *Send response automatically*, nel listener HTTP.

```
"myresponse" : {
    "type" : "response",
    "inputs" : {
        "statusCode" : 200,
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        }
    },
    "conditions" : []
}
```

Le risposte includono gli elementi seguenti:

| proprietà | Descrizione |
| -------- | ----------- |
| statusCode | Codice di stato HTTP per rispondere alla richiesta in ingresso. Può essere qualsiasi codice di stato valido che inizia con 2xx, 4xx o 5xx. I codici di stato 3xx non sono consentiti. | 
| body | Oggetto body che può essere una stringa, un oggetto JSON o anche contenuto binario a cui si fa riferimento da un passaggio precedente. | 
| headers | Nella risposta è possibile includere un numero illimitato di intestazioni. | 

Tutti i passaggi nell'app per la logica richiesti per la risposta devono essere completati entro *60 secondi* per consentire alla richiesta originale di ricevere la risposta. Se entro 60 secondi non viene raggiunta alcuna azione di risposta, si verificherà il timeout della richiesta in ingresso che riceverà una risposta HTTP **408 Client timeout**.

## Configurazione dell'endpoint avanzata
Le app per la logica includono il supporto per l'endpoint di accesso diretto e usano sempre il metodo `POST` per l'avvio dell'esecuzione. L'app per le API **Listerner HTTP** supportava in precedenza la modifica dei segmenti dell'URL e il metodo HTTP. È anche possibile configurare maggiore sicurezza o un altro dominio personalizzato aggiungendolo all'host dell'app per le API, ovvero l'app Web che ha ospitato l'app per le API.

Questa funzionalità è disponibile tramite **Gestione API**: * [Modificare il metodo della richiesta](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) * [Modificare i segmenti dell'URL della richiesta](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL) * Configurare i domini di gestione API nella scheda **Configura** nel portale di Azure classico * Configurare criteri per controllare l'autenticazione di base (**è necessario il collegamento**)

## Riepilogo della migrazione da 2014-12-01-preview

| 2014-12-01-preview | 2015-08-01-preview |
|---------------------|--------------------|
| Fare clic sull'app per le API **Listener HTTP** | Fare clic su **Manual trigger**, non è necessaria un'app per le API |
| Impostazione del listener HTTP "*Send response automatically*" | Includere o meno un'azione **response** nella definizione del flusso di lavoro |
| Configurare l'autenticazione di base o OAuth | tramite Gestione API |
| Configurare il metodo HTTP | tramite Gestione API |
| Configurare il percorso relativo | tramite Gestione API |
| Fare riferimento all'oggetto Body in ingresso tramite `@triggerOutputs().body.Content` | Riferimento tramite `@triggerOutputs().body` |
| Azione **Send HTTP response** nel listener HTTP | Fare clic su **Respond to HTTP request** (non è necessaria un'app per le API)

<!---HONumber=AcomDC_0224_2016-->