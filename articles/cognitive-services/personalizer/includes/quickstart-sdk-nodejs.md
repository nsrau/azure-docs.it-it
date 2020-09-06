---
title: includere file
description: includere file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/27/2020
ms.openlocfilehash: 03680a2a6b4792a2bf522eff1462e29439e0f61b
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055390"
---
[Documentazione di riferimento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Pacchetto (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Esempi](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Versione corrente di [Node.js](https://nodejs.org) e npm.
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="creare una risorsa Personalizza esperienze"  target="_blank">creare una risorsa Personalizza esperienze <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Personalizza esperienze. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app.

```console
mkdir myapp && cd myapp
```

Eseguire il comando `npm init -y` per creare un file `package.json`.

```console
npm init -y
```

Creare una nuova applicazione Node.js nell'editor preferito o nell'IDE denominato `sample.js` e creare le variabili per l'endpoint e la chiave di sottoscrizione della risorsa. 

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```javascript
const uuidv1 = require('uuid/v1');
const Personalizer = require('@azure/cognitiveservices-personalizer');
const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
const readline = require('readline-sync');

// The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
const serviceKey = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>";

// The endpoint specific to your personalization service instance; 
// e.g. https://<your-resource-name>.cognitiveservices.azure.com
const baseUri = "https://<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>.cognitiveservices.azure.com";
```

### <a name="install-the-nodejs-library-for-personalizer"></a>Installare la libreria Node.js per Personalizza esperienze

Installare la libreria client di Personalizza esperienze per Node.js con il comando seguente:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Installare i pacchetti NPM rimanenti per questa guida di avvio rapido:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Modello a oggetti

Il client di Personalizza esperienze è un oggetto [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) che esegue l'autenticazione ad Azure con Microsoft.Rest.ServiceClientCredentials, che contiene la chiave.

Per chiedere il singolo elemento migliore del contenuto, creare un oggetto [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest), quindi passarlo al metodo [client.Rank](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-). Il metodo Rank restituisce un oggetto RankResponse.

Per inviare una ricompensa a Personalizza esperienze, creare un oggetto [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest) e quindi passarlo al metodo [Reward](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) nella classe Events.

In questo argomento di avvio rapido, determinare la ricompensa è un'operazione banale. In un sistema di produzione invece, stabilire cosa influisce sul [punteggio di ricompensa](../concept-rewards.md) e in quale misura può essere un processo complesso, che si può decidere di cambiare nel tempo. Questa dovrebbe essere una delle principali decisioni di progettazione da prendere nell'architettura di Personalizza esperienze.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come effettuare le operazioni seguenti con la libreria client di Personalizza esperienze per Node.js:

* [Creare un client di Personalizza esperienze](#authenticate-the-client)
* [API Classificazione](#request-the-best-action)
* [API Premio](#send-a-reward)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di `PersonalizerClient` con gli elementi `serviceKey` e `baseUri` creati in precedenza.

```javascript
const credentials = new CognitiveServicesCredentials(serviceKey);

// Initialize Personalization client.
const personalizerClient = new Personalizer.PersonalizerClient(credentials, baseUri);
```

## <a name="get-content-choices-represented-as-actions"></a>Ottenere scelte di contenuto rappresentate come azioni

Le azioni rappresentano le scelte di contenuto da cui si vuole che Personalizza esperienze selezioni l'elemento di contenuto migliore. Aggiungere i metodi seguenti alla classe Program per rappresentare il set di azioni e le relative caratteristiche.

```javascript
function getContextFeaturesList() {
  const timeOfDayFeatures = ['morning', 'afternoon', 'evening', 'night'];
  const tasteFeatures = ['salty', 'sweet'];

  let answer = readline.question("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n");
  let selection = parseInt(answer);
  const timeOfDay = selection >= 1 && selection <= 4 ? timeOfDayFeatures[selection - 1] : timeOfDayFeatures[0];

  answer = readline.question("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet\n");
  selection = parseInt(answer);
  const taste = selection >= 1 && selection <= 2 ? tasteFeatures[selection - 1] : tasteFeatures[0];

  console.log("Selected features:\n");
  console.log("Time of day: " + timeOfDay + "\n");
  console.log("Taste: " + taste + "\n");

  return [
    {
      "time": timeOfDay
    },
    {
      "taste": taste
    }
  ];
}
```

```javascript
function getActionsList() {
  return [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium"
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low"
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ];
}
```

## <a name="create-the-learning-loop"></a>Creare il ciclo di apprendimento

Il ciclo di apprendimento di Personalizza esperienze è un ciclo di chiamate [Rank](#request-the-best-action) e [Reward](#send-a-reward). In questo argomento di avvio rapido ogni chiamata Rank per personalizzare il contenuto è seguita da una chiamata Reward per indicare a Personalizza esperienze il livello di prestazioni del servizio.

Il codice seguente esegue un ciclo in cui chiede all'utente di indicare le proprie preferenze alla riga di comando, invia tali informazioni a Personalizza esperienze per la selezione dell'azione migliore, presenta all'utente una selezione sotto forma di elenco da cui scegliere e quindi invia una ricompensa a Personalizza esperienze per segnalare l'accuratezza del servizio nella selezione.

```javascript
let runLoop = true;

do {

  let rankRequest = {}

  // Generate an ID to associate with the request.
  rankRequest.eventId = uuidv1();

  // Get context information from the user.
  rankRequest.contextFeatures = getContextFeaturesList();

  // Get the actions list to choose from personalization with their features.
  rankRequest.actions = getActionsList();

  // Exclude an action for personalization ranking. This action will be held at its current position.
  rankRequest.excludedActions = getExcludedActionsList();

  rankRequest.deferActivation = false;

  // Rank the actions
  const rankResponse = await personalizerClient.rank(rankRequest);

  console.log("\nPersonalization service thinks you would like to have:\n")
  console.log(rankResponse.rewardActionId);

  // Display top choice to user, user agrees or disagrees with top choice
  const reward = getReward();

  console.log("\nPersonalization service ranked the actions with the probabilities as below:\n");
  for (let i = 0; i < rankResponse.ranking.length; i++) {
    console.log(JSON.stringify(rankResponse.ranking[i]) + "\n");
  }

  // Send the reward for the action based on user response.

  const rewardRequest = {
    value: reward
  }

  await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);

  runLoop = continueLoop();

} while (runLoop);
```

Nelle sezioni seguenti verranno esaminate più in dettaglio le chiamate rank e reward.

Aggiungere i metodi seguenti, che [ottengono le scelte di contenuto](#get-content-choices-represented-as-actions), prima di eseguire il file di codice:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>Richiedere l'azione migliore

Per completare la richiesta Rank, il programma chiede le preferenze dell'utente per creare scelte di contenuto. Il processo può creare contenuto da escludere dalle azioni, indicato come `excludeActions`. Per ricevere la risposta, la richiesta Rank deve includere le [azioni](../concepts-features.md#actions-represent-a-list-of-options) e le relative caratteristiche, le caratteristiche currentContext, excludeActions e un ID evento di classificazione univoco.

In questo argomento di avvio rapido sono state usate funzionalità di contesto semplici come l'ora del giorno e le preferenze alimentari dell'utente. Nei sistemi di produzione, determinare e [valutare](../concept-feature-evaluation.md) [azioni e funzionalità](../concepts-features.md) può non essere altrettanto semplice.

```javascript
let rankRequest = {}

// Generate an ID to associate with the request.
rankRequest.eventId = uuidv1();

// Get context information from the user.
rankRequest.contextFeatures = getContextFeaturesList();

// Get the actions list to choose from personalization with their features.
rankRequest.actions = getActionsList();

// Exclude an action for personalization ranking. This action will be held at its current position.
rankRequest.excludedActions = getExcludedActionsList();

rankRequest.deferActivation = false;

// Rank the actions
const rankResponse = await personalizerClient.rank(rankRequest);
```

## <a name="send-a-reward"></a>Inviare una ricompensa

Per ottenere il punteggio di ricompensa e inviare la richiesta Reward, il programma ottiene la selezione dell'utente dalla riga di comando, assegna un valore numerico alla selezione, quindi invia l'ID evento univoco e il valore numerico all'API Premio.

Questo argomento di avvio rapido assegna un numero semplice come punteggio di ricompensa, ovvero 0 o 1. Nei sistemi di produzione, determinare quando e cosa inviare alla chiamata [Reward](../concept-rewards.md) può essere un'operazione non banale, a seconda delle esigenze specifiche.

```javascript
const rewardRequest = {
  value: reward
}

await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);
```

## <a name="run-the-program"></a>Eseguire il programma

Eseguire l'applicazione con Node.js dalla directory dell'applicazione.

```console
node sample.js
```

![Il programma di avvio rapido pone alcune domande per raccogliere le preferenze utente, note come funzionalità, quindi restituisce l'azione più comune.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
