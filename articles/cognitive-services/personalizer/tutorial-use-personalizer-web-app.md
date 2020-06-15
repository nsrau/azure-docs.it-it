---
title: Usare app Web-personalizzatore
description: Personalizzare un'app Web C# .NET con un ciclo di personalizzazione per fornire il contenuto corretto a un utente in base alle azioni (con funzionalità) e alle funzionalità di contesto.
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.author: diberry
ms.openlocfilehash: 9def69a1540e81b99723c16ad34ba522d1737c7f
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84713917"
---
# <a name="add-personalizer-to-a-net-web-app"></a>Aggiungere il Personalizzatore a un'app Web .NET

Personalizzare un'app Web C# .NET con un ciclo di personalizzazione per fornire il contenuto corretto a un utente in base alle azioni (con funzionalità) e alle funzionalità di contesto.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Configurare la chiave e l'endpoint di personalizzazione
> * Raccogli funzionalità
> * Chiamare le API Rank e Reward
> * Visualizza l'azione top, designata come _rewardActionId_



## <a name="select-the-best-content-for-a-web-app"></a>Selezionare il contenuto migliore per un'app Web

Un'app Web deve usare il Personalizzatore quando è presente un elenco di _azioni_ (un tipo di contenuto) nella pagina Web che deve essere personalizzato per un singolo elemento principale (rewardActionId) da visualizzare. Esempi di elenchi di azioni includono articoli di notizie, posizioni di selezione dei pulsanti e scelte di parole per i nomi di prodotto.

Si invia l'elenco di azioni, insieme alle funzionalità del contesto, al ciclo di personalizzazione. Personalizzatore seleziona la singola azione migliore, quindi l'app Web visualizza tale azione.

In questa esercitazione, le azioni sono tipi di cibo:

* pasta
* gelato
* succo
* insalata
* popcorn
* Coffee
* soup

Per semplificare la conoscenza delle azioni da personalizzare, inviare le_azioni _ con_ le funzionalità e le _funzionalità di contesto_ con ogni richiesta dell'API Rank.

