---
title: Chiamare l'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come chiamare i servizi cognitivi di Azure Analisi del testo l'API REST e il post.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: afb576c265ccdd4a014ed678331f030a0442a197
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286613"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Come chiamare l'API REST Analisi del testo

Le chiamate dell'**API Analisi del testo** sono chiamate HTTP POST/GET che è possibile formulare in qualsiasi linguaggio. In questo articolo si usa REST e [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) per illustrare i concetti chiave.

Ogni richiesta deve includere la chiave di accesso e un endpoint HTTP. L'endpoint specifica l'area scelta durante la registrazione, l'URL del servizio e una risorsa utilizzata nella richiesta: `sentiment`, `keyphrases`, `languages` e `entities`. 

È importante ricordare che Analisi del testo è senza stato perciò non ci sono risorse dati da gestire. Il testo viene caricato, analizzato al momento della ricezione e i risultati vengono restituiti immediatamente all'applicazione chiamante.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definizione dello schema JSON

L'input deve essere JSON in testo non elaborato e non strutturato. XML non è supportato. Lo schema è semplice ed è costituito dagli elementi descritti nell'elenco seguente. 

Attualmente è possibile inviare gli stessi documenti per tutte le operazioni di Analisi del testo: sentiment, frase chiave, rilevamento della lingua e identificazione delle entità. In futuro probabilmente lo schema varierà per ogni analisi.

| Elemento | Valori validi | Obbligatorio? | Utilizzo |
|---------|--------------|-----------|-------|
|`id` |Il tipo di dati è stringa, ma in pratica gli ID documento tendono a essere numeri interi. | obbligatori | Il sistema usa gli ID immessi per strutturare l'output. Per ogni ID della richiesta vengono generati codici di lingua, frasi chiave e punteggi di sentiment.|
|`text` | Testo non elaborato non strutturato, composto da un massimo di 5.120 caratteri. | obbligatori | Per il rilevamento della lingua, il testo può essere espresso in qualsiasi lingua. Per l'analisi del sentiment, l'estrazione delle frasi chiave e l'identificazione delle entità, il testo deve essere in una [lingua supportata](../text-analytics-supported-languages.md). |
|`language` | Codice [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) di 2 caratteri per una [lingua supportata](../text-analytics-supported-languages.md) | Variabile | Richiesto per l'analisi del sentiment, l'estrazione delle frasi chiave e il collegamento delle entità; facoltativo per il rilevamento della lingua. Se lo si omette non si verifica nessun errore ma l'analisi risulta più debole. Il codice della lingua deve corrispondere al `text` fornito. |

Per altre informazioni sui limiti, vedere [Panoramica di Analisi del testo > Limiti relativi ai dati](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Configurare una richiesta in Postman

Il servizio accetta richieste fino alla dimensione di 1 MB. Se si usa Postman o un altro strumento di test dell'API Web, configurare l'endpoint per includere la risorsa da usare e fornire la chiave di accesso nell'intestazione della richiesta. Ogni operazione richiede che si accodi la risorsa appropriata all'endpoint. 

1. In Postman:

   + Scegliere **Post** come tipo di richiesta.
   + Incollare l'endpoint che è stato copiato dalla pagina del portale.
   + Accodare una risorsa.

   Gli endpoint di risorsa sono i seguenti (l'area può variare):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Impostare le tre intestazioni della richiesta:

   + `Ocp-Apim-Subscription-Key`: la chiave di accesso, ottenuta dal portale di Azure.
   + `Content-Type`: applicazione/json.
   + `Accept`: applicazione/json.

   La richiesta dovrebbe essere simile allo screenshot seguente, presupponendo che la risorsa sia **/keyPhrases**.

   ![Screenshot di richiesta con l'endpoint e intestazioni](../media/postman-request-keyphrase-1.png)

4. Fare clic su **Body** (Corpo) e scegliere **raw** (non elaborato) per il formato.

   ![Screenshot di richiesta con impostazioni relative al corpo](../media/postman-request-body-raw.png)

5. Incollare alcuni documenti JSON in un formato valido per l'analisi desiderata. Per altre informazioni su una determinata analisi, vedere gli argomenti seguenti:

  + [Rilevamento della lingua](text-analytics-how-to-language-detection.md)  
  + [Estrazione delle frasi chiave](text-analytics-how-to-keyword-extraction.md)  
  + [Analisi del sentiment](text-analytics-how-to-sentiment-analysis.md)  
  + [Riconoscimento delle entità](text-analytics-how-to-entity-linking.md)  


6. Fare clic su **Send** (Invia) per inviare la richiesta. Per informazioni sul numero di richieste che è possibile inviare al minuto e al secondo, vedere la sezione relativa ai [limiti dei dati](../overview.md#data-limits) nella panoramica.

   In Postman la risposta è visualizzata nella finestra successiva in basso, come singolo documento JSON, con un elemento per ogni ID documento specificato nella richiesta.

## <a name="see-also"></a>Vedere anche 

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Rilevare la lingua](text-analytics-how-to-language-detection.md)
