---
title: Chiavi di sottoscrizione-LUIS
titleSuffix: Azure Cognitive Services
description: Non è necessario creare chiavi di sottoscrizione per usare le prime 1000 query di endpoint gratuite. Se si riceve un errore di _superamento della quota_ in forma di messaggio HTTP 403 o 429, è necessario creare una chiave e assegnarla all'app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: 1f8b84722c881cee1fe196e5a614b58cf3c19031
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932851"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Uso delle chiavi della sottoscrizione con l'app LUIS

Quando si usa per la prima volta Language Understanding (LUIS), non è necessario creare chiavi di sottoscrizione. Si ricevono le query endpoint 1000 per iniziare. 

Solo in caso di test o come prototipo, usa il livello (F0) gratuito. Per i sistemi di produzione, usa un livello [a pagamento](https://aka.ms/luis-price-tier). Non usare la [chiave di creazione](luis-concept-keys.md#authoring-key) per le query di endpoint nell'ambiente di produzione.


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Creazione della risorsa di runtime dell'endpoint di stima nel portale di Azure

Si crea la [risorsa dell'endpoint di stima](get-started-portal-deploy-app.md#create-the-endpoint-resource) nel portale di Azure. Dovrà essere usata solo per le query di stima dell'endpoint. Non usarla per apportare modifiche all'app.

È possibile creare una risorsa Language Understanding o una risorsa Servizi cognitivi. Se si sta creando una risorsa di Language Understanding, è consigliabile antepore il tipo di risorsa al nome della risorsa. 

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

### <a name="using-resource-from-luis-portal"></a>Uso della risorsa dal portale LUIS

Se si usa la risorsa dal portale LUIS, non è necessario conoscerne la chiave e la località. È invece necessario conoscerne il tenant, la sottoscrizione e il nome della risorsa.

Una volta [assegnata](#assign-resource-key-to-luis-app-in-luis-portal) la risorsa all'app Luis nel portale Luis, la chiave e la località vengono fornite come parte dell'URL dell'endpoint di stima della query nella pagina **chiavi e Impostazioni endpoint** della sezione Gestisci.
 
### <a name="using-resource-from-rest-api-or-sdk"></a>Uso della risorsa dall'API REST o dall'SDK

Se si usa la risorsa dalle API REST o dall'SDK, è necessario conoscerne la chiave e il percorso. Queste informazioni vengono fornite come parte dell'URL dell'endpoint di stima della query nella pagina **chiavi e Impostazioni endpoint** della sezione Gestisci, oltre che nella portale di Azure, nelle pagine Panoramica e chiavi della risorsa.

## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Assegnare la chiave di risorsa all'app LUIS nel portale LUIS

Ogni volta che si crea una nuova risorsa per LUIS, è necessario [assegnare la risorsa all'app Luis](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal). Dopo l'assegnazione, non sarà più necessario eseguire questo passaggio a meno che non venga creata una nuova risorsa. Si potrebbe creare una nuova risorsa per espandere le aree dell'app oppure per supportare un numero più elevato di query di stima.

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
In **Impostazioni URL endpoint** la casella di controllo**Controllo ortografico Bing** permette a LUIS di correggere l'ortografia prima della stima. Creare una **[chiave del Controllo ortografico Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** . 

Aggiungere il parametro querystring **spellCheck=true** e **bing-spell-check-subscription-key={CHIAVE_BING}** . Sostituire `{YOUR_BING_KEY_HERE}` con la chiave del controllo ortografico di Bing.

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

Per scopi di automazione, ad esempio una pipeline CI/CD, è possibile automatizzare l'assegnazione di una risorsa LUIS a un'app LUIS. Per farlo, è necessario seguire questa procedura:

1. Ottenere un token di Azure Resource Manager per questo [sito Web](https://resources.azure.com/api/token?plaintext=true). Il token scade, perciò usarlo immediatamente. La richiesta restituisce un token di Azure Resource Manager.

    ![Richiedere il token di Azure Resource Manager e ricevere il token di Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Usare il token per richiedere le risorse LUIS tra sottoscrizioni, dall'API [Get LUIS azure accounts](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) (Ottenere account LUIS di Azure), a cui l'account ha accesso. 

    Questa API POST richiede le impostazioni seguenti:

    |Intestazione|Value|
    |--|--|
    |`Authorization`|Il valore di `Authorization` è `Bearer {token}`. Si noti che il valore del token deve essere preceduto dalla parola `Bearer` e uno spazio.| 
    |`Ocp-Apim-Subscription-Key`|La [chiave di creazione](luis-how-to-account-settings.md).|

    Questa API restituisce una matrice di oggetti JSON delle sottoscrizioni LUIS inclusi ID sottoscrizione, gruppo di risorse e nome della risorsa restituito come nome dell'account. Trovare l'elemento della matrice che rappresenta la risorsa LUIS da assegnare all'app LUIS. 

1. Assegnare il token alla risorsa servizio LUIS con l'API [Assign a LUIS azure accounts to an application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) (Assegnare un account LUIS di Azure a un'applicazione). 

    Questa API POST richiede le impostazioni seguenti:

    |Type|Impostazione|Value|
    |--|--|--|
    |Intestazione|`Authorization`|Il valore di `Authorization` è `Bearer {token}`. Si noti che il valore del token deve essere preceduto dalla parola `Bearer` e uno spazio.|
    |Intestazione|`Ocp-Apim-Subscription-Key`|La [chiave di creazione](luis-how-to-account-settings.md).|
    |Intestazione|`Content-type`|`application/json`|
    |QueryString|`appid`|L'ID dell'app LUIS. 
    |Body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quando l'API ha esito positivo, restituisce 201 - stato creato. 

## <a name="change-pricing-tier"></a>Modifica piano tariffario

1.  In [Azure](https://portal.azure.com), trova la sottoscrizione LUIS. Selezionare la sottoscrizione LUIS.
    ![Trova la sottoscrizione LUIS](./media/luis-usage-tiers/find.png)
1.  Selezionare **Piano tariffario** per visualizzare i piani tariffari disponibili. 
    ![Visualizza piano tariffario](./media/luis-usage-tiers/subscription.png)
1.  Selezionare il piano tariffario e scegliere **Seleziona** per salvare la modifica. 
    ![Modifica il piano tariffario LUIS](./media/luis-usage-tiers/plans.png)
1.  Una volta completata la modifica dei prezzi, una finestra popup verifica il nuovo piano tariffario. 
    ![Verifica il piano tariffario LUIS](./media/luis-usage-tiers/updated.png)
1. Ricorda di [assegnare questa chiave di endpoint](#assign-endpoint-key) nella pagina **Pubblica** e di usarla in tutte le query di endpoint. 

## <a name="fix-http-status-code-403-and-429"></a>Correzione del codice di stato HTTP 403 e 429

Si ottengono i codici di stato di errore 403 e 429 quando si superano le transazioni al secondo o le transazioni al mese per il piano tariffario.

### <a name="when-you-receive-an-http-403-error-status-code"></a>Quando si riceve un codice di stato di errore HTTP 403

Quando si usano tutte le query di endpoint 1000 gratuite o si supera la quota di transazioni mensili del piano tariffario, si riceve un codice di stato di errore HTTP 403. 

Per correggere l'errore, è necessario [modificare il](luis-how-to-azure-subscription.md#change-pricing-tier) piano tariffario a un livello superiore o [creare una nuova risorsa](get-started-portal-deploy-app.md#create-the-endpoint-resource) e [assegnarla all'app](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Le soluzioni per questo errore includono:

* Nel [portale di Azure](https://portal.azure.com), sulla risorsa Language Understanding, nel piano tariffario di **gestione delle risorse->** modificare il piano tariffario in un livello di TPS superiore. Non è necessario eseguire alcuna operazione nel portale di Language Understanding se la risorsa è già stata assegnata all'app Language Understanding.
*  Se l'utilizzo supera il piano tariffario più elevato, aggiungere altre Language Understanding risorse con un servizio di bilanciamento del carico. Il [contenitore Language Understanding](luis-container-howto.md) con Kubernetes o Docker compose può essere utile per questa operazione.

### <a name="when-you-receive-an-http-429-error-status-code"></a>Quando si riceve un codice di stato di errore HTTP 429

Questo codice di stato viene restituito quando le transazioni al secondo superano il piano tariffario.  

Le soluzioni includono:

* Se non si è al livello più elevato, è possibile [aumentare il](#change-pricing-tier)piano tariffario.
* Se l'utilizzo supera il piano tariffario più elevato, aggiungere altre Language Understanding risorse con un servizio di bilanciamento del carico. Il [contenitore Language Understanding](luis-container-howto.md) con Kubernetes o Docker compose può essere utile per questa operazione.
* È possibile controllare le richieste dell'applicazione client con i [criteri di ripetizione](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) implementati quando si ottiene questo codice di stato. 

## <a name="viewing-summary-usage"></a>Visualizzazione del riepilogo dell'utilizzo
È possibile visualizzare le informazioni sull'utilizzo di LUIS in Azure. La pagina **Panoramica** contiene informazioni di riepilogo recenti, incluse chiamate ed errori. Se effettui una richiesta LUIS di endpoint, quindi osservi la **pagina Panoramica**, attendi fino a cinque minuti perché compaia l'utilizzo.

![Visualizzazione del riepilogo dell'utilizzo](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Personalizzazione di grafici relativi all'utilizzo
Metrica offre una visualizzazione più dettagliata dei dati.

![Metriche predefinite](./media/luis-usage-tiers/metrics-default.png)

È possibile configurare i grafici delle metriche per periodo di tempo e il tipo di metrica. 

![Metriche personalizzate](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Avviso di superamento della soglia delle transazioni totali
Se desideri sapere quando è stata raggiunta una determinata soglia delle transazioni, ad esempio 10.000 transazioni, puoi creare un avviso. 

![Avvisi predefiniti](./media/luis-usage-tiers/alert-default.png)

Aggiungi un avviso di metrica per la metrica **Totale chiamate** riferito a un determinato periodo di tempo. Aggiungi gli indirizzi e-mail di tutti gli utenti che devono ricevere l'avviso. Aggiungi webhook per tutti i sistemi che devono ricevere l'avviso. È anche possibile eseguire un'app per la logica quando viene generato l'avviso. 

## <a name="next-steps"></a>Passaggi successivi

Impara a usare le [versioni](luis-how-to-manage-versions.md) per gestire le modifiche all'app LUIS.