Una **funzionalità** del modello è costituita dalle informazioni sull'azione o sul contesto che possono essere aggregate (raggruppate) tra i membri della base utente dell'app Web. Una funzionalità _non è_ specifica singolarmente, ad esempio un ID utente, oppure è molto specifica (ad esempio l'ora esatta del giorno).

### <a name="actions-with-features"></a>Azioni con funzionalità

Ogni azione (elemento contenuto) dispone di funzionalità che consentono di distinguere l'elemento del cibo.

Le funzionalità non sono configurate come parte della configurazione del ciclo nel portale di Azure. Vengono invece inviati come oggetto JSON con ogni chiamata API di rango. In questo modo, le azioni e le relative funzionalità possono aumentare, modificare e compattarsi nel tempo, consentendo al Personalizzatore di seguire le tendenze.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>Caratteristiche del contesto

Le funzionalità del contesto consentono al personale di comprendere il contesto delle azioni. Il contesto per questa applicazione di esempio include:

* ora del giorno, mattina, pomeriggio, sera, notte
* preferenza dell'utente per i gusti, dolce, amara, acida o gustosa
* contesto del browser: agente utente, posizione geografica, referrer

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>In che modo l'app Web usa il Personalizzatore?

L'app Web usa il Personalizzatore per selezionare l'azione migliore dall'elenco di scelte alimentari. Questa operazione viene eseguita inviando le informazioni seguenti con ogni chiamata all'API Rank:
* **azioni** con funzionalità quali `taste` e`spiceLevel`
* funzionalità del **contesto** quali `time` il giorno, `taste` le preferenze dell'utente e le informazioni sull'agente utente del browser e le funzionalità del contesto
* **azioni da escludere** , ad esempio Juice
* **ID**evento, che è diverso per ogni chiamata all'API Rank.

## <a name="personalizer-model-features-in-a-web-app"></a>Funzionalità del modello di personalizzatore in un'app Web

Il Personalizzatore necessita di funzionalità per le azioni (contenuto) e il contesto corrente (utente e ambiente). Le funzionalità vengono utilizzate per allineare le azioni al contesto corrente nel modello. Il modello è una rappresentazione delle informazioni precedenti su azioni, contesto e funzionalità che consentono al personale IT di prendere decisioni consapevoli.

Il modello, incluse le funzionalità, viene aggiornato in base a una pianificazione in base all'impostazione della **frequenza di aggiornamento del modello** nel portale di Azure.

> [!CAUTION]
> Le funzionalità di questa applicazione hanno lo scopo di illustrare le funzionalità e i valori delle funzionalità, ma non necessariamente le funzionalità migliori da usare in un'app Web.

### <a name="plan-for-features-and-their-values"></a>Pianificare le funzionalità e i relativi valori

È necessario selezionare le funzionalità con la stessa pianificazione e progettazione che si applicano a qualsiasi schema o modello nell'architettura tecnica. I valori della funzionalità possono essere impostati con la logica di business o sistemi di terze parti. I valori delle funzionalità non devono essere così specifici che non si applicano a un gruppo o a una classe di funzionalità.

### <a name="generalize-feature-values"></a>Generalizzare i valori delle funzionalità

#### <a name="generalize-into-categories"></a>Generalizza in categorie

Questa app usa `time` come funzionalità ma raggruppa il tempo in categorie come `morning` ,, `afternoon` `evening` e `night` . Questo è un esempio di utilizzo delle informazioni di tempo ma non in modo estremamente specifico, ad esempio `10:05:01 UTC+2` .

#### <a name="generalize-into-parts"></a>Generalizza in parti

Questa app usa le funzionalità di richiesta HTTP dal browser. Questa operazione inizia con una stringa molto specifica con tutti i dati, ad esempio:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

La libreria di classi **HttpRequestFeatures** generalizza questa stringa in un oggetto **userAgentInfo** con singoli valori. Tutti i valori troppo specifici vengono impostati su una stringa vuota. Quando vengono inviate le funzionalità di contesto per la richiesta, il formato JSON è il seguente:

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>Uso dell'app Web di esempio

Per eseguire l'app, è necessario che sia installata l'app Web di esempio basata su browser (tutto il codice).

Installare il software seguente:

* [.Net core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.1) -il server back-end di esempio USA .NET Core
* [Node.js](https://nodejs.org/) : il client/front-end dipende da questa applicazione
* [Visual studio 2019](https://visualstudio.microsoft.com/vs/)o [interfaccia della riga di comando di .NET Core](https://docs.microsoft.com/dotnet/core/tools/) : usare l'ambiente di sviluppo di Visual studio 2019 o il interfaccia della riga di comando di .NET Core per compilare ed eseguire l'app

### <a name="set-up-the-sample"></a>Configurare l'esempio
1. Clonare il repository degli esempi di personalizzazione di Azure.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Passare a _Samples/HttpRequestFeatures_ per aprire la soluzione `HttpRequestFeaturesExample.sln` .

    Se richiesto, consentire a Visual Studio di aggiornare il pacchetto .NET per la personalizzazione.

### <a name="set-up-azure-personalizer-service"></a>Configurare il servizio di personalizzazione di Azure

1. [Creare una risorsa di personalizzazione](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) nel portale di Azure.

1. Nella portale di Azure individuare `Endpoint` e o (funzionerà `Key1` `Key2` ) nella scheda **chiavi ed endpoint** . Si tratta del `PersonalizerServiceEndpoint` e del `PersonalizerApiKey` .
1. Compilare il `PersonalizerServiceEndpoint` in **appsettings.js**.
1. Configurare `PersonalizerApiKey` come segreti dell' [app](https://docs.microsoft.com/aspnet/core/security/app-secrets) in uno dei modi seguenti:

    * Se si usa il interfaccia della riga di comando di .NET Core, è possibile usare il `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` comando.
    * Se si usa Visual Studio, è possibile fare clic con il pulsante destro del mouse sul progetto e selezionare l'opzione di menu **Gestisci segreti utente** per configurare le chiavi di personalizzazione. In questo modo, Visual Studio apre un `secrets.json` file in cui è possibile aggiungere le chiavi come segue:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>Eseguire l'esempio

Compilare ed eseguire HttpRequestFeaturesExample con uno dei metodi seguenti:

* Visual Studio 2019: premere **F5**
* Interfaccia della riga di comando di .NET Core: `dotnet build` quindi`dotnet run`

Tramite un Web browser è possibile inviare una richiesta di rango e una richiesta di ricompensa e visualizzarne le risposte, nonché le funzionalità di richiesta http estratte dall'ambiente.

> [!div class="mx-imgBorder"]
> ![Compilare ed eseguire il progetto HTTPRequestFeaturesExample. Viene visualizzata una finestra del browser per visualizzare l'applicazione a pagina singola.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Dimostrazione del ciclo di personalizzazione

1. Selezionare il pulsante **generate New Rank Request (genera nuovo rango** ) per creare un nuovo oggetto JSON per la chiamata all'API Rank. In questo modo vengono create le azioni (con funzionalità) e le funzionalità di contesto e vengono visualizzati i valori in modo da visualizzare l'aspetto di JSON.

    Per la tua applicazione futura, la generazione di azioni e funzionalità può verificarsi nel client, nel server, in una combinazione delle due o con chiamate ad altri servizi.

1. Selezionare **Invia la richiesta di rango** per inviare l'oggetto JSON al server. Il server chiama l'API di rango della personalizzazione. Il server riceve la risposta e restituisce l'azione di classificazione più alta al client da visualizzare.

1. Impostare il valore Reward, quindi selezionare il pulsante **Send Reward request** . Se non si modifica il valore Reward, l'applicazione client invia sempre il valore di `1` a personalizzar.

    > [!div class="mx-imgBorder"]
    > ![Compilare ed eseguire il progetto HTTPRequestFeaturesExample. Viene visualizzata una finestra del browser per visualizzare l'applicazione a pagina singola.](./media/tutorial-web-app/reward-score-api-call.png)

    Per la tua applicazione futura, la generazione del Punteggio di ricompensa può verificarsi dopo aver raccolto le informazioni dal comportamento dell'utente sul client, insieme alla logica di business nel server.

## <a name="understand-the-sample-web-app"></a>Informazioni sull'app Web di esempio

L'app Web di esempio dispone di un server **C# .NET** , che gestisce la raccolta di funzionalità e l'invio e la ricezione di chiamate http all'endpoint di personalizzazione.

L'app Web di esempio usa un' **applicazione client front-end Knockout** per acquisire funzionalità ed elaborare le azioni dell'interfaccia utente, ad esempio fare clic sui pulsanti e inviare dati al server .NET.

Le sezioni seguenti illustrano le parti del server e del client che uno sviluppatore deve comprendere per usare la personalizzazione.

## <a name="rank-api-client-application-sends-context-to-server"></a>Rank API: l'applicazione client invia il contesto al server

L'applicazione client raccoglie l' _agente utente_del browser dell'utente.

> [!div class="mx-imgBorder"]
> ![Compilare ed eseguire il progetto HTTPRequestFeaturesExample. Viene visualizzata una finestra del browser per visualizzare l'applicazione a pagina singola.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>Rank API: server application calls

Si tratta di un'app Web .NET tipica con un'applicazione client. la maggior parte del codice della piastra calda è disponibile per l'utente. Qualsiasi codice non specifico per la personalizzazione viene rimosso dai frammenti di codice seguenti, in modo da potersi concentrare sul codice specifico del personalizzatore.

### <a name="create-personalizer-client"></a>Crea client di personalizzazione

Nel **Startup.cs**del server, l'endpoint e la chiave di personalizzazione vengono usati per creare il client di personalizzazione. L'applicazione client non deve comunicare con il Personalizzatore in questa app, invece di fare affidamento sul server per effettuare le chiamate dell'SDK.

Il codice di avvio .NET del server Web è:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>Selezionare l'azione migliore

Nel **PersonalizerController.cs**del server, l'API del server **GenerateRank** riepiloga la preparazione per chiamare l'API Rank

* Crea nuovo `eventId` per la chiamata di rango
* Ottenere l'elenco di azioni
* Ottenere l'elenco delle funzionalità dall'utente e creare le funzionalità del contesto
* Facoltativamente, impostare eventuali azioni escluse
* Call Rank API, risultati restituiti al client

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

Il codice JSON inviato al personalizzatore, che contiene entrambe le azioni (con funzionalità) e le funzionalità del contesto corrente, è simile al seguente:

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
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
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Restituisce il rewardActionId di personalizzazione al client

L'API Rank restituisce il **rewardActionId** di azione migliore selezionato al server.

Visualizza l'azione restituita in **rewardActionId**.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>Il client Visualizza l'azione rewardActionId

In questa esercitazione `rewardActionId` viene visualizzato il valore.

Nell'applicazione futura, può trattarsi di un testo esatto, di un pulsante o di una sezione della pagina Web evidenziata. L'elenco viene restituito per qualsiasi post-analisi dei punteggi, non per un ordinamento del contenuto. `rewardActionId`Viene visualizzato solo il contenuto.

## <a name="reward-api-collect-information-for-reward"></a>API Reward: Raccogli informazioni per la ricompensa

Il [Punteggio di ricompensa](concept-rewards.md) dovrebbe essere pianificato con attenzione, proprio come le funzionalità sono pianificate. Il Punteggio di ricompensa in genere deve essere un valore compreso tra 0 e 1. Il valore _può_ essere calcolato parzialmente nell'applicazione client, in base ai comportamenti degli utenti e parzialmente sul server, in base alla logica di business e agli obiettivi.

Se il server non chiama l'API Reward entro il **tempo di attesa della ricompensa** configurato nel portale di Azure per la risorsa di personalizzazione, viene usata la **ricompensa predefinita** (configurata anche nel portale di Azure) per tale evento.

In questa applicazione di esempio è possibile selezionare un valore per vedere come la ricompensa influisca sulle selezioni.

## <a name="additional-ways-to-learn-from-this-sample"></a>Ulteriori modi per apprendere da questo esempio

Nell'esempio vengono utilizzati diversi eventi basati sul tempo configurati nella portale di Azure per la risorsa di personalizzazione. Giocare con questi valori, quindi tornare a questa app Web di esempio per verificare il modo in cui le modifiche influiscano sul rango e sulle chiamate di ricompensa:

* Reward wait time (Tempo di attesa ricompense)
* Model update frequency (Frequenza di aggiornamento del modello)

Altre impostazioni da riprodurre includono:
* Default reward (Ricompensa predefinita)
* Percentuale di esplorazione


## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa esercitazione, pulire le risorse seguenti:

* Eliminare la directory del progetto di esempio.
* Elimina la risorsa di personalizzazione: è possibile pensare a una risorsa di personalizzazione come dedicata alle azioni e riutilizzare solo il contesto della risorsa se si usano ancora gli alimenti come dominio soggetto azioni.


## <a name="next-steps"></a>Passaggi successivi
* [Funzionamento di Personalizza esperienze](how-personalizer-works.md)
* [Funzionalità](concepts-features.md): informazioni sui concetti relativi alle funzionalità usando con azioni e contesto
* [Vantaggi](concept-rewards.md): informazioni sul calcolo dei premi