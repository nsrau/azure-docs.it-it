---
title: Gestire tipi di contenuto - App per la logica di Azure | Documentazione Microsoft
description: 'Procedura: come App per la logica di Azure gestisce i tipi di contenuto in fase di progettazione e di runtime'
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: cd1f08fd-8cde-4afc-86ff-2e5738cc8288
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 3206c076350c2105e92d320ce30ce73448ccd3bd
ms.lasthandoff: 03/10/2017


---
# <a name="handle-content-types-in-logic-apps"></a>Gestire i tipi di contenuto di app per la logica

Molti tipi diversi di contenuto possono attraversare un'app per la logica, tra cui JSON, XML, file flat e dati binari. Sebbene il motore di App per la logica supporti tutti i tipi di contenuto, alcuni vengono riconosciuti in modo nativo dal motore. Altri potrebbero richiedere cast o conversioni in base alle esigenze. Questo articolo descrive come il motore gestisce i differenti tipi di contenuto e come gestire correttamente questi tipi quando è necessario.

## <a name="content-type-header"></a>Intestazione Content-Type

Per semplicità, esaminiamo due valori `Content-Types` che non richiedono alcuna conversione o cast da usare nei tipi di contenuto `application/json` e `text/plain` di un'app per la logica.

## <a name="applicationjson"></a>Application/JSON

Il motore del flusso di lavoro si basa sull'intestazione `Content-Type` delle chiamate HTTP per determinare la gestione appropriata. Qualsiasi richiesta con tipo di contenuto `application/json` viene archiviata e gestita come un oggetto JSON. Per impostazione predefinita, il contenuto JSON può anche essere analizzato senza che sia necessario alcun cast. 

Ad esempio, è possibile analizzare una richiesta con l'intestazione content-type `application/json ` in un flusso di lavoro usando un'espressione come `@body('myAction')['foo'][0]` per ottenere il valore `bar` in questo caso:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

Non è necessario alcun cast aggiuntivo. Se si lavora con dati JSON ma non è stata specificata un'intestazione, è possibile impostare manualmente i dati su JSON usando la funzione `@json()`, ad esempio `@json(triggerBody())['foo']`.

### <a name="schema-and-schema-generator"></a>Schema e generatore di schemi

Il trigger della richiesta consente di immettere uno schema JSON per il payload che si prevede di ricevere. Questo schema consente alla finestra di progettazione di generare token per consentire di usare il contenuto della richiesta. Se non si dispone di uno schema, selezionare `Use sample payload to generate schema` in modo che sia possibile generare uno schema JSON da un payload di esempio.

![Schema](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="parse-json-action"></a>Azione di "analisi JSON"

L'azione `Parse JSON` consente di analizzare il contenuto JSON in token descrittivi per l'uso di app per la logica. Analogamente al trigger di richiesta, questa azione consente di immettere o generare uno schema JSON per il contenuto da analizzare. Questo strumento rende molto più semplice l'uso di dati da Bus di servizio, DocumentDB e così via.

![Analizzare JSON](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>Text/plain

Come per `application/json`, i messaggi HTTP ricevuti con l'intestazione `Content-Type` di `text/plain` vengono archiviati nel formato non elaborato. Inoltre, se questi messaggi sono inclusi in azioni successive senza eseguire il cast, queste richieste passano con l'intestazione `Content-Type`: `text/plain`. Ad esempio, quando si usa un file flat, è possibile che venga visualizzato questo contenuto HTTP come `text/plain`:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

Se nell'azione successiva si invia la richiesta come corpo di un'altra richiesta (`@body('flatfile')`), la richiesta avrà un'intestazione Content-Type `text/plain`. Se si lavora con dati che sono testo normale ma non è stata specificata un'intestazione, è possibile impostare manualmente i dati come testo usando la funzione `@string()`, ad esempio `@string(triggerBody())`.

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml, Application/octet-stream e funzioni del convertitore

Il motore dell'app per la logica mantiene sempre il valore `Content-Type` che è stato ricevuto per la richiesta o risposta HTTP. Se quindi il motore riceve contenuto con il `Content-Type` di `application/octet-stream` e si include questo contenuto in un'azione successiva senza eseguire il cast, la richiesta in uscita ha `Content-Type`: `application/octet-stream`. In questo modo, il motore può garantire che non vengano persi dati durante lo spostamento nel flusso di lavoro. Tuttavia, lo stato dell'azione, ovvero gli input e gli output, viene archiviato in un oggetto JSON mentre attraversa il flusso di lavoro. Per mantenere alcuni tipi di dati, il motore converte il contenuto in una stringa binaria con codifica Base64, con i metadati appropriati per conservare sia `$content` che `$content-type`, che viene automaticamente convertita. 

* `@json()`: esegue il cast dei dati in `application/json`
* `@xml()`: esegue il cast dei dati in `application/xml`
* `@binary()`: esegue il cast dei dati in `application/octet-stream`
* `@string()`: esegue il cast dei dati in `text/plain`
* `@base64()` : converte il contenuto in una stringa Base64
* `@base64toString()`: converte una stringa Base64 codificata in `text/plain`
* `@base64toBinary()`: converte una stringa Base64 codificata in `application/octet-stream`
* `@encodeDataUri()` : codifica una stringa come matrice di byte dataUri
* `@decodeDataUri()` : decodifica un dataUri in una matrice di byte

Ad esempio, se si riceve una richiesta HTTP con `Content-Type`: `application/xml`:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Si potrebbe eseguire il cast e usarlo in un secondo tempo con un elemento simile a `@xml(triggerBody())` o all'interno di una funzione come `@xpath(xml(triggerBody()), '/CustomerName')`.

## <a name="other-content-types"></a>Altri tipi di contenuto

Esistono altri tipi di contenuto che sono supportati e funzionano con le app per la logica, ma potrebbero richiedere il recupero manuale del corpo del messaggio con la decodifica di `$content`. Ad esempio si supponga di attivare una richiesta `application/x-www-url-formencoded` dove `$content` rappresenta il payload codificato come stringa Base64 per mantenere tutti i dati:

```
CustomerName=Frank&Address=123+Avenue
```

Poiché la richiesta non è in testo normale o JSON, la richiesta viene archiviata nell'azione come indicato di seguito:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Poiché al momento non esiste una funzione nativa per dati del modulo, questi dati possono essere usati all'interno di un flusso di lavoro eseguendo manualmente l'accesso ai dati con una funzione come `@string(body('formdataAction'))`. Se la richiesta in uscita deve avere anche l'intestazione content-type `application/x-www-url-formencoded`, è sufficiente aggiungere la richiesta al corpo dell'azione senza cast come `@body('formdataAction')`. Questo metodo tuttavia funziona solo se il corpo è l'unico parametro nell'input `body`. Se si tenta di usare `@body('formdataAction')` in una richiesta `application/json`, verrà visualizzato un errore di runtime perché viene inviato il corpo codificato.


