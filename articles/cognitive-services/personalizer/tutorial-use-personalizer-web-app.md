---
title: Usare app Web - Personalizza esperienze
description: Personalizzare un'app Web .NET C# con un ciclo di Personalizza esperienze per fornire il contenuto corretto a un utente in base alle azioni (con caratteristiche) e alle caratteristiche del contesto.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e06d191573219df44631f6ffaee86f895166de57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777259"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>Esercitazione: Usare Personalizza esperienze in un'app Web .NET

Personalizzare un'app Web .NET C# con un ciclo di Personalizza esperienze per fornire il contenuto corretto a un utente in base alle azioni (con caratteristiche) e alle caratteristiche del contesto.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Configurare la chiave e l'endpoint di Personalizza esperienze
> * Raccogliere funzionalità
> * Chiamare le API Classificazione e Ricompensa
> * Visualizzare l'azione principale, designata come _rewardActionId_



## <a name="select-the-best-content-for-a-web-app"></a>Selezionare il contenuto migliore per un'app Web

Un'app Web dovrebbe usare Personalizza esperienze quando è presente un elenco di _azioni_ (un tipo di contenuto) nella pagina Web che deve essere personalizzato in base a un singolo elemento principale (rewardActionId) da visualizzare. Gli esempi di elenchi di azioni includono articoli di notizie, posizioni di selezione dei pulsanti e scelte di parole per i nomi di prodotti.

Si invia l'elenco di azioni, insieme alle caratteristiche del contesto, al ciclo di Personalizza esperienze. Personalizza esperienze seleziona la singola azione migliore, che verrà visualizzata nell'app Web.

In questa esercitazione le azioni sono tipi di cibo:

* pasta
* ice cream
* juice
* salad
* popcorn
* coffee
* soup

Per consentire a Personalizza esperienze di acquisire informazioni sulle azioni, inviare _azioni con caratteristiche_ e _caratteristiche del contesto_ con ogni richiesta all'API Classificazione.

Una **caratteristica** del modello è costituita da informazioni sull'azione o sul contesto che possono essere aggregate (raggruppate) tra i membri della base di utenti dell'app Web. Una caratteristica _non è_ specifica per un individuo, come un ID utente, né altamente specifica, come l'ora esatta del giorno.

### <a name="actions-with-features"></a>Azioni con caratteristiche

Ogni azione (contenuto) include caratteristiche per distinguere l'alimento.

Le caratteristiche non vengono configurate come parte della configurazione del ciclo nel portale di Azure. Vengono invece inviate come oggetto JSON con ogni chiamata all'API Classificazione. In questo modo, le azioni e le relative caratteristiche possono aumentare, cambiare e diminuire con grande flessibilità, consentendo a Personalizza esperienze di seguire le tendenze.

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

Le caratteristiche del contesto consentono a Personalizza esperienze di capire il contesto delle azioni. Il contesto per questa applicazione di esempio include:

* ora del giorno: morning, afternoon, evening, night
* preferenza di gusto dell'utente: salty, sweet, bitter, sour, savory
* contesto del browser: user agent, geographical location, referrer

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

## <a name="how-does-the-web-app-use-personalizer"></a>In che modo l'app Web usa Personalizza esperienze?

L'app Web usa Personalizza esperienze per selezionare l'azione migliore nell'elenco di scelte alimentari. A questo scopo invia le informazioni seguenti con ogni chiamata all'API Classificazione:
* **Azioni**, con relative caratteristiche, ad esempio `taste` e `spiceLevel`
* Caratteristiche del **contesto**, ad esempio `time` del giorno, preferenza di `taste` dell'utente e informazioni sull'agente utente del browser
* **Azioni da escludere**, ad esempio juice
* **ID evento**, che è diverso per ogni chiamata all'API Classificazione.

## <a name="personalizer-model-features-in-a-web-app"></a>Caratteristiche del modello di Personalizza esperienze in un'app Web

