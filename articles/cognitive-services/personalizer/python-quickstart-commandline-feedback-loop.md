---
title: 'Guida introduttiva: Libreria client di Personalizza esperienze per Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Introduzione alla libreria client di Personalizza esperienze per Python con un ciclo di apprendimento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 08/09/2019
ms.author: diberry
ms.openlocfilehash: ca1478801ad704888266175a23b6f436d067dd10
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950892"
---
# <a name="quickstart-personalize-client-library-for-python"></a>Guida introduttiva: Libreria client di Personalizza esperienze per Python

Visualizzare contenuto personalizzato in questo argomento di avvio rapido su Python con il servizio Personalizza esperienze.

Introduzione alla libreria client di Personalizza esperienze per Python. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

 * Classificare un elenco di azioni per la personalizzazione.
 * Segnalare il punteggio di ricompensa che indica l'esito positivo dell'azione nella posizione più alta in classifica.

[Pacchetto (PyPI)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [Esempi](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-personalizer-azure-resource"></a>Creare una risorsa di Azure di Personalizza esperienze

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Personalizza esperienze usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Ottenere un [codice della versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services), valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/).

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, creare due [variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY` per la chiave della risorsa.
* `PERSONALIZER_ENDPOINT` per l'endpoint della risorsa.

### <a name="install-the-python-library-for-personalizer"></a>Installare la libreria Python per Personalizza esperienze

Installare la libreria client di Personalizza esperienze per Python con il comando seguente:

```console
pip install azure-cognitiveservices-personalizer
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.

### <a name="change-the-model-update-frequency"></a>Modificare la frequenza di aggiornamento del modello

Nella risorsa Personalizza esperienze del portale di Azure impostare **Frequenza di aggiornamento del modello** su 10 secondi. In questo modo il training del servizio verrà eseguito rapidamente e sarà possibile visualizzare il cambiamento dell'azione più alta in classifica per ogni iterazione.

![Modificare la frequenza di aggiornamento del modello](./media/settings/configure-model-update-frequency-settings.png)

Quando viene creata per la prima volta un'istanza di un ciclo di Personalizza esperienze, non è disponibile alcun modello, perché non sono state effettuate chiamate all'API per le ricompense da cui eseguire il training. Le chiamate a Classifica restituiranno uguali probabilità per ogni elemento. L'applicazione dovrà sempre classificare il contenuto usando l'output di RewardActionId.

## <a name="object-model"></a>Modello a oggetti

Il client di Personalizza esperienze è un oggetto PersonalizerClient che esegue l'autenticazione in Azure con Microsoft.Rest.ServiceClientCredentials, che contiene la chiave.

Per chiedere una classificazione del contenuto, creare un oggetto RankRequest e quindi passarlo al metodo client.Rank. Il metodo Rank restituisce un oggetto RankResponse che contiene il contenuto classificato. 

Per inviare una ricompensa a Personalizza esperienze, creare un oggetto RewardRequest e quindi passarlo al metodo client.Reward. 

In questo argomento di avvio rapido, determinare la ricompensa è un'operazione banale. In un sistema di produzione invece, stabilire cosa influisce sul [punteggio di ricompensa](concept-rewards.md) e in quale misura può essere un processo complesso, che si può decidere di cambiare nel tempo. Questa dovrebbe essere una delle principali decisioni di progettazione da prendere nell'architettura di Personalizza esperienze. 

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di Personalizza esperienze per Python:

* [Creare un client di Personalizza esperienze](#create-a-personalizer-client)
* [Richiedere una classificazione](#request-a-rank)
* [Inviare una ricompensa](#send-a-reward)

## <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare una nuova applicazione Python nell'IDE o nell'editor preferito, denominata `sample.py`. 

## <a name="add-the-dependencies"></a>Aggiungere le dipendenze

Dalla directory del progetto aprire il file **Program.cs** nell'ambiente di sviluppo integrato o nell'editor preferito. Sostituire il codice `using` esistente con le direttive `using` seguenti:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Aggiungere le informazioni sulla risorsa di Personalizza esperienze

Nella classe **Program** creare le variabili per la chiave e l'endpoint di Azure della risorsa estratti dalle variabili di ambiente, denominate `PERSONALIZER_RESOURCE_KEY` e `PERSONALIZER_RESOURCE_ENDPOINT`. Se le variabili di ambiente sono state create dopo l'avvio dell'applicazione, per accedere alle variabili sarà necessario chiudere e ricaricare l'editor, la shell o l'ambiente di sviluppo integrato in cui sono in esecuzione. I metodi verranno creati in un secondo momento in questo argomento di avvio rapido.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Creare un client di Personalizza esperienze

Si creerà ora un metodo per restituire un client di Personalizza esperienze. Il parametro per il metodo è `PERSONALIZER_RESOURCE_ENDPOINT` e l'ApiKey è `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Ottenere scelte di contenuto rappresentate come azioni

Le azioni rappresentano le scelte di contenuto che si vuole classificare con Personalizza esperienze. Aggiungere i metodi seguenti alla classe Program per ottenere l'input di un utente dalla riga di comando per l'ora del giorno e la preferenza alimentare attuale.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Creare il ciclo di apprendimento

Il ciclo di apprendimento di Personalizza esperienze è un ciclo di chiamate [rank](#request-a-rank) e [reward](#send-a-reward). In questa guida di avvio rapido ogni chiamata di classificazione, per personalizzare il contenuto, è seguita da una chiamata di ricompensa, per indicare a Personalizza esperienze come il servizio ha classificato il contenuto. 

Il codice seguente nel metodo `main` del programma esegue un ciclo in cui chiede all'utente di indicare le sue preferenze dalla riga di comando, invia tali informazioni a Personalizza esperienze per la classificazione, presenta all'utente una selezione in forma di classifica da cui scegliere, quindi invia una ricompensa a Personalizza esperienze per segnalare l'accuratezza del servizio nella classificazione della selezione.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Nelle sezioni seguenti verranno esaminate più in dettaglio le chiamate rank e reward.

## <a name="request-a-rank"></a>Richiedere una classificazione

Per completare la richiesta di classificazione, il programma richiede le preferenze dell'utente per creare un `currentContent` delle scelte di contenuto. Il processo può creare contenuto da escludere dalla classificazione, indicato come `excludeActions`. La richiesta di classificazione ha bisogno delle azioni, di currentContext, excludeActions e di un ID evento di classificazione univoco (come GUID) per ricevere la risposta classificata. 

In questo argomento di avvio rapido sono state usate funzionalità di contesto semplici come l'ora del giorno e le preferenze alimentari dell'utente. Nei sistemi di produzione, determinare e [valutare](concept-feature-evaluation.md) [azioni e funzionalità](concepts-features.md) può non essere altrettanto semplice.  

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Inviare una ricompensa

Per completare la richiesta di ricompensa, il programma ottiene la selezione dell'utente dalla riga di comando, assegna un valore numerico a ogni selezione, quindi invia l'ID evento di classificazione univoco e il valore numerico al metodo reward.

Questo argomento di avvio rapido assegna un numero semplice come ricompensa, ovvero zero o 1. Nei sistemi di produzione, determinare quando e cosa inviare alla chiamata [reward](concept-rewards.md) può essere un'operazione non banale, a seconda delle esigenze specifiche. 

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Eseguire il programma

Eseguire l'applicazione con il comando python dalla directory dell'applicazione.

```console
python sample.py
```

![Il programma di avvio rapido pone alcune domande per raccogliere le preferenze utente, note come funzionalità, quindi restituisce l'azione più comune.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Funzionamento di Personalizza esperienze](how-personalizer-works.md)

* [Informazioni su Personalizza esperienze](what-is-personalizer.md)
* [In quali situazioni è possibile usare Personalizza esperienze?](where-can-you-use-personalizer.md)
* [Risoluzione dei problemi](troubleshooting.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
