---
title: Gestire i tipi di contenuti
description: Informazioni su come App per la logica gestisce i tipi di contenuto in fase di progettazione e in fase di esecuzione
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: 75d9285c4a838c2057c0f23841c3a2f465789c7c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791526"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Gestire tipi di contenuto in App per la logica di Azure

Molti tipi diversi di contenuto possono attraversare un'app per la logica, tra cui file JSON, XML, flat e dati binari. Sebbene App per la logica supporti tutti i tipi di contenuto, alcune app per la logica dispongono del supporto nativo e non richiedono di eseguire il cast o la conversione nell'app stessa. Altri tipi potrebbero richiedere cast o conversioni in base alle esigenze. Questo articolo descrive il modo in cui App per la logica gestisce i tipi di contenuto e come eseguire correttamente il cast o la conversione di questi tipi quando necessario.

Per determinare il modo più appropriato per gestire i tipi di contenuto, App per la logica si basa sul valore dell'intestazione `Content-Type` nelle chiamate HTTP, ad esempio:

* [application/json](#application-json) (tipo nativo)
* [text/plain](#text-plain) (tipo nativo)
* [application/xml e application/octet-stream](#application-xml-octet-stream)
* [Altri tipi di contenuto](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

App per la logica archivia e gestisce tutte le richieste con il tipo di contenuto *application/json* come oggetti JavaScript Notation (JSON). Per impostazione predefinita, è possibile analizzare il contenuto JSON senza cast. Per analizzare una richiesta la cui intestazione ha il tipo di contenuto "application/json", è possibile usare un'espressione. Questo esempio restituisce il valore `dog` dalla matrice `animal-type` senza eseguire il cast: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Se si lavora con dati JSON che non specificano un'intestazione, è possibile eseguire manualmente il cast di quei dati in formato JSON usando la [funzione json()](../logic-apps/workflow-definition-language-functions-reference.md#json), ad esempio: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Creare token per le proprietà JSON

App per la logica offre la possibilità di generare token descrittivi che rappresentano le proprietà nel contenuto JSON, in modo che sia possibile fare riferimento alle proprietà e usarle più facilmente nel flusso di lavoro dell'app per la logica.

* **Trigger di richiesta**

  Quando si usa questo trigger nella finestra di progettazione di App per la logica, è possibile fornire uno schema JSON che descrive il payload che si prevede di ricevere. 
  La finestra di progettazione consente di analizzare il contenuto JSON con questo schema e genera i token descrittivi che rappresentano le proprietà presenti nel contenuto JSON. 
  È possibile quindi fare riferimento a tali proprietà e usarle facilmente nel flusso di lavoro dell'app per la logica. 
  
  Se non si dispone di uno schema, è possibile generarlo. 
  
  1. Nel trigger di richiesta selezionare **Usare il payload di esempio per generare lo schema**.  
  
  2. In **Immettere o incollare un payload JSON di esempio** specificare un payload di esempio e quindi scegliere **Fine**. ad esempio: 

     ![Fornire il payload JSON di esempio](./media/logic-apps-content-type/request-trigger.png)

     Lo schema generato compare nel trigger.

     ![Fornire il payload JSON di esempio](./media/logic-apps-content-type/generated-schema.png)

     Ecco la definizione sottostante per il trigger di richiesta nell'editor della visualizzazione codice:

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. Nella richiesta assicurarsi di includere un'intestazione `Content-Type` e impostare il valore dell'intestazione su `application/json`.

* **Azione di "analisi JSON"**

  Quando si usa questa azione nella finestra di progettazione di App per la logica, è possibile analizzare l'output JSON e generare token descrittivi che rappresentano le proprietà presenti nel contenuto JSON. 
  È possibile quindi fare riferimento a tali proprietà e usarle facilmente nel flusso di lavoro dell'app per la logica. Analogamente al trigger di richiesta, è possibile fornire o generare uno schema JSON che descrive il contenuto JSON da analizzare. 
  In questo modo è possibile usare più facilmente i dati dal bus di servizio di Azure, da Azure Cosmos DB e così via.

  ![Analizzare JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>text/plain

Quando l'app per la logica riceve messaggi HTTP in cui l'intestazione `Content-Type` è impostata su `text/plain`, l'app per la logica archivia i messaggi in formato non elaborato. Se si includono questi messaggi in azioni successive senza eseguire il cast, le richieste escono con l'intestazione `Content-Type` impostata su `text/plain`. 

Ad esempio, quando si lavora con un file flat, si potrebbe ottenere una richiesta HTTP con l'intestazione `Content-Type` impostata sul tipo di contenuto `text/plain`:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Se poi si invia la richiesta in un'azione successiva come corpo per un'altra richiesta, ad esempio `@body('flatfile')`, anche la seconda richiesta ha l'intestazione `Content-Type` impostata su `text/plain`. Se si lavora con i dati in testo normale, ma non è stata specificata un'intestazione, è possibile eseguire manualmente il cast di quei dati in testo usando la [funzione string ()](../logic-apps/workflow-definition-language-functions-reference.md#string), ad esempio con questa espressione: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>application/xml e application/octet-stream

App per la logica mantiene sempre il `Content-Type` in una richiesta o risposta HTTP ricevuta. Se l'app per la logica riceve contenuto con `Content-Type` impostato su `application/octet-stream` e si include questo contenuto in un'azione successiva senza eseguire il cast, anche la richiesta in uscita ha `Content-Type` impostato su `application/octet-stream`. In questo modo, App per la logica può garantire che i dati non vadano persi durante lo spostamento nel flusso di lavoro. Tuttavia, lo stato dell'azione, o gli input e gli output, viene archiviato in un oggetto JSON mentre attraversa il flusso di lavoro. 

## <a name="converter-functions"></a>Funzioni di conversione

Per mantenere alcuni tipi di dati, App per la logica converte il contenuto in una stringa binaria con codifica Base64, con i metadati appropriati per conservare sia il payload `$content` che `$content-type`, i quali vengono convertiti automaticamente. 

Questo elenco descrive come App per la logica converte il contenuto quando si usano queste [funzioni](../logic-apps/workflow-definition-language-functions-reference.md):

* `json()`: esegue il cast dei dati in `application/json`
* `xml()`: esegue il cast dei dati in `application/xml`
* `binary()`: esegue il cast dei dati in `application/octet-stream`
* `string()`: esegue il cast dei dati in `text/plain`
* `base64()`: converte il contenuto in una stringa con codifica Base64
* `base64toString()`: converte una stringa con codifica Base64 in `text/plain`
* `base64toBinary()`: converte una stringa con codifica Base64 in `application/octet-stream`
* `dataUri()`: converte una stringa in un URI di dati
* `dataUriToBinary()`: converte un URI di dati in una stringa binaria
* `dataUriToString()`: converte un URI di dati in una stringa

Ad esempio, se si riceve una richiesta HTTP in cui `Content-Type` è impostato su `application/xml`, come questo contenuto:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

È possibile eseguire il cast del contenuto tramite l'espressione `@xml(triggerBody())` con le funzioni `xml()` e `triggerBody()`, quindi usare tale contenuto in un secondo momento. In alternativa è possibile usare l'espressione `@xpath(xml(triggerBody()), '/CustomerName')` con le funzioni `xpath()` e `xml()`. 

## <a name="other-content-types"></a>Altri tipi di contenuto

App per la logica è compatibile con altri tipi di contenuto, ma potrebbe richiedere di ottenere manualmente il corpo del messaggio decodificando la variabile `$content`.

Ad esempio, si supponga che l'app per la logica sia attivata da una richiesta con il tipo di contenuto `application/x-www-url-formencoded`. Per mantenere tutti i dati, la variabile `$content` nel corpo della richiesta contiene un payload codificato come stringa Base64:

`CustomerName=Frank&Address=123+Avenue`

Poiché la richiesta non è in testo normale o JSON, la richiesta viene archiviata nell'azione come indicato di seguito:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

App per la logica offre funzioni native di gestione dei dati di modulo, ad esempio: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

In alternativa è possibile accedere manualmente ai dati usando un'espressione, come in questo esempio:

`@string(body('formdataAction'))` 

Se la richiesta in uscita deve avere la stessa intestazione content-type `application/x-www-url-formencoded`, è possibile aggiungere la richiesta al corpo dell'azione senza eseguire il cast usando un'espressione come `@body('formdataAction')`. Questo metodo tuttavia funziona solo quando il corpo è l'unico parametro nell'input `body`. Se si tenta di usare l'espressione `@body('formdataAction')` in una richiesta `application/json`, si ottiene un errore di runtime perché viene inviato il corpo codificato.