Personalizza esperienze richiede le caratteristiche per le azioni (contenuto) e il contesto corrente (utente e ambiente). Le caratteristiche vengono usate per allineare le azioni al contesto corrente nel modello. Il modello è una rappresentazione delle conoscenze passate di Personalizza esperienze riguardanti azioni, contesto e relative caratteristiche tramite cui il servizio può prendere decisioni informate.

Il modello, incluse le caratteristiche, viene aggiornato in base a una pianificazione definita dall'impostazione di **Frequenza di aggiornamento del modello** nel portale di Azure.

> [!CAUTION]
> In questa applicazione sono illustrate le caratteristiche e i relativi valori, ma non necessariamente quelle migliori da usare in un'app Web.

### <a name="plan-for-features-and-their-values"></a>Pianificare le caratteristiche e i relativi valori

Le caratteristiche devono essere selezionate con la stessa pianificazione e gli stessi criteri che si applicherebbero a qualsiasi schema o modello nell'architettura tecnica. I valori delle caratteristiche possono essere impostati con la logica di business o con sistemi di terze parti. I valori delle caratteristiche non devono essere talmente specifici da non essere applicabili a un gruppo o a una categoria di caratteristiche.

### <a name="generalize-feature-values"></a>Generalizzare i valori delle caratteristiche

#### <a name="generalize-into-categories"></a>Generalizzare in categorie

Questa app usa `time` come caratteristica ma raggruppa il tempo in categorie come `morning`, `afternoon`, `evening` e `night`. Questo è un esempio dell'uso delle informazioni sul tempo, ma in modo non altamente specifico, come ad esempio `10:05:01 UTC+2`.

#### <a name="generalize-into-parts"></a>Generalizzare in parti

Questa app usa le caratteristiche delle richieste HTTP del browser. Inizia con una stringa molto specifica con tutti i dati, ad esempio:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

La libreria di classi **HttpRequestFeatures** generalizza questa stringa in un oggetto **userAgentInfo** con singoli valori. Tutti i valori troppo specifici vengono impostati su una stringa vuota. Quando vengono inviate le caratteristiche del contesto per la richiesta, il formato JSON è il seguente:

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

Per eseguire l'app Web di esempio basata su browser (viene fornito tutto il codice), è necessario che siano installate le applicazioni seguenti.

Installare il software seguente:

* [.NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1): il server back-end di esempio usa .NET Core
* [Node.js](https://nodejs.org/): il client/front-end dipende da questa applicazione
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) o l'[interfaccia della riga di comando di .NET Core](https://docs.microsoft.com/dotnet/core/tools/): usare l'ambiente di sviluppo di Visual Studio 2019 o l'interfaccia della riga di comando di .NET Core per compilare ed eseguire l'app

### <a name="set-up-the-sample"></a>Configurare l'esempio
1. Clonare il repository di esempi di Personalizza esperienze di Azure.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Passare a _samples/HttpRequestFeatures_ per aprire la soluzione `HttpRequestFeaturesExample.sln`.

    Se richiesto, consentire a Visual Studio di aggiornare il pacchetto .NET per Personalizza esperienze.

### <a name="set-up-azure-personalizer-service"></a>Configurare il servizio Personalizza esperienze di Azure

1. Nel portale di Azure [creare una risorsa di Personalizza esperienze](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).

