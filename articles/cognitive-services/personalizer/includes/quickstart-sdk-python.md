---
title: includere file
description: includere file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.openlocfilehash: b602ab1a6aa6f9c4c153924bce63da12d872a62e
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133854"
---
[Documentazione di riferimento](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [Pacchetto (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [Esempi](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Uso di questo avvio rapido


Per usare questo avvio rapido, è necessario eseguire diversi passaggi:

* Nel portale di Azure creare una risorsa di Personalizza esperienze
* Nella pagina **Configurazione** della risorsa di Personalizza esperienze nel portale di Azure cambiare la frequenza di aggiornamento del modello impostando un intervallo molto breve
* In un editor di codice creare un file di codice e modificarlo
* Dalla riga di comando o dal terminale installare l'SDK dalla riga di comando
* Dalla riga di comando o dal terminale eseguire il file di codice

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>Installare la libreria Python per Personalizza esperienze

Installare la libreria client di Personalizza esperienze per Python con il comando seguente:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Modello a oggetti

Il client di Personalizza esperienze è un oggetto [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) che esegue l'autenticazione ad Azure con Microsoft.Rest.ServiceClientCredentials, che contiene la chiave.

Per chiedere il singolo elemento migliore del contenuto, creare un oggetto [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python), quindi passarlo al metodo client.Rank. Il metodo Rank restituisce un oggetto RankResponse.

Per inviare un punteggio di ricompensa a Personalizza esperienze, impostare l'ID evento e il punteggio di ricompensa (valore) da inviare al metodo [Reward](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) nella classe EventOperations.

In questo argomento di avvio rapido, determinare la ricompensa è un'operazione banale. In un sistema di produzione invece, stabilire cosa influisce sul [punteggio di ricompensa](../concept-rewards.md) e in quale misura può essere un processo complesso, che si può decidere di cambiare nel tempo. Questa dovrebbe essere una delle principali decisioni di progettazione da prendere nell'architettura di Personalizza esperienze.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di Personalizza esperienze per Python:

* [Creare un client di Personalizza esperienze](#create-a-personalizer-client)
* [API Classificazione](#request-the-best-action)
* [API Premio](#send-a-reward)

## <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare una nuova applicazione Python nell'IDE o nell'editor preferito, denominata `sample.py`.

## <a name="add-the-dependencies"></a>Aggiungere le dipendenze

Dalla directory del progetto aprire il file **sample.py** nell'ambiente di sviluppo integrato o nell'editor preferito. Aggiungere quanto segue:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Aggiungere le informazioni sulla risorsa di Personalizza esperienze

Creare le variabili per la chiave e l'endpoint di Azure della risorsa estratti dalle variabili di ambiente denominate `PERSONALIZER_RESOURCE_KEY` e `PERSONALIZER_RESOURCE_ENDPOINT`. Se le variabili di ambiente sono state create dopo l'avvio dell'applicazione, per accedere alle variabili sarà necessario chiudere e ricaricare l'editor, la shell o l'ambiente di sviluppo integrato in cui sono in esecuzione. I metodi verranno creati in un secondo momento in questo argomento di avvio rapido.

Il nome della risorsa fa parte dell'URL dell'endpoint: `https://<your-resource-name>.api.cognitive.microsoft.com/`.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Creare un client di Personalizza esperienze

Si creerà ora un metodo per restituire un client di Personalizza esperienze. Il parametro per il metodo è `PERSONALIZER_RESOURCE_ENDPOINT` e l'ApiKey è `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Ottenere scelte di contenuto rappresentate come azioni

Le azioni rappresentano le scelte di contenuto da cui si vuole che Personalizza esperienze selezioni l'elemento di contenuto migliore. Aggiungere i metodi seguenti alla classe Program per rappresentare il set di azioni e le relative caratteristiche.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Creare il ciclo di apprendimento

Il ciclo di apprendimento di Personalizza esperienze è un ciclo di chiamate [Rank](#request-the-best-action) e [Reward](#send-a-reward). In questo argomento di avvio rapido ogni chiamata Rank per personalizzare il contenuto è seguita da una chiamata Reward per indicare a Personalizza esperienze il livello di prestazioni del servizio.

Il codice seguente esegue un ciclo in cui chiede all'utente di indicare le proprie preferenze alla riga di comando, invia tali informazioni a Personalizza esperienze per la selezione dell'azione migliore, presenta all'utente una selezione sotto forma di elenco da cui scegliere e quindi invia una ricompensa a Personalizza esperienze per segnalare l'accuratezza del servizio nella selezione.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Aggiungere i metodi seguenti, che [ottengono le scelte di contenuto](#get-content-choices-represented-as-actions), prima di eseguire il file di codice:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Richiedere l'azione migliore


Per completare la richiesta Rank, il programma richiede le preferenze dell'utente per creare un oggetto `currentContent` delle scelte di contenuto. Il processo può creare contenuto da escludere dalle azioni, indicato come `excludeActions`. Per ricevere la risposta, la richiesta Rank deve includere le azioni e le relative caratteristiche, le caratteristiche currentContext, excludeActions e un ID evento univoco.

In questo argomento di avvio rapido sono state usate funzionalità di contesto semplici come l'ora del giorno e le preferenze alimentari dell'utente. Nei sistemi di produzione, determinare e [valutare](../concept-feature-evaluation.md) [azioni e funzionalità](../concepts-features.md) può non essere altrettanto semplice.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Inviare una ricompensa


Per ottenere il punteggio di ricompensa e inviare la richiesta Reward, il programma ottiene la selezione dell'utente dalla riga di comando, assegna un valore numerico alla selezione, quindi invia l'ID evento univoco e il valore numerico all'API Premio.

Questo argomento di avvio rapido assegna un numero semplice come punteggio di ricompensa, ovvero 0 o 1. Nei sistemi di produzione, determinare quando e cosa inviare alla chiamata [Reward](../concept-rewards.md) può essere un'operazione non banale, a seconda delle esigenze specifiche.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Eseguire il programma

Eseguire l'applicazione con il comando python dalla directory dell'applicazione.

```console
python sample.py
```

![Il programma di avvio rapido pone alcune domande per raccogliere le preferenze utente, note come funzionalità, quindi restituisce l'azione più comune.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)