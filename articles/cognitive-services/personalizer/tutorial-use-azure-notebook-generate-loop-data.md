---
title: 'Esercitazione: Azure Notebooks - Personalizza esperienze'
titleSuffix: Azure Cognitive Services
description: Questa esercitazione simula un ciclo di Personalizza esperienze in un notebook di Azure, che suggerisce a un cliente quale tipo di caffè ordinare. Gli utenti e le relative preferenze sono archiviati in un set di dati degli utenti. Sono disponibili anche le informazioni sul caffè, archiviate in un apposito set di dati.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 7c0dc40ee2d748b1f48c3254a3e3a6e197069c08
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515179"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Esercitazione: Usare Personalizza esperienze in Azure Notebooks

Questa esercitazione esegue un ciclo di Personalizza esperienze in un notebook di Azure, illustrandone il ciclo di vita completo. 

Il ciclo suggerisce quale tipo di caffè dovrebbe essere ordinato da un cliente. Gli utenti e le relative preferenze sono archiviati in un set di dati degli utenti. Le informazioni relative al caffè vengono archiviate in un apposito set di dati.

## <a name="users-and-coffee"></a>Utenti e caffè

Il notebook seleziona a caso dal set di dati un utente, un'ora del giorno e un tipo di condizione meteo. Ecco un riepilogo delle informazioni utente:

|Clienti - funzionalità di contesto|Ora del giorno|Tipo di condizione meteo|
|--|--|--|
|Alice<br>Bob<br>Cathy<br>Dave|Morning<br>Afternoon<br>Evening|Sunny<br>Rainy<br>Snowy| 

Per consentire a Personalizza esperienze di apprendere, nel tempo, la corretta selezione del caffè per ogni persona, il _sistema_ conosce anche i dettagli sul caffè.

|Caffè - funzionalità di azione|Tipi di temperatura|Luogo di origine|Tipo di tostatura|Biologico|
|--|--|--|--|--|
|Cappacino|Accesso frequente|Kenya|Dark (Scuro)|Organic|
|Cold brew|Cold|Brasile|Light|Organic|
|Iced mocha|Cold|Etiopia|Light|Not organic|
|Latte|Accesso frequente|Brasile|Dark (Scuro)|Not organic|


Lo **scopo** del ciclo di Personalizza esperienze è trovare la corrispondenza migliore tra gli utenti e il caffè il più spesso possibile. 

Il codice di questa esercitazione è disponibile nel [repository GitHub di esempi di Personalizza esperienze](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook).

## <a name="how-the-simulation-works"></a>Funzionamento della simulazione

All'inizio dell'esecuzione del sistema la percentuale di successo dei suggerimenti di Personalizza esperienze è compresa appena tra il 20% e il 30% (indicata dal punteggio di ricompensa 1). Dopo alcune richieste, il sistema migliora.

Dopo le prime 10.000 richieste, eseguire una valutazione offline, per consentire a Personalizza esperienze di esaminare i dati e suggerire un criterio di apprendimento migliore. Applicare i nuovi criteri di apprendimento ed eseguire di nuovo il notebook con 2.000 richieste. Le prestazioni del ciclo miglioreranno.

## <a name="rank-and-reward-calls"></a>Chiamate di classificazione e ricompensa

Per ognuna delle migliaia di chiamate al servizio Personalizza esperienze il notebook di Azure Invia la richiesta di **classificazione** all'API REST:

* ID univoco per l'evento di classificazione/richiesta
* Contesto - scelta casuale di utente, meteo e ora del giorno - simulazione di un utente in un sito Web o in un dispositivo mobile
* Funzionalità - _tutti_ i dati sul caffè, da cui Personalizza esperienze trae un suggerimento

Il sistema riceve la classificazione delle scelte di caffè, quindi confronta la stima con la scelta nota dell'utente per la stessa ora del giorno e la stessa condizione meteo. Se la scelta nota è uguale a quella stimata, viene restituita a Personalizza esperienze la **ricompensa** 1. In caso contrario, la ricompensa è 0. 

> [!Note]
> Questa è una simulazione, quindi l'algoritmo per la ricompensa è semplice. In uno scenario reale l'algoritmo dovrebbe usare la logica di business, possibilmente con pesi per diversi aspetti dell'esperienza del cliente, per determinare il punteggio di ricompensa. 


## <a name="prerequisites"></a>Prerequisiti

* Un account [Azure Notebooks](https://notebooks.azure.com/). 
* Una [risorsa di Personalizza esperienze](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). 
    * Se la risorsa di Personalizza esperienze è già stata usata, assicurarsi di [cancellare i dati](how-to-settings.md#clear-data-for-your-learning-loop) relativi alla risorsa nel portale di Azure. 
* Caricare tutti i file per [questo esempio](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) in un progetto di Azure Notebooks. 

Descrizioni dei file:

* [Personalizer.ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) è il notebook Jupyter per questa esercitazione.
* Il [set di dati per gli utenti](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) è archiviato in un oggetto JSON.
* Il [set di dati per il caffè](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) è archiviato in un oggetto JSON. 
* [JSON di richiesta di esempio](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) è il formato previsto per una richiesta POST all'API di classificazione.

## <a name="configure-personalizer-resource"></a>Configurare la risorsa di Personalizza esperienze

Nel portale di Azure configurare la [risorsa di Personalizza esperienze](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)con la **frequenza di aggiornamento del modello** impostata su 15 secondi e un **tempo di attesa per la ricompensa** di 15 secondi. Queste impostazioni sono disponibili nella pagina **[Impostazioni](how-to-settings.md#configure-service-settings-in-the-azure-portal)** . 

|Impostazione|Valore|
|--|--|
|Frequenza di aggiornamento del modello|5 secondi|
|Tempo di attesa per la ricompensa|5 secondi|

Questi valori hanno una durata molto breve al fine di mostrare i cambiamenti in questa esercitazione. Non devono essere usati in uno scenario di produzione senza prima verificare che raggiungano l'obiettivo prefissato con il ciclo di Personalizza esperienze. 

## <a name="set-up-the-azure-notebook"></a>Configurare il notebook di Azure

1. Cambiare il Kernel in `Python 3.6`. 
1. Aprire il file `Personalizer.ipynb` .

## <a name="run-notebook-cells"></a>Eseguire le celle del notebook

Eseguire ogni cella eseguibile e attendere che venga restituito un risultato. Questo avviene quando le parentesi quadre accanto alla cella visualizzano un numero al posto di un `*`. Le sezioni seguenti illustrano il funzionamento di ogni cella a livello di codice e cosa aspettarsi come output. 

### <a name="include-the-python-modules"></a>Includere i moduli Python

Includere i moduli Python necessari. La cella non ha output.

```python
import json
import matplotlib.pyplot as plt
import random 
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Impostare la chiave e il nome della risorsa di Personalizza esperienze

Nel portale di Azure trovare la chiave e l'endpoint nella pagina **Avvio rapido** della risorsa di Personalizza esperienze. Sostituire il valore di `<your-resource-name>` con il nome della risorsa di Personalizza esperienze. Sostituire il valore di `<your-resource-key>` con la chiave di Personalizza esperienze. 

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Stampare la data e l'ora correnti
Usare questa funzione per prendere nota dell'ora di inizio e di fine della funzione iterativa (iterazioni).

Queste celle non hanno output. La funzione non restituisce la data e l'ora correnti quando viene chiamata.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Ottenere l'ora dell'ultimo aggiornamento del modello

Quando la funzione `get_last_updated` viene chiamata, visualizza l'ultima data e ora in cui il modello è stato aggiornato. 

Queste celle non hanno output. Quando viene chiamata, la funzione restituisce la data dell'ultimo training del modello.

La funzione usa un'API REST GET per [ottenere le proprietà del modello](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties). 

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):
    
    print('-----checking model')
    
    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)
    
    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])
    
    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>Ottenere la configurazione dei criteri e del servizio

Verificare lo stato del servizio con queste due chiamate REST.

Queste celle non hanno output. Quando viene chiamata, la funzione restituisce le impostazioni del servizio.

```python
def get_service_settings():
    
    print('-----checking service settings')
    
    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)
    
    print(response)
    print(response.json())
    
    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)
    
    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>Costruire gli URL e leggere i file di dati JSON

Questa cella 

* crea gli URL usati nelle chiamate REST 
* imposta l'intestazione di sicurezza usando la chiave della risorsa di Personalizza esperienze 
* imposta il valore di inizializzazione casuale per l'ID evento di classificazione
* legge i file di dati JSON
* chiama il metodo `get_last_updated` - i criteri di apprendimento sono stati rimossi nell'output dell'esempio
* chiama il metodo `get_service_settings`

La cella contiene l'output della chiamata alle funzioni `get_last_updated` e `get_service_settings`.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None 
rankactionsjsonobj = None 
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())
    
with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())  
    
get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Verificare che i valori `rewardWaitTime` e `modelExportFrequency` dell'output siano entrambi impostati su 15 secondi. 
    
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>Risoluzione dei problemi relativi alla prima chiamata REST

Questa cella precedente è la prima cella che effettua una chiamata a Personalizza esperienze. Verificare che il codice di stato REST nell'output sia `<Response [200]>`. Se si riceve un errore, ad esempio il 404, ma si è certi che la chiave e il nome della risorsa siano corretti, ricaricare il notebook.

Verificare che il conteggio sia 4 sia per il caffè che per gli utenti. Se si verifica un errore, controllare di aver caricato tutti e 3 i file JSON. 

### <a name="set-up-metric-chart-in-azure-portal"></a>Configurare il grafico delle metriche nel portale di Azure

Più avanti in questa esercitazione, il processo a esecuzione prolungata di 10.000 richieste è visibile dal browser con una casella di testo di aggiornamento. Può risultare più semplice da visualizzare in un grafico o come somma totale, al termine del processo a esecuzione prolungata. Per visualizzare queste informazioni, usare le metriche fornite con la risorsa. È possibile creare il grafico ora che è stata completata una richiesta al servizio, quindi aggiornare periodicamente il grafico mentre è in corso il processo a esecuzione prolungata.

1. Nel portale di Azure selezionare la risorsa di Personalizza esperienze.
1. Nel riquadro di spostamento della risorsa selezionare **Metriche** sotto Monitoraggio. 
1. Nel grafico selezionare **Aggiungi metrica**.
1. La risorsa e lo spazio dei nomi della metrica sono già impostati. È sufficiente selezionare la metrica **Chiamate riuscite** e l'aggregazione **Somma**.
1. Cambiare il filtro temporale in Ultime 4 ore.

    ![Configurare il grafico delle metriche nel portale di Azure, aggiungendo la metrica per le chiamate riuscite nelle ultime 4 ore.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    Si dovrebbero vedere tre chiamate riuscite nel grafico. 

### <a name="generate-a-unique-event-id"></a>Generare un ID evento univoco

Questa funzione genera un ID univoco per ogni chiamata di classificazione. L'ID viene usato per identificare le informazioni sulle chiamate di classificazione e ricompensa. Questo valore può provenire da un processo aziendale, ad esempio un ID di visualizzazione Web o un ID di transazione.

La cella non ha output. Quando viene chiamata, la funzione restituisce l'ID univoco.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Ottenere un utente, una condizione meteo e un'ora del giorno casuali

Questa funzione seleziona un utente, una condizione meteo e un'ora del giorno casuali, quindi li aggiunge all'oggetto JSON da inviare alla richiesta di classificazione.

La cella non ha output. Quando la funzione viene chiamata, restituisce il nome dell'utente casuale, la condizione meteo casuale e l'ora del giorno casuale.

Ecco l'elenco dei 4 utenti e delle loro preferenze (per brevità sono visualizzate solo alcune preferenze): 

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):   
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Aggiungere tutti i dati relativi al caffè

Questa funzione aggiunge l'intero elenco di caffè all'oggetto JSON da inviare alla richiesta di classificazione. 

La cella non ha output. La funzione cambia il valore di `rankjsonobj` quando viene chiamata.


Ecco l'esempio delle caratteristiche di un singolo tipo di caffè: 

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"
        
    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Confrontare la stima con la preferenza nota dell'utente

Questa funzione viene chiamata dopo la chiamata dell'API di classificazione per ogni iterazione.

Confronta la preferenza dell'utente per il caffè, in base alla condizione meteo e all'ora del giorno, con il suggerimento di Personalizza esperienze per l'utente con questi stessi filtri. Se il suggerimento corrisponde, viene restituito il punteggio 1, altrimenti il punteggio è 0. La cella non ha output. Quando viene chiamata, la funzione restituisce il punteggio.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1 
    return 0
``` 

### <a name="loop-through-calls-to-rank-and-reward"></a>Riprodurre a ciclo continuo le chiamate alle API di classificazione e ricompensa

La cella successiva è il lavoro _principale_ del notebook: ottiene un utente casuale e l'elenco dei caffè e invia entrambi all'API di classificazione. Confronta la stima con le preferenze note dell'utente, quindi restituisce la ricompensa al servizio Personalizza esperienze. 

Il ciclo viene eseguito per `num_requests` volte. Personalizza esperienze ha bisogno di qualche migliaia di chiamate per consentire alle API di classificazione e ricompensa di creare un modello. 

Di seguito è riportato un esempio di codice JSON inviato all'API di classificazione. Per brevità, l'elenco dei caffè non è completo. L'intero codice JSON per il caffè è disponibile in `coffee.json`.

JSON inviato all'API di classificazione:

```json
{ 
   'contextFeatures':[ 
      { 
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[ 
      { 
         'id':'Cappucino',
         'features':[ 
            { 
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[ 

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

Risposta JSON dall'API di classificazione:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ], 
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2', 
    'rewardActionId': 'Latte'
}
```

Infine, ogni ciclo mostra la selezione casuale di utente, meteo e ora del giorno e la ricompensa determinata. Una ricompensa pari a 1 indica che la risorsa di Personalizza esperienze ha selezionato il tipo di caffè corretto per l'utente, la condizione meteo e l'ora del giorno specificati.

```console
1 Alice Rainy Morning Latte 1
```

La funzione usa:

* Classificazione: un'API REST POST per [ottenere la classificazione](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank). 
* Ricompensa: un'API REST POST per [segnalare la ricompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1
    
    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']
    
    
    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat) 

        # show JSON to send to Rank
        print('To: ', jsonFormat)    

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction 
        print ('From: ',response.json())    

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service 
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified) 

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1
        
    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>Eseguire il ciclo per 10.000 iterazioni
Eseguire il ciclo di Personalizza esperienze per 10.000 iterazioni. Si tratta di un evento a esecuzione prolungata. Non chiudere il browser in cui è in esecuzione il notebook. Aggiornare periodicamente il grafico delle metriche nel portale di Azure per visualizzare le chiamate totali al servizio. Quando si raggiungono circa 20.000 chiamate, una chiamata di classificazione e ricompensa per ogni iterazione del ciclo, le iterazioni sono terminate. 

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Organizzare i risultati in un grafico per visualizzare i miglioramenti 

Creare un grafico da `count` e `rewards`.

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Eseguire il grafico per 10.000 richieste di classificazione

Eseguire la funzione `createChart`.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>Lettura del grafico

Questo grafico mostra l'esito positivo del modello per i criteri di apprendimento predefiniti correnti. 

![Questo grafico mostra l'esito positivo dei criteri di apprendimento correnti per la durata del test.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


L'obiettivo ideale è che entro la fine del test il ciclo abbia ottenuto in media una percentuale di successo vicina al 100% meno l'esplorazione. L'impostazione predefinita dell'esplorazione è 20%. 

`100-20=80`

Questa impostazione di esplorazione si trova nella pagina **Impostazioni** della risorsa di Personalizza esperienze nel portale di Azure. 

Per trovare un criterio di apprendimento migliore, in base ai dati dell'API di classificazione, eseguire una [valutazione offline](how-to-offline-evaluation.md) del ciclo di personalizzazione nel portale.

## <a name="run-an-offline-evaluation"></a>Eseguire una valutazione offline

1. Nel portale di Azure aprire la pagina **Valutazioni** della risorsa di Personalizza esperienze.
1. Selezionare **Crea valutazione**.
1. Immettere i dati necessari per il nome della valutazione e l'intervallo di date per la valutazione del ciclo. L'intervallo di date deve includere solo i giorni sui quali ci si vuole concentrare per la valutazione. 
    ![Nel portale di Azure aprire la pagina Valutazioni della risorsa di Personalizza esperienze. Selezionare Crea valutazione. Immettere il nome e l'intervallo di date della valutazione.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Lo scopo dell'esecuzione di questa valutazione offline è determinare se esistono criteri di apprendimento migliori per le funzionalità e le azioni usate in questo ciclo. Per trovare questi criteri di apprendimento migliori, assicurarsi che l'opzione **Optimization policy** (Criteri di ottimizzazione) sia attivata.

1. Scegliere **OK** per iniziare la valutazione. 
1. Questa pagina **Valutazioni** mostra la nuova valutazione e il suo stato corrente. Il tempo necessario per l'esecuzione della valutazione dipende dalla quantità di dati disponibili. È possibile tornare a questa pagina dopo alcuni minuti per visualizzare i risultati. 
1. Al termine della valutazione, selezionare la valutazione e quindi **Confronto tra vari criteri di apprendimento**. Vengono visualizzati i criteri di apprendimento disponibili e il modo in cui si comporterebbero con i dati. 
1. Selezionare il criterio di apprendimento elencato per primo nella tabella e quindi selezionare **Applica**. Viene così applicato il criterio di apprendimento _migliore_ al modello e viene ripetuto il training. 

## <a name="change-update-model-frequency-to-5-minutes"></a>Impostare la frequenza di aggiornamento del modello su 5 minuti

1. Nel portale di Azure, sempre nella risorsa di Personalizza esperienze, selezionare la pagina **Impostazioni**. 
1. Impostare **Frequenza di aggiornamento del modello**  e **Tempo di attesa per la ricompensa**  su 5 minuti e selezionare **Salva**.

Altre informazioni sulle opzioni [Tempo di attesa per la ricompensa ](concept-rewards.md#reward-wait-time) e [Frequenza di aggiornamento del modello](how-to-settings.md#model-update-frequency).

```python
#Verify new learning policy and times
get_service_settings()
```

Verificare che i valori `rewardWaitTime` e `modelExportFrequency` dell'output siano entrambi impostati su 5 minuti. 
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Convalidare i nuovi criteri di apprendimento 

Tornare al notebook di Azure ed eseguire lo stesso ciclo, ma solo per 2.000 iterazioni. Aggiornare periodicamente il grafico delle metriche nel portale di Azure per visualizzare le chiamate totali al servizio. Quando si raggiungono circa 4.000 chiamate, una chiamata di classificazione e ricompensa per ogni iterazione del ciclo, le iterazioni sono terminate. 

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Eseguire il grafico per 2.000 richieste di classificazione

Eseguire la funzione `createChart`.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>Esaminare il secondo grafico

Il secondo grafico dovrebbe mostrare un visibile aumento dell'allineamento delle stime di classificazione alle preferenze dell'utente. 

![Il secondo grafico dovrebbe mostrare un visibile aumento dell'allineamento delle stime di classificazione alle preferenze dell'utente.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di continuare la serie di esercitazioni, pulire le risorse seguenti:

* Eliminare il progetto di Azure Notebooks. 
* Eliminare la risorsa di Personalizza esperienze. 

## <a name="next-steps"></a>Passaggi successivi

Il [notebook Jupyter e i file di dati](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) usati in questo esempio sono disponibili nel repository GitHub per Personalizza esperienze. 