1. Nel portale di Azure trovare `Endpoint` e `Key1` o `Key2` (funzionano entrambi) nella scheda **Chiavi ed endpoint**. Corrispondono a `PersonalizerServiceEndpoint` e `PersonalizerApiKey`.
1. Inserire `PersonalizerServiceEndpoint` in **appsettings.json**.
1. Configurare `PersonalizerApiKey` come [segreti dell'app](https://docs.microsoft.com/aspnet/core/security/app-secrets) in uno dei modi seguenti:

    * Se si usa l'interfaccia della riga di comando di .NET Core, è possibile usare il comando `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"`.
    * Se si usa Visual Studio, è possibile fare clic con il pulsante destro del mouse sul progetto e scegliere l'opzione di menu **Gestisci segreti utente** per configurare le chiavi di Personalizza esperienze. In questo modo, Visual Studio apre un file `secrets.json` in cui è possibile aggiungere le chiavi come indicato di seguito:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>Eseguire l'esempio

Compilare ed eseguire HttpRequestFeaturesExample con uno dei metodi seguenti:

* Visual Studio 2019: premere **F5**
* Interfaccia della riga di comando di .NET Core: `dotnet build` e quindi `dotnet run`

Tramite un Web browser è possibile inviare una richiesta di classificazione e una richiesta di ricompensa e visualizzare le rispettive risposte, oltre alle caratteristiche delle richieste HTTP estratte dalle ambiente.

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra un esempio della funzionalità delle richieste HTTP in un Web browser.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Dimostrazione del ciclo di Personalizza esperienze

1. Selezionare il pulsante **Generate new Rank Request** (Genera nuova richiesta di classificazione) per creare un nuovo oggetto JSON per la chiamata all'API Classificazione. Vengono create le azioni (con caratteristiche) e le caratteristiche del contesto, quindi vengono visualizzati i valori per mostrare l'aspetto dell'oggetto JSON.

    Per le applicazioni future, la generazione di azioni e caratteristiche potrebbe essere eseguita nel client, nel server, in una combinazione di questi due o con chiamate ad altri servizi.

1. Selezionare **Send Rank Request** (Invia richiesta di classificazione) per inviare l'oggetto JSON al server. Il server chiama l'API Classificazione di Personalizza esperienze. Il server riceve la risposta e restituisce l'azione con la classificazione più alta al client per la visualizzazione.

1. Impostare il valore della ricompensa, quindi selezionare il pulsante **Send Reward Request** (Invia richiesta di ricompensa). Se non si cambia il valore della ricompensa, l'applicazione client invia sempre il valore `1` a Personalizza esperienze.

    > [!div class="mx-imgBorder"]
    > ![Screenshot che mostra la sezione the Reward Request (Richiesta di ricompensa).](./media/tutorial-web-app/reward-score-api-call.png)

    Per le applicazioni future, la generazione del punteggio di ricompensa potrebbe essere eseguita dopo la raccolta di informazioni dal comportamento dell'utente nel client, oltre che dalla logica di business nel server.

## <a name="understand-the-sample-web-app"></a>Spiegazione dell'app Web di esempio

L'app Web di esempio include un server **.NET C#** , che gestisce la raccolta di caratteristiche, oltre all'invio e alla ricezione di chiamate HTTP all'endpoint di Personalizza esperienze.

L'app Web di esempio usa un'**applicazione client front-end Knockout** per acquisire le caratteristiche ed elaborare le azioni dell'interfaccia utente, ad esempio il clic sui pulsanti e l'invio di dati al server .NET.

Le sezioni seguenti illustrano le parti di server e client con cui lo sviluppatore deve acquisire familiarità per usare Personalizza esperienze.

## <a name="rank-api-client-application-sends-context-to-server"></a>API Classificazione: l'applicazione client invia il contesto al server

L'applicazione client raccoglie l'_agente utente_ del browser dell'utente.

> [!div class="mx-imgBorder"]
> ![Compilare ed eseguire il progetto HTTPRequestFeaturesExample. Viene visualizzata una finestra del browser con l'applicazione a pagina singola.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>API Classificazione: l'applicazione server chiama Personalizza esperienze

Si tratta di una tipica app Web .NET con un'applicazione client. Viene fornita la maggior parte del codice boilerplate. Qualsiasi codice non specifico di Personalizza esperienze è stato rimosso dai frammenti di codice seguente, in modo da potersi concentrare sul codice specifico di Personalizza esperienze.

### <a name="create-personalizer-client"></a>Creare il client di Personalizza esperienze

Per creare il client di Personalizza esperienze, vengono usati l'endpoint e la chiave di Personalizza esperienze nel file **Startup.cs** del server. L'applicazione client non deve comunicare con Personalizza esperienze in questa app, ma deve invece fare affidamento sul server per effettuare queste chiamate all'SDK.

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

Nel file **PersonalizerController.cs** del server l'API server **GenerateRank** riepiloga la preparazione della chiamata all'API Classificazione

* Creare nuovo oggetto `eventId` per la chiamata a Classificazione
* Ottenere l'elenco di azioni
* Ottenere l'elenco delle funzionalità dall'utente e creare le caratteristiche del contesto
* Facoltativamente, impostare eventuali azioni escluse
* Chiamare l'API Classificazione e restituire i risultati al client

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

Il codice JSON inviato a Personalizza esperienze, contenente sia le azioni (con caratteristiche) sia le caratteristiche del cotesto corrente, avrà l'aspetto seguente:

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

### <a name="return-personalizer-rewardactionid-to-client"></a>Restituire rewardActionId di Personalizza esperienze al client

L'API Classificazione restituisce al server l'azione migliore selezionata in **rewardActionId**.

Visualizzare l'azione restituita in **rewardActionId**.

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

### <a name="client-displays-the-rewardactionid-action"></a>Il client visualizza l'azione rewardActionId

Per questa esercitazione, viene visualizzato il valore`rewardActionId`.

Nelle applicazioni future potrebbe essere un testo esatto, un pulsante o una sezione evidenziata di una pagina Web. L'elenco viene restituito per un'eventuale analisi successiva dei punteggi, non per l'ordinamento del contenuto. Dovrebbe essere visualizzato solo il contenuto di `rewardActionId`.

## <a name="reward-api-collect-information-for-reward"></a>API Ricompensa: raccogliere informazioni per la ricompensa

Il [punteggio di ricompensa](concept-rewards.md) dovrà essere pianificato con cautela, proprio come le caratteristiche. Il punteggio di ricompensa è in genere un valore compreso tra 0 e 1. Il valore _può_ essere calcolato parzialmente nell'applicazione client, in base ai comportamenti degli utenti, e parzialmente nel server, in base alla logica di business e agli obiettivi.

Se il server non chiama l'API Ricompensa entro il periodo indicato in **Tempo di attesa per la ricompensa** configurato nel portale di Azure per la risorsa di Personalizza esperienze, per tale evento verrà usato il valore di **Ricompensa predefinita**, anche questo configurato nel portale di Azure.

In questa applicazione di esempio è possibile selezionare un valore per vedere l'effetto della ricompensa sulle selezioni.

## <a name="additional-ways-to-learn-from-this-sample"></a>Altre informazioni ricavabili da questo esempio

Per la risorsa di Personalizza esperienze, l'esempio usa diversi eventi basati sul tempo configurati nel portale di Azure. Sperimentare con questi valori, quindi tornare in questa app Web di esempio per verificare l'effetto delle modifiche sulle chiamate a Classificazione e a Ricompensa:

* Reward wait time (Tempo di attesa ricompense)
* Model update frequency (Frequenza di aggiornamento del modello)

Altre impostazioni con cui sperimentare includono:
* Default reward (Ricompensa predefinita)
* Percentuale di esplorazione


## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa esercitazione, pulire le risorse seguenti:

* Eliminare la directory del progetto di esempio.
* Elimina la risorsa di Personalizza esperienze, che può essere considerata una risorsa dedicata ad azioni e contesto, da riutilizzare solo se si usa ancora il cibo come dominio delle azioni.


## <a name="next-steps"></a>Passaggi successivi
* [Funzionamento di Personalizza esperienze](how-personalizer-works.md)
* [Caratteristiche](concepts-features.md): informazioni sui concetti relativi alle caratteristiche usate con azioni e contesto
* [Ricompense](concept-rewards.md): informazioni sul calcolo delle ricompense
