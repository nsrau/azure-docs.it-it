---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.author: diberry
ms.openlocfilehash: 0b09f15eb05a94bb62f4484ac362467fbdb0af8b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188867"
---
[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [Esempi](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Prerequisites

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="using-this-quickstart"></a>Uso di questo avvio rapido

Per usare questo avvio rapido, è necessario eseguire diversi passaggi:

* Nel portale di Azure creare una risorsa di Personalizza esperienze
* Nella pagina **Configurazione** della risorsa di Personalizza esperienze nel portale di Azure cambiare la frequenza di aggiornamento del modello impostando un intervallo molto breve
* In un editor di codice creare un file di codice e modificarlo
* Dalla riga di comando o dal terminale installare l'SDK dalla riga di comando
* Dalla riga di comando o dal terminale eseguire il file di codice

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

Creare una nuova applicazione .NET Core nell'ambiente di sviluppo integrato o nell'editor preferito.

Nella finestra di una console (ad esempio cmd, PowerShell o Bash) usare il comando dotnet `new` per creare una nuova app console con il nome `personalizer-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: `Program.cs`.

```console
dotnet new console -n personalizer-quickstart
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```console
dotnet build
```

L'output di compilazione non deve contenere alcun avviso o errore.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="install-the-sdk"></a>Installare l'SDK

Nella directory dell'applicazione installare la libreria client di Personalizza esperienze per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.

## <a name="object-model"></a>Modello a oggetti

Il client di Personalizza esperienze è un oggetto [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) che esegue l'autenticazione ad Azure con Microsoft.Rest.ServiceClientCredentials, che contiene la chiave.

Per chiedere il singolo elemento migliore del contenuto, creare un oggetto [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview), quindi passarlo al metodo [client.Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview). Il metodo Rank restituisce un oggetto RankResponse.

Per inviare un punteggio di ricompensa a Personalizza esperienze, creare un oggetto [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview), quindi passarlo al metodo [client.Reward](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview).

In questo argomento di avvio rapido, determinare il punteggio di ricompensa è semplice. In un sistema di produzione invece, stabilire cosa influisce sul [punteggio di ricompensa](../concept-rewards.md) e in quale misura può essere un processo complesso, che si può decidere di cambiare nel tempo. Questa dovrebbe essere una delle principali decisioni di progettazione da prendere nell'architettura di Personalizza esperienze.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di Personalizza esperienze per .NET:

* [Creare un client di Personalizza esperienze](#create-a-personalizer-client)
* [API Classificazione](#request-the-best-action)
* [API Premio](#send-a-reward)

## <a name="add-the-dependencies"></a>Aggiungere le dipendenze

Dalla directory del progetto aprire il file **Program.cs** nell'ambiente di sviluppo integrato o nell'editor preferito. Sostituire il codice `using` esistente con le direttive `using` seguenti:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Aggiungere le informazioni sulla risorsa di Personalizza esperienze

Nella classe **Program** creare le variabili per la chiave e l'endpoint di Azure della risorsa estratti dalle variabili di ambiente, denominate `PERSONALIZER_RESOURCE_KEY` e `PERSONALIZER_RESOURCE_ENDPOINT`. Se le variabili di ambiente sono state create dopo l'avvio dell'applicazione, per accedere alle variabili sarà necessario chiudere e ricaricare l'editor, la shell o l'ambiente di sviluppo integrato in cui sono in esecuzione. I metodi verranno creati in un secondo momento in questo argomento di avvio rapido.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Creare un client di Personalizza esperienze

Si creerà ora un metodo per restituire un client di Personalizza esperienze. Il parametro per il metodo è `PERSONALIZER_RESOURCE_ENDPOINT` e l'ApiKey è `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Recuperare gli elementi alimentari come azioni classificabili

Le azioni rappresentano le scelte di contenuto da cui si vuole che Personalizza esperienze selezioni l'elemento di contenuto migliore. Aggiungere i metodi seguenti alla classe Program per rappresentare il set di azioni e le relative caratteristiche. 

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Recuperare le preferenze dell'utente per il contesto

Aggiungere i metodi seguenti alla classe Program per ottenere l'input di un utente dalla riga di comando per l'ora del giorno e la preferenza alimentare attuale. Si useranno come caratteristiche del contesto.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Entrambi i metodi usano il metodo `GetKey` per leggere la selezione dell'utente dalla riga di comando.

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Creare il ciclo di apprendimento

Il ciclo di apprendimento di Personalizza esperienze è un ciclo di chiamate [Rank](#request-the-best-action) e [Reward](#send-a-reward). In questo argomento di avvio rapido ogni chiamata Rank per personalizzare il contenuto è seguita da una chiamata Reward per indicare a Personalizza esperienze il livello di prestazioni del servizio.

Il codice seguente esegue un ciclo in cui chiede all'utente di indicare le proprie preferenze alla riga di comando, invia tali informazioni a Personalizza esperienze per la selezione dell'azione migliore, presenta all'utente una selezione sotto forma di elenco da cui scegliere e quindi invia un punteggio di ricompensa a Personalizza esperienze per segnalare l'accuratezza del servizio nella selezione.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Aggiungere i metodi seguenti, che [ottengono le scelte di contenuto](#get-food-items-as-rankable-actions), prima di eseguire il file di codice:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Richiedere l'azione migliore

Per completare la richiesta Rank, il programma richiede le preferenze dell'utente per creare un oggetto `currentContent` delle scelte di contenuto. Il processo può creare contenuto da escludere dalle azioni, indicato come `excludeActions`. Per ricevere la risposta, la richiesta Rank deve includere le azioni e le relative caratteristiche, le caratteristiche currentContext, excludeActions e un ID evento univoco.

In questo argomento di avvio rapido sono state usate funzionalità di contesto semplici come l'ora del giorno e le preferenze alimentari dell'utente. Nei sistemi di produzione, determinare e [valutare](../concept-feature-evaluation.md) [azioni e funzionalità](../concepts-features.md) può non essere altrettanto semplice.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Inviare una ricompensa

Per ottenere il punteggio di ricompensa e inviare la richiesta Reward, il programma ottiene la selezione dell'utente dalla riga di comando, assegna un valore numerico alla selezione, quindi invia l'ID evento univoco e il valore numerico all'API Premio.

Questo argomento di avvio rapido assegna un numero semplice come punteggio di ricompensa, ovvero 0 o 1. Nei sistemi di produzione, determinare quando e cosa inviare alla chiamata [Reward](../concept-rewards.md) può essere un'operazione non banale, a seconda delle esigenze specifiche.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Eseguire il programma

Eseguire l'applicazione con il comando dotnet `run` dalla directory dell'applicazione.

```console
dotnet run
```

![Il programma di avvio rapido pone alcune domande per raccogliere le preferenze utente, note come funzionalità, quindi restituisce l'azione più comune.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

Il [codice sorgente per questo argomento di avvio rapido](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) è disponibile nel repository GitHub di esempi di Personalizza esperienze.
