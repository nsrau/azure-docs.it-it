---
title: Pubblicare l'app LUIS - Azure | Microsoft Docs
description: Dopo aver compilato e testato l'app usando Language Understanding, ovvero LUIS, pubblicarla come servizio Web in Azure.
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 1bd24af0498755b7cdcb170624fd8f9f3b39c85a
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341290"
---
# <a name="publish-your-trained-app"></a>Pubblicare l'app sottoposta a training
Dopo aver compilato e testato l'app LUIS, pubblicarla. In seguito alla pubblicazione dell'app, la pagina Pubblica mostra gli [endpoint](luis-glossary.md#endpoint) HTTP associati. Questi endpoint per [area](luis-reference-regions.md) e per [chiave](luis-how-to-manage-keys.md), vengono integrati in un client, chatbot o applicazione back-end qualsiasi. 

È sempre possibile [testare](interactive-test.md) l'app prima di pubblicarla. 

## <a name="production-and-staging-slots"></a>Slot di staging e di produzione
È possibile pubblicare l'app nello **slot di staging** o nello **slot di produzione**. Usando due slot di pubblicazione è possibile avere due diverse versioni con endpoint pubblicati oppure la stessa versione su due endpoint diversi. 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>L'impostazione della configurazione richiede il modello di pubblicazione
Pubblicare nell'endpoint dopo aver modificato le impostazioni seguenti. 

## <a name="external-services-settings"></a>Impostazioni di servizi esterni
Le impostazioni di servizi esterni includono **[Analisi del sentiment](#enable-sentiment-analysis)** e **[Priming del riconoscimento vocale](#enable-speech-priming)**.

### <a name="enable-sentiment-analysis"></a>Abilitare l'analisi del sentiment
In **Impostazioni di servizi esterni** la casella di controllo **Enable Sentiment Analysis** (Abilita analisi del sentiment) consente a LUIS di integrarsi con [Analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) per specificare la valutazione e la frase chiave. Non è necessario specificare una chiave di analisi del testo e non è previsto alcun addebito per questo servizio al proprio account Azure. Dopo averla selezionata, questa impostazione è permanente. 

I dati sentiment sono un punteggio compreso tra 1 e 0 che indica il sentiment positivo (più vicino a 1) o negativo (più vicino a 0) dei dati.

Per altre informazioni sulla risposta dell'endpoint JSON con l'analisi del sentiment, vedere [Analisi del sentiment](luis-concept-data-extraction.md#sentiment-analysis)

### <a name="enable-speech-priming"></a>Abilitare il priming del riconoscimento vocale 
In **Impostazioni di servizi esterni** la casella di controllo **Enable Speech Priming** (Abilita priming di riconoscimento vocale) consente di avere un solo endpoint per ottenere un'espressione vocale da un chatbot o da un'applicazione che chiama LUIS e riceve una risposta di previsione di LUIS. Il priming di riconoscimento vocale usa il servizio cognitivo [Speech API](../Speech-Service/rest-apis.md). 

![Finestra di dialogo di conferma del priming di riconoscimento vocale](./media/luis-how-to-publish-app/speech-prime-modal.png)

Dopo aver abilitato questa funzionalità, pubblicare l'app. Quando si pubblica l'app LUIS, il modello di dell'app viene inviato al servizio di riconoscimento vocale per preparare il servizio di riconoscimento vocale. Le informazioni del modello **non** vengono usate all'esterno del proprio servizio. 

Per completare l'uso del priming di riconoscimento vocale, è necessario avere le informazioni seguenti per usare [Speech SDK](../speech-service/speech-sdk-reference.md):
* Una chiave endpoint LUIS.
* L'ID dell'app LUIS.
* Un dominio di endpoint, definito "Hostname" in Speech SDK, ad esempio "westus.api.cognitive.microsoft.com", in cui il primo sottodominio è l'area in cui avviene la pubblicazione dell'app.

Per altre informazioni, consultare l'esempio [Riconoscimento finalità voce](http://aka.ms/speechsdk).

Quando si elimina l'app LUIS o il servizio di Speech, i dati del modello vengono rimossi. 

## <a name="endpoint-url-settings"></a>Impostazioni URL dell'endpoint
Le impostazioni dei servizi URL dell'endpoint comprendono l'offset del **[fuso orario](#set-timezone-offset)**, **[tutti i punteggi di finalità stimati](#include-all-predicted-intent-scores)** e il **[controllo ortografico di Bing](#enable-bing-spell-checker)**.

### <a name="set-timezone-offset"></a>Impostare l'offset del fuso orario 
Parte dell'opzione di scelta dello slot consiste nella selezione del fuso orario. L'impostazione del fuso orario consente a LUIS di [alterare](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) qualsiasi valore di datetimeV2 predefinito durante la stima, in modo che i dati dell'entità restituiti siano corretti in base al fuso orario selezionato. 

### <a name="include-all-predicted-intent-scores"></a>Includere tutti i punteggi di finalità stimati
La casella di controllo **Include all predicted intent scores** (Includi tutti i punteggi di finalità stimati) consente alla risposta della query dell'endpoint di includere il punteggio della previsione per ogni finalità. 

Questa impostazione consente al chatbot o all'applicazione che chiama LUIS di prendere una decisione a livello di codice in base ai punteggi delle finalità restituite. In genere i primi due tipi di finalità sono quelli più interessanti. Se il punteggio più alto ha finalità None, il chatbot può porre una domanda di completamento che consente di fare una scelta definitiva tra la finalità None e altre finalità per punteggi elevati. 

Queste finalità e i relativi punteggi sono inclusi anche nei log di endpoint. È possibile [esportare](create-new-app.md#export-app) i log e analizzarne i punteggi. 

```
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Abilitare il controllo ortografico di Bing 
In **Endpoint url settings** (Impostazioni URL dell'endpoint) la casella di controllo **Enable Bing spell checker** (Abilita controllo ortografico di Bing) permette a LUIS di correggere l'ortografia prima della previsione. Per questa operazione è necessario che l'utente crei una **[chiave per il controllo ortografico di Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. Dopo aver creato la chiave, nella pagina di pubblicazione vengono aggiunti due parametri della stringa di query all'URL dell'endpoint. 

Se si creano gli URL personali per l'applicazione che chiama LUIS, assicurarsi che ci sia il parametro della stringa di query **spellCheck=true** e **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}**. Sostituire `{YOUR_BING_KEY_HERE}` con la chiave del controllo ortografico di Bing.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>Pubblicare l'app sottoposta a training in un endpoint HTTP
Aprire l'app facendo clic sul relativo nome nella pagina **App personali** e selezionare **Pubblica** nel pannello superiore. 

![Pagina di pubblicazione.](./media/luis-how-to-publish-app/publish-to-production.png)
 
Dopo aver pubblicato correttamente l'app, nella parte superiore del browser viene visualizzata una notifica di operazione riuscita verde. 

* Scegliere se pubblicare in **Produzione** o **Staging** effettuando una selezione nel menu a discesa sotto **Seleziona slot**. 

## <a name="assign-key"></a>Assegnare la chiave

Se si desidera usare una chiave diversa da quella gratuita Starter_Key mostrata, fare clic sul pulsante **Aggiungi chiave**. Questa azione apre una finestra di dialogo che consente di selezionare una chiave dell'endpoint esistente da assegnare all'app. Per altre informazioni su come creare e aggiungere le chiavi di endpoint all'app LUIS, vedere [Gestire le chiavi](luis-how-to-manage-keys.md).

Per visualizzare gli endpoint e le chiavi associati ad altre aree, usare i pulsanti di opzione per passare da un'area all'altra. Ogni riga della tabella **Resources and Keys** (Risorse e chiavi) elenca le risorse di Azure associate all'account e le chiavi di endpoint associate a tale risorsa.

## <a name="endpoint-url-construction"></a>Costruzione dell'URL dell'endpoint
L'URL dell'endpoint corrisponde all'area di Azure associata alla chiave di endpoint.

Questa tabella riflette in modo efficace la configurazione di pubblicazione nell'URL dell'endpoint con scelte di route e valori della stringa di query. Se si stanno creando gli URL dell'endpoint per l'applicazione che chiama LUIS, assicurarsi che queste stesse route e questi stessi valori della stringa di query vengano impostati per l'endpoint usato, se si desidera impostarli.

La route dell'URL viene costruita con l'area e l'ID dell'app. Se si pubblica in altre aree o con altre app, l'URL dell'endpoint può essere creato cambiando i valori dell'ID dell'area e dell'app. 

* Selezionare lo slot di produzione, quindi fare clic sul pulsante **Publish** (Pubblica). Quando la pubblicazione ha esito positivo, usare l'URL dell'endpoint visualizzato per accedere all'app LUIS. 

### <a name="optional-query-string-parameters"></a>Parametri della stringa di query facoltativi
È possibile usare i parametri della stringa di query seguenti con l'URL dell'endpoint:

<!-- TBD: what about speech priming? -->

|Stringa di query|type|Valore di esempio|Scopo|
|--|--|--|--|
|verbose|boolean|true|Includere [tutti i punteggi di finalità](#include-all-predicted-intent-scores) per l'espressione|
|timezoneOffset|numero (l'unità è in minuti)|60|Impostare l'[offset del fuso orario](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) per le [entità predefinite datetimeV2](luis-reference-prebuilt-datetimev2.md)|
|spellCheck|boolean|true|[Correggere l'ortografia](#enable-bing-spell-checker) di un'espressione. Usato insieme al parametro della stringa di query bing-spell-check-subscription-key|
|bing-spell-check-subscription-key|ID sottoscrizione||Usato insieme al parametro della stringa di query spellCheck|
|staging|boolean|false|Selezionare l'endpoint di staging o quello di produzione|
|log|boolean|true|Aggiungere query e risultati al log|


## <a name="test-your-published-endpoint-in-a-browser"></a>Testare l'endpoint pubblicato in un browser
Testare l'endpoint pubblicato selezionando l'URL nella colonna **Endpoint**. Si apre il browser predefinito con l'URL generato. Impostare il parametro dell'URL "&q" nella query di test. Ad esempio, aggiungere `&q=Book me a flight to Boston on May 4` all'URL e quindi premere INVIO. Il browser mostra la risposta JSON dell'endpoint HTTP. 

![Risposta JSON di un endpoint HTTP pubblicato](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>Passaggi successivi

* Consultare [Gestire le chiavi](./luis-how-to-manage-keys.md) per aggiungere le chiavi all'app LUIS e capire come queste vengono mappate rispetto alle aree.
* Per istruzioni su come testare l'app pubblicata nella console di test, vedere [Eseguire il training e testare l'app](interactive-test.md).
