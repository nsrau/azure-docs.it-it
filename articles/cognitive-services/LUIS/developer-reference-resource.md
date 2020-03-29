---
title: Risorse per sviluppatori - Comprensione della lingua
description: SDK, API REST, CLI consentono di sviluppare app di comprensione del linguaggio (LUIS) nel linguaggio di programmazione. Gestire le risorse di Azure e le stime LUIS.
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 5e375157cef4789bc2980f6154ea8d59e765ff3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457985"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Risorse per sviluppatori SDK, REST e CLI per la comprensione della lingua (LUIS)

SDK, API REST, CLI consentono di sviluppare app di comprensione del linguaggio (LUIS) nel linguaggio di programmazione. Gestire le risorse di Azure e le stime LUIS.

## <a name="azure-resource-management"></a>Gestione delle risorse di AzureAzure resource management

Usare il livello gestione dei servizi cognitivi di Azure per creare, modificare, elencare ed eliminare la risorsa Informazioni sulla lingua o Servizio cognitivo.

Trova la documentazione di riferimento in base allo strumento:

* [Interfaccia della riga di comando di AzureAzure](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Lingua Informazioni sulle richieste di creazione e stima

È possibile accedere al servizio Language Understanding da una risorsa di Azure che è necessario creare. Ci sono due risorse:

* Usare la risorsa **di creazione** per la formazione per creare, modificare, eseguire il training e pubblicare.
* Usare la **stima** per il runtime per inviare il testo dell'utente e ricevere una stima.

Informazioni sull'endpoint di [stima V3.](luis-migration-api-v3.md)

Usare il [codice di esempio di Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-quickstart-code) per apprendere e usare le attività più comuni.

### <a name="rest-specifications"></a>Specifiche REST

Le [specifiche REST LUIS](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), insieme a tutte le [specifiche REST](https://github.com/Azure/azure-rest-api-specs)di Azure , sono disponibili pubblicamente su GitHub.

### <a name="rest-apis"></a>API REST

Entrambe le API dell'endpoint di creazione e stima sono disponibili dalle API REST:Both authoring and prediction endpoint APIS are available from REST APIs:

|Type|Versione|
|--|--|
|Creazione|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[anteprima V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Previsione|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Endpoint REST

LUIS dispone attualmente di due tipi di endpoint:

* creazione sull'endpoint di training
* stima delle query sull'endpoint di runtime.

|Scopo|URL|
|--|--|
|Creazione nell'endpoint di training|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Runtime V2 - tutte le stime sull'endpoint di runtimeV2 Runtime - all predictions on runtime endpoint|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|Runtime V3 - stima delle versioni nell'endpoint di runtimeV3 Runtime - versions prediction on runtime endpoint|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|Runtime V3 - stima degli slot nell'endpoint di runtimeV3 Runtime - slot prediction on runtime endpoint|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

La tabella seguente illustra i parametri indicati con parentesi graffe `{}` nella tabella precedente.

|Parametro|Scopo|
|--|--|
|`your-resource-name`|Nome risorsa di AzureAzure resource name|
|`q` o `query`|testo dell'espressione inviato dall'applicazione client, ad esempio chatbot|
|`version`|Nome della versione di 10 caratteri|
|`slot`| `production` o `staging`|

### <a name="language-based-sdks"></a>SDK basati sulla lingua

|Linguaggio |Documentazione di riferimento|Pacchetto|Esempi|Avvi rapidi|
|--|--|--|--|--|
|C#|[Creazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Previsioni](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Creazione NuGetNuGet authoring](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Previsione NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Esempi di .Net SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Creare e gestire app](sdk-authoring.md?pivots=programming-language-csharp)<br>[Eseguire query sull'endpoint di stima](sdk-query-prediction-endpoint.md)|
|Go|[Creazione e stima](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Creazione](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Previsioni](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Creazione e stima tramite RESTAuthoring and Prediction using REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Creazione e stima](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven authoring](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Previsione di Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Creazione](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Previsioni](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Creazione e stima](luis-get-started-get-intent-from-rest.md)
|Node.js|[Creazione](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Previsioni](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Creazione NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Stima NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Creazione](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Previsioni](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Creazione e stima tramite RESTAuthoring and Prediction using REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Creazione e stima](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Creazione](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Creazione](sdk-authoring.md?pivots=programming-language-python)<br>[Stima tramite RESTPrediction using REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Contenitori

Language Understanding (LUIS) fornisce un [contenitore](luis-container-howto.md) per fornire versioni locali e contenute dell'app.

### <a name="export-and-import-formats"></a>Formati di esportazione e importazione

La comprensione della lingua offre la possibilità di gestire `.LU` l'app e i relativi modelli in formato JSON, il formato ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) e un pacchetto compresso per il contenitore Language Understanding.

L'importazione e l'esportazione di questi formati sono disponibili dalle API e dal portale LUIS. Il portale fornisce l'importazione e l'esportazione come parte dell'elenco App e dell'elenco Versioni.

## <a name="other-tools-and-sdks"></a>Altri strumenti e SDK

Il framework del bot è disponibile come [SDK](https://github.com/Microsoft/botframework) in diverse lingue e come servizio usando il servizio bot di [Azure.](https://dev.botframework.com/)

Il framework bot fornisce diversi strumenti per facilitare la comprensione della lingua, tra cui:Bot framework provides [several tools](https://github.com/microsoft/botbuilder-tools) to help with Language Understanding, including:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) - Compilare i modelli di conoscenza del linguaggio LUIS utilizzando i file markdown
* [Interfaccia della riga di comando LUIS](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) - Creare e gestire le applicazioni LUIS.ai
* [Spedizione:](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)gestire le app padre e figlio
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) - Genera automaticamente le classi di backup di C /Typescript per le finalità e le entità LUIS.
* [Emulatore di Bot Framework:](https://github.com/Microsoft/BotFramework-Emulator/releases) un'applicazione desktop che consente agli sviluppatori di bot di testare ed eseguire il debug dei bot creati con Bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - uno strumento di sviluppo integrato per sviluppatori e team multidisciplinari per creare bot ed esperienze conversazionali con Microsoft Bot Framework
* [come microsoft/NLU. DevOps:](https://github.com/microsoft/NLU.DevOps) strumenti che supportano l'integrazione e la distribuzione continua per i servizi NLU.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [codici di errore HTTP](luis-reference-response-codes.md) comuni
* [Documentazione di riferimento](https://docs.microsoft.com/azure/index) per tutte le API e gli SDK
* [Framework bot](https://github.com/Microsoft/botbuilder-dotnet) e [servizio bot di AzureBot framework](https://dev.botframework.com/) and Azure Bot Service
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Contenitori cognitivi](../cognitive-services-container-support.md)
