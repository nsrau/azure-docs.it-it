---
title: Usare Personalizza esperienze nel chatbot - Personalizza esperienze
description: Personalizzare un chatbot .NET C# con un ciclo di Personalizza esperienze per fornire il contenuto corretto a un utente in base alle azioni (con caratteristiche) e alle caratteristiche del contesto.
ms.topic: tutorial
ms.date: 07/17/2020
ms.openlocfilehash: 8e936b4017ad50434bc7d6b05b4217f82a9080e0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131668"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>Esercitazione: Usare Personalizza esperienze in un chatbot .NET

Usare un chatbot .NET C# con un ciclo di Personalizza esperienze per fornire il contenuto corretto a un utente. Questo chatbot suggerisce un caffè o un tè specifico a un utente. L'utente può accettare o rifiutare il suggerimento. In questo modo, vengono fornite a Personalizza esperienze informazioni per perfezionare il suggerimento successivo.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Configurare le risorse di Azure
> * Configurare ed eseguire il bot
> * Interagire con il bot usando l'emulatore del bot
> * Acquisire informazioni su dove e come il bot usa Personalizza esperienze


## <a name="how-does-the-chat-bot-work"></a>Come funziona il chat bot?

Un chatbot è in genere una conversazione in più passaggi con un utente. Questo chatbot specifico usa Personalizza esperienze per selezionare l'azione migliore (caffè o tè) da offrire all'utente. Per effettuare la selezione, Personalizza esperienze usa l'apprendimento per rinforzo.

