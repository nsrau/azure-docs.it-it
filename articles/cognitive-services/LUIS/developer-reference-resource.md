---
title: Risorse per sviluppatori-Language Understanding
description: SDK, API REST, interfaccia della riga di comando, consentono di sviluppare app Language Understanding (LUIS) nel linguaggio di programmazione. Gestisci le tue risorse di Azure e le tue stime LUIS.
ms.topic: reference
ms.date: 02/09/2020
ms.openlocfilehash: ed869b7022e43b8ecf8c1f05bb3c6f0919076818
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119968"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Risorse per sviluppatori SDK, REST e CLI per Language Understanding (LUIS)

SDK, API REST, interfaccia della riga di comando, consentono di sviluppare app Language Understanding (LUIS) nel linguaggio di programmazione. Gestisci le tue risorse di Azure e le tue stime LUIS. 

## <a name="azure-resource-management"></a>Gestione risorse di Azure

Usare il livello di gestione dei servizi cognitivi di Azure per creare, modificare, elencare ed eliminare la risorsa di Language Understanding o del servizio cognitivo.

Trovare la documentazione di riferimento basata sullo strumento:

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [AzureRM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding la creazione e la stima delle richieste

È possibile accedere al servizio Language Understanding da una risorsa di Azure che è necessario creare. Sono disponibili due risorse:

* Usare la risorsa di **creazione** per il training per creare, modificare, eseguire il training e pubblicare.
* Utilizzare la **stima** per Runtime per inviare il testo dell'utente e ricevere una stima.

Informazioni sull' [endpoint di stima V3](luis-migration-api-v3.md).

Usare il [codice di esempio di servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-quickstart-code) per apprendere e usare le attività più comuni.

### <a name="rest-apis"></a>API REST

Le API REST di creazione ed endpoint di stima sono disponibili nelle API REST:

|Type|Versione|
|--|--|
|Creazione e modifica|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[Anteprima V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Stima|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>SDK basati su linguaggio

|Lingua: |Documentazione di riferimento|Pacchetto|Samples|Guide introduttive|
|--|--|--|--|--|
|C#|[Creazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Previsioni](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Creazione di NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Stima di NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Esempi di .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Crea e Gestisci app](sdk-authoring.md?pivots=programming-language-csharp)<br>[Endpoint di stima query](sdk-query-prediction-endpoint.md)|
|Vai|[Creazione e stima](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Creazione](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Previsioni](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Creazione e stima con REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Creazione e stima](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Creazione di Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Stima Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Creazione](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Previsioni](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Creazione e stima](luis-get-started-get-intent-from-rest.md)
|Node.js|[Creazione](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Previsioni](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Creazione NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Stima NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Creazione](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Previsioni](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Creazione e stima con REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Creazione e stima](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Creazione](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Creazione](sdk-authoring.md?pivots=programming-language-python)<br>[Stima con REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Contenitori

Language Understanding (LUIS) fornisce un [contenitore](luis-container-howto.md) per fornire versioni locali e indipendenti dell'app.

### <a name="export-and-import-formats"></a>Esportare e importare formati

Language Understanding offre la possibilità di gestire l'app e i relativi modelli in formato JSON, il formato `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) e un pacchetto compresso per il contenitore di Language Understanding.

L'importazione e l'esportazione di questi formati è disponibile dalle API e dal portale LUIS. Il portale fornisce l'importazione e l'esportazione come parte dell'elenco di app e delle versioni.

## <a name="other-tools-and-sdks"></a>Altri strumenti e SDK

Bot Framework è disponibile come [SDK](https://github.com/Microsoft/botframework) in un'ampia gamma di linguaggi e come servizio tramite il [servizio Azure bot](https://dev.botframework.com/).

Bot Framework offre [diversi strumenti](https://github.com/microsoft/botbuilder-tools) che consentono di Language Understanding, tra cui:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -compila i modelli di comprensione del linguaggio Luis usando i file Markdown
* [Interfaccia](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) della riga di comando Luis: creare e gestire le applicazioni Luis.ai
* Gestire le app padre e figlio tramite [Dispatch](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) -genera automaticamente le classi C#/typescript di backup per gli Intent ed entità di Luis.
* [Emulatore di bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases) : applicazione desktop che consente agli sviluppatori di bot di testare ed eseguire il debug di bot creati usando bot Framework SDK


## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui codici di [errore http](luis-reference-response-codes.md) comuni
* [Documentazione di riferimento](https://docs.microsoft.com/azure/index#pivot=sdkstools) per tutte le API e gli SDK
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) e [servizio Azure bot](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Contenitori cognitivi](../cognitive-services-container-support.md)
