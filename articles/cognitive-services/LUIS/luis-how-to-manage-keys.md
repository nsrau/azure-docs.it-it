---
title: Gestire le chiavi
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dopo aver creato una chiave endpoint LUIS nel portale di Azure, assegnare la chiave all'app LUIS per ottenere l'URL endpoint corretto. Usare l'URL endpoint per ottenere stime LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0a278ded7ce290347645f345e4eee0b15972787f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088103"
---
# <a name="add-an-azure-luis-resource-to-app"></a>Aggiungere una risorsa LUIS all'app

Dopo aver creato una risorsa LUIS nel portale di Azure, assegnare la risorsa all'app LUIS per ottenere il corretto URL endpoint. Usare l'URL endpoint per ottenere stime LUIS.

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>


## <a name="assign-resource-in-luis-portal"></a>Assegnare risorse nel portale di LUIS

1. Creare una chiave LUIS sul [portale di Azure](https://portal.azure.com). Per ulteriori istruzioni, vedere [Creazione di una chiave endpoint usando Azure](luis-how-to-azure-subscription.md).
 
2. Selezionare **Gestisci** nel menu in alto a destra, quindi selezionare **Chiavi ed endpoint**.

    [ ![Pagina chiavi ed endpoint](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

3. Per aggiungere il servizio LUIS, selezionare **Assegnare una risorsa +**.

    ![Assegnare una risorsa all'app](./media/luis-manage-keys/assign-key.png)

4. Selezionare un tenant nella finestra di dialogo associata all'indirizzo e-mail attraverso il quale si accede al sito Web di LUIS.  

5. Scegliere il **Nome sottoscrizione** associato alla risorsa di Azure che si vuole aggiungere.

6. Selezionare il **Nome della risorsa LUIS**. 

7. Selezionare **Assign resource** (Assegnare una risorsa). 

8. Trovare la nuova riga nella tabella e copiare l'URL endpoint. Per ottenere una stima, è consigliabile inoltrare una richiesta HTTP GET all'endpoint del servizio LUIS. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>Annullare l'assegnazione di una risorsa
Quando si annulla l'assegnazione di una chiave endpoint, questa non viene eliminata da Azure. Viene soltanto scollegata da LUIS. 

Quando una chiave endpoint non viene assegnata o non viene assegnata all'app, qualsiasi richiesta all'URL endpoint viene restituisce un errore: `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Includere tutti i punteggi di finalità stimati
La casella di controllo **Include all predicted intent scores** (Includi tutti i punteggi di finalità stimati) consente alla risposta della query dell'endpoint di includere il punteggio della previsione per ogni finalità. 

Questa impostazione consente al chatbot o all'applicazione che chiama LUIS di prendere una decisione a livello di codice in base ai punteggi delle finalità restituite. In genere i primi due tipi di finalità sono quelli più interessanti. Se il punteggio più alto ha finalità None, il chatbot può porre una domanda di completamento che consente di fare una scelta definitiva tra la finalità None e altre finalità per punteggi elevati. 

Queste finalità e i relativi punteggi sono inclusi anche nei log di endpoint. È possibile [esportare](luis-how-to-start-new-app.md#export-app) i log e analizzarne i punteggi. 

```JSON
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
In **Impostazioni URL endpoint** la casella di controllo**Controllo ortografico Bing** permette a LUIS di correggere l'ortografia prima della stima. Creare una **[chiave del Controllo ortografico Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. 

Aggiungere il parametro querystring **spellCheck=true** e **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}**. Sostituire `{YOUR_BING_KEY_HERE}` con la chiave del controllo ortografico di Bing.

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


### <a name="publishing-regions"></a>Regioni di pubblicazione

Ulteriori informazioni sulle [regioni](luis-reference-regions.md) di pubblicazione, inclusa la pubblicazione in [Europa](luis-reference-regions.md#publishing-to-europe) e in [Australia](luis-reference-regions.md#publishing-to-australia). Le regioni di pubblicazione sono diverse dalle regioni di creazione. Creare un'app nella regione di creazione corrispondente alla regione di pubblicazione scelta per la query endpoint.

## <a name="assign-resource-without-luis-portal"></a>Assegnare risorse senza il portale di LUIS

Per scopi di automazione, ad esempio una pipeline CI/CD, è possibile automatizzare l'assegnazione di una risorsa LUIS a un'app LUIS. Per farlo è necessario seguire questa procedura:

1. Ottenere un token di Azure Resource Manager per questo [sito Web](https://resources.azure.com/api/token?plaintext=true). Il token scade, perciò usarlo immediatamente. La richiesta restituisce un token di Azure Resource Manager.

    ![Richiedere il token di Azure Resource Manager e ricevere il token di Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Usare il token per richiedere le risorse LUIS tra sottoscrizioni, dall'API [Get LUIS azure accounts](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) (Ottenere account LUIS di Azure), a cui l'account ha accesso. 

    Questa API POST richiede le impostazioni seguenti:

    |Intestazione|Valore|
    |--|--|
    |`Authorization`|Il valore di `Authorization` è `Bearer {token}`. Si noti che il valore del token deve essere preceduto dalla parola `Bearer` e uno spazio.| 
    |`Ocp-Apim-Subscription-Key`|La [chiave di creazione](luis-how-to-account-settings.md).|

    Questa API restituisce una matrice di oggetti JSON delle sottoscrizioni LUIS inclusi ID sottoscrizione, gruppo di risorse e nome della risorsa restituito come nome dell'account. Trovare l'elemento della matrice che rappresenta la risorsa LUIS da assegnare all'app LUIS. 

1. Assegnare il token alla risorsa servizio LUIS con l'API [Assign a LUIS azure accounts to an application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) (Assegnare un account LUIS di Azure a un'applicazione). 

    Questa API POST richiede le impostazioni seguenti:

    |type|Impostazione|Valore|
    |--|--|--|
    |Intestazione|`Authorization`|Il valore di `Authorization` è `Bearer {token}`. Si noti che il valore del token deve essere preceduto dalla parola `Bearer` e uno spazio.|
    |Intestazione|`Ocp-Apim-Subscription-Key`|La [chiave di creazione](luis-how-to-account-settings.md).|
    |Intestazione|`Content-type`|`application/json`|
    |QueryString|`appid`|L'ID dell'app LUIS. 
    |Corpo||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quando l'API ha esito positivo, restituisce 201 - stato creato. 

## <a name="next-steps"></a>Passaggi successivi

Usare la chiave per pubblicare l'app nella pagina **Publish app** (Pubblica app). Per istruzioni sulla pubblicazione, vedere [Pubblicare app](luis-how-to-publish-app.md).

Vedere [Chiavi in LUIS](luis-concept-keys.md) per comprendere i concetti chiave di endpoint e creazione in LUIS.