Il chatbot deve gestire i turni nella conversazione. Il chatbot usa [Bot Framework](https://github.com/microsoft/botframework-sdk) per gestire l'architettura e la conversazione di bot e usa il servizio cognitivo [Language Understanding](../LUIS/index.yml) (LUIS), per comprendere la finalità del linguaggio naturale dell'utente.

Il chatbot è un sito Web con una route specifica disponibile per rispondere alle richieste, `http://localhost:3978/api/messages`. È possibile usare l'emulatore del bot per interagire visivamente con il chatbot in esecuzione mentre si sviluppa un bot in locale.

### <a name="user-interactions-with-the-bot"></a>Interazioni utente con il bot

Questo è un semplice chatbot che consente di immettere query di testo.

|L'utente immette il testo|Il bot risponde con un testo|Descrizione dell'azione eseguita dal bot per determinare il testo della risposta|
|--|--|--|
|Nessun testo immesso. Il bot avvia la conversazione.|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|Il bot avvia la conversazione con testo informativo e comunica all'utente le informazioni sul contesto: `Tuesday`, `Snowy`.|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|Determina la finalità della query con LUIS, quindi visualizza le opzioni del menu per gli elementi caffè e tè. Caratteristiche delle azioni: |
|`What do you suggest`|`How about Latte?`|Determina la finalità della query con LUIS, quindi chiama l'**API Classificazione** e visualizza la scelta migliore come una domanda `How about {response.RewardActionId}?`. Visualizza anche la chiamata e la risposta JSON a scopo illustrativo.|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Determina la finalità della query con LUIS, quindi chiama l'**API Ricompensa** con la ricompensa `1` e visualizza la chiamata e la risposta JSON a scopo illustrativo.|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Determina la finalità della query con LUIS, quindi chiama l'**API Ricompensa** con la ricompensa `0` e visualizza la chiamata e la risposta JSON a scopo illustrativo.|
|`Reset`|Restituisce il testo informativo.|Determina la finalità della query con LUIS, quindi visualizza il testo informativo e reimposta il contesto.|


### <a name="personalizer-in-this-bot"></a>Personalizza esperienze in questo bot

Questo chatbot usa Personalizza esperienze per selezionare l'azione principale (caffè o tè specifico) in base a un elenco di _azioni_ (un tipo di contenuto) e caratteristiche del contesto.

Il bot invia l'elenco di azioni, insieme alle caratteristiche del contesto, al ciclo di Personalizza esperienze. Personalizza esperienze restituisce la migliore azione singola al bot, che viene visualizzata dal bot.

In questa esercitazione le **azioni** sono tipi di caffè o tè:

|Caffè|Tè|
|--|--|
|Cappuccino<br>Espresso<br>Latte<br>Moka|Tè verde<br>Rooibos|

**API Classificazione:** Per consentire a Personalizza esperienze di acquisire informazioni sulle azioni, il bot invia quanto segue con ogni richiesta dell'API Classificazione:

* Azioni _con caratteristiche_
* Caratteristiche del contesto

Una **caratteristica** del modello è costituita da informazioni sull'azione o sul contesto che possono essere aggregate (raggruppate) tra i membri della base di utenti del chatbot. Una caratteristica _non è_ specifica per un individuo, come un ID utente, né altamente specifica, come l'ora esatta del giorno.

Le caratteristiche vengono usate per allineare le azioni al contesto corrente nel modello. Il modello è una rappresentazione delle conoscenze passate di Personalizza esperienze riguardanti azioni, contesto e relative caratteristiche tramite cui il servizio può prendere decisioni informate.

Il modello, incluse le caratteristiche, viene aggiornato in base a una pianificazione definita dall'impostazione di **Frequenza di aggiornamento del modello** nel portale di Azure.

Le caratteristiche devono essere selezionate con la stessa pianificazione e gli stessi criteri che si applicherebbero a qualsiasi schema o modello nell'architettura tecnica. I valori delle caratteristiche possono essere impostati con la logica di business o con sistemi di terze parti.

> [!CAUTION]
> Le caratteristiche di questa applicazione sono a scopo dimostrativo e potrebbero non essere necessariamente le caratteristiche migliori da usare in un'app Web per il caso d'uso corrente.

#### <a name="action-features"></a>Caratteristiche delle azioni

Ogni azione (contenuto) include caratteristiche per distinguere l'elemento caffè o tè.

Le caratteristiche non vengono configurate come parte della configurazione del ciclo nel portale di Azure. Vengono invece inviate come oggetto JSON con ogni chiamata all'API Classificazione. In questo modo, le azioni e le relative caratteristiche possono aumentare, cambiare e diminuire con grande flessibilità, consentendo a Personalizza esperienze di seguire le tendenze.

Le caratteristiche per caffè e tè includono:

* Località di origine dei chicchi di caffè, ad esempio Kenya e Brasile
* Il caffè o il tè è biologico?
* Tostatura del caffè chiara o scura

Sebbene per il caffè siano presenti tre caratteristiche nell'elenco precedente, il tè ne include solo una. Passare a Personalizza esperienze solo le caratteristiche significative per l'azione. Non passare un valore vuoto per una caratteristica se non è applicabile all'azione.

#### <a name="context-features"></a>Caratteristiche del contesto

Le caratteristiche del contesto consentono a Personalizza esperienze di comprendere il contesto dell'ambiente, ad esempio il dispositivo di visualizzazione, l'utente, la posizione e altre caratteristiche rilevanti per il caso d'uso corrente.

Il contesto per questo chatbot include:

* Tipo di meteo (nevoso, piovoso, soleggiato)
* Giorno della settimana

La selezione delle caratteristiche è casuale in questo chatbot. In un bot reale usare dati reali per le caratteristiche del contesto.

### <a name="design-considerations-for-this-bot"></a>Considerazioni di progettazione per questo bot

Per questa conversazione, è necessario prestare attenzione ad alcuni fattori:
* **Interazione con il bot**: La conversazione è molto semplice perché mostra l'uso della classificazione e della ricompensa in un caso d'uso di base. Non vengono illustrate le funzionalità complete di Bot Framework SDK o dell'emulatore.
* **Personalizza esperienze**: Le caratteristiche vengono selezionate in modo casuale per simulare l'utilizzo. Non selezionare le caratteristiche in modo casuale in uno scenario di Personalizza esperienze di produzione.
* **Language Understanding (LUIS)** : Le espressioni di esempio del modello LUIS sono destinate solo a questo esempio. Non usare un numero così ridotto di espressioni di esempio nell'applicazione LUIS di produzione.


## <a name="install-required-software"></a>Installare il software necessario
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Se si preferisce usare la interfaccia della riga di comando di .NET Core, nel repository degli esempi scaricabili sono disponibili le istruzioni.
- [Microsoft Bot Framework Emulator](https://aka.ms/botframeworkemulator) è un'applicazione desktop che consente agli sviluppatori di bot di eseguire il test e il debug dei bot in localhost o in modalità remota tramite un tunnel.

## <a name="download-the-sample-code-of-the-chat-bot"></a>Scaricare il codice di esempio del chatbot

Il chatbot è disponibile nel repository degli esempi di Personalizza esperienze. Clonare o [scaricare](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip) repository, quindi aprire l'esempio nella directory `/samples/ChatbotExample` con Visual Studio 2019.

Per clonare il repository, usare il comando GIT seguente in una shell Bash (terminale).

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>Creare e configurare le risorse Personalizza esperienze e LUIS

### <a name="create-azure-resources"></a>Creare le risorse di Azure

Per usare questo chatbot, è necessario creare le risorse di Azure per Personalizza esperienze e Language Understanding (LUIS).

* [Creare le risorse di LUIS](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal). Nel passaggio di creazione selezionare **entrambe** perché saranno necessarie entrambe le risorse di creazione e di previsione.
* [Creare la risorsa Personalizza esperienze](how-to-create-resource.md) quindi copiare la chiave e l'endpoint dal portale di Azure. Sarà necessario impostare questi valori nel file `appsettings.json` del progetto .NET.

### <a name="create-luis-app"></a>Creare l'app LUIS

Se non si ha familiarità con LUIS, è necessario [eseguire l'accesso](https://www.luis.ai) e procedere immediatamente alla migrazione dell'account. Non è necessario creare nuove risorse, ma selezionare le risorse create nella sezione precedente di questa esercitazione.

1. Per creare una nuova applicazione LUIS, nel [portale di LUIS](https://www.luis.ai) selezionare la sottoscrizione e la risorsa di creazione.
1. Sempre nella stessa pagina selezionare **+ Nuova app per la conversazione**, quindi **Importa come JSON**.
1. Nella finestra di dialogo popup selezionare **Scegliere un file**, quindi selezionare il file `/samples/ChatbotExample/CognitiveModels/coffeebot.json`. Immettere il nome `Personalizer Coffee bot`.
1. Nella barra di spostamento in alto a destra del portale di LUIS selezionare il pulsante **Esegui il training**.
1. Selezionare il pulsante **Pubblica** per pubblicare l'app nello **slot di produzione** per il runtime di previsione.
1. Selezionare **Gestisci** e quindi **Impostazioni**. Copiare il valore di **ID App**. Sarà necessario impostare questo valore nel file `appsettings.json` del progetto .NET.
1. Sempre nella sezione **Gestisci** selezionare **Risorse di Azure**. Verranno visualizzate le risorse associate nell'app.
1. Selezionare **Aggiungi risorsa di previsione**. Nella finestra di dialogo popup selezionare la sottoscrizione e la risorsa di previsione creata in una sezione precedente di questa esercitazione, quindi selezionare **Fine**.
1. Copiare i valori di **Chiave primaria** e **URL endpoint**. Sarà necessario impostare questi valori nel file `appsettings.json` del progetto .NET.

### <a name="configure-bot-with-appsettingsjson-file"></a>Configurare bot con il file appsettings.json

1. Aprire il file della soluzione chatbot, `ChatbotSamples.sln`, con Visual Studio 2019.
1. Aprire `appsettings.json` nella directory radice del progetto.
1. Impostare tutte e cinque le impostazioni copiate nella sezione precedente di questa esercitazione.

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>Compilare ed eseguire il bot

Dopo aver configurato il file `appsettings.json`, è possibile compilare ed eseguire il chatbot. Quando si esegue questa operazione, viene aperto un browser nel sito Web in esecuzione, `http://localhost:3978`.

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="Screenshot del browser che visualizza il sito Web del chatbot.":::

Mantenere il sito Web in esecuzione perché l'esercitazione illustra le operazioni eseguite dal bot, in modo da poter interagire con esso.


## <a name="set-up-the-bot-emulator"></a>Configurare l'emulatore del bot

1. Aprire l'emulatore del bot e selezionare **Open Bot** (Apri bot).

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="Screenshot della schermata di avvio dell'emulatore del bot.":::


1. Configurare il bot con l'**URL del bot** seguente, quindi selezionare **Connetti**:

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="Screenshot delle impostazioni di apertura del bot dell'emulatore del bot.":::

    L'emulatore si connette al chatbot e visualizza il testo informativo, insieme alle informazioni di registrazione e di debug utili per lo sviluppo locale.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="Screenshot dell'emulatore del bot nel primo turno della conversazione.":::

## <a name="use-the-bot-in-the-bot-emulator"></a>Usare il bot nell'emulatore del bot

1. Per chiedere di visualizzare il menu, immettere `I would like to see the menu`. Il chatbot visualizza gli elementi.
1. Per consentire al bot di suggerire un elemento, immettere `Please suggest a drink for me.`. L'emulatore visualizza la richiesta e la risposta di classificazione nella finestra della chat, in modo che sia possibile visualizzare il codice JSON completo. Il bot mostra un suggerimento, ad esempio `How about Latte?`
1. Se si risponde che si gradisce l'elemento suggerito, significa che si accetta la selezione con la priorità più elevata di Personalizza esperienze, `I like it.`. L'emulatore mostra la richiesta di ricompensa con il punteggio di ricompensa 1 e la risposta nella finestra della chat, in modo che sia possibile visualizzare il codice JSON completo. Il bot risponde con `That’s great! I’ll keep learning your preferences over time.` e `Would you like to get a new suggestion or reset the simulated context to a new day?`

    Se alla selezione si risponde con `no`, a Personalizza esperienze verrà inviato il punteggio di ricompensa 0.


## <a name="understand-the-net-code-using-personalizer"></a>Informazioni sul codice .NET con Personalizza esperienze

La soluzione .NET è un semplice chatbot di Bot Framework. Il codice relativo a Personalizza esperienze si trova nelle cartelle seguenti:
* `/samples/ChatbotExample/Bots`
    * File `PersonalizerChatbot.cs` per l'interazione tra il bot e Personalizza esperienze
* `/samples/ChatbotExample/ReinforcementLearning`: gestisce le azioni e le caratteristiche per il modello di Personalizza esperienze
* `/samples/ChatbotExample/Model`: file per le azioni e le caratteristiche di Personalizza esperienze e per le finalità di LUIS

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs - Utilizzo con Personalizza esperienze

La classe `PersonalizerChatbot` è derivata da `Microsoft.Bot.Builder.ActivityHandler`. Sono disponibili tre proprietà e metodi per gestire il flusso della conversazione.

> [!CAUTION]
> Non copiare il codice da questa esercitazione. Usare il codice di esempio disponibile nel [repository degli esempi di Personalizza esperienze](https://github.com/Azure-Samples/cognitive-services-personalizer-samples).

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

I metodi preceduti da `Send` gestiscono la conversazione con il bot e LUIS. I metodi `ChooseRankAsync` e `RewardAsync` interagiscono con Personalizza esperienze.

#### <a name="calling-rank-api-and-display-results"></a>Chiamata dell'API Classificazione e visualizzazione dei risultati

Il metodo `ChooseRankAsync` compila i dati JSON da inviare all'API Classificazione di Personalizza esperienze raccogliendo le azioni con le caratteristiche e le caratteristiche del contesto.

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>Chiamata dell'API Ricompensa e visualizzazione dei risultati

Il metodo `RewardAsync` compila i dati JSON da inviare all'API Ricompensa di Personalizza esperienze mediante la determinazione del punteggio. Il punteggio è determinato dalla finalità di LUIS identificata nel testo dell'utente e inviata dal metodo `OnTurnAsync`.

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>Considerazioni di progettazione per un bot

Questo esempio mostra una semplice soluzione end-to-end di Personalizza esperienze in un bot. Il caso d'uso corrente potrebbe essere più complesso.

Se si intende usare Personalizza esperienze in un bot di produzione, è opportuno prevedere quanto segue:
* Accesso in tempo reale a Personalizza esperienze _ogni volta_ che è necessaria una selezione classificata. Non è possibile eseguire in batch o memorizzare nella cache l'API Classificazione.  È possibile ritardare o eseguire l'offload della chiamata di ricompensa in un processo separato e se non viene restituita una ricompensa entro il periodo definito, viene impostato un valore di ricompensa predefinito per l'evento.
* Calcolo della ricompensa basato sul caso d'uso: Questo esempio ha mostrato due ricompense pari a zero e una senza intervallo e nessun valore negativo per un punteggio. Il sistema in uso potrebbe richiedere un sistema di assegnazione dei punteggi più granulare.
* Canali bot: questo esempio usa un solo canale, ma se si intende usare più di un canale o varianti del bot su un solo canale, potrebbe essere necessario considerare questo aspetto come parte delle caratteristiche del contesto del modello di Personalizza esperienze.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa esercitazione, pulire le risorse seguenti:

* Eliminare la directory del progetto di esempio.
* Eliminare la risorsa Personalizza esperienze e LUIS nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi
* [Funzionamento di Personalizza esperienze](how-personalizer-works.md)
* [Caratteristiche](concepts-features.md): informazioni sui concetti relativi alle caratteristiche usate con azioni e contesto
* [Ricompense](concept-rewards.md): informazioni sul calcolo delle ricompense
