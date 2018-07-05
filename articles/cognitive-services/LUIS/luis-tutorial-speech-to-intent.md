---
title: Usare Speech C# SDK con LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Usare l'esempio di Speech C# SDK per parlare nel microfono e ottenere le stime delle finalità ed entità LUIS restituite.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/26/2018
ms.author: v-geberr;
ms.openlocfilehash: b681598f953d217ca636fb5c0adc3de4ddbebd60
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031788"
---
# <a name="integrate-speech-service"></a>Integrare il Servizio di riconoscimento vocale
Il [Servizio di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) consente di usare una singola richiesta per ricevere l'audio e restituire gli oggetti JSON di stima LUIS.

In questo articolo viene scaricato e usato un progetto C# in Visual Studio per pronunciare un'espressione in un microfono e ricevere le informazioni di stima LUIS. Il progetto usa il pacchetto Speech [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) già incluso come riferimento. 

Per questo articolo è necessario un account del sito Web [LUIS][LUIS] gratuito per importare l'applicazione.

## <a name="create-luis-endpoint-key"></a>Creare la chiave di endpoint LUIS
Nel portale di Azure [creare](luis-how-to-azure-subscription.md#create-luis-endpoint-key) una chiave **Language Understanding** (LUIS). 

## <a name="import-human-resources-luis-app"></a>Importare l'app LUIS per le risorse umane
Le finalità e le espressioni di questo articolo derivano dall'app LUIS per le risorse umane disponibile nel repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) (Esempi LUIS). Scaricare il file [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json), salvarlo con l'estensione *.json e [importarlo](create-new-app.md#import-new-app) in LUIS. 

Questa app include le finalità, le entità e le espressioni correlate al dominio Human Resources (Risorse umane). Le espressioni di esempio includono:

```
Who is John Smith's manager?
Who does John Smith manage?
Where is Form 123456?
Do I have any paid time off?
```

## <a name="add-keyphrase-prebuilt-entity"></a>Aggiungere l'entità predefinita KeyPhrase
Dopo aver importato l'app, selezionare **Entities** (Entità) e quindi **Manage prebuilt entities** (Gestisci entità predefinite). Aggiungere l'entità **KeyPhrase**. L'entità KeyPhrase estrae l'oggetto principale dall'espressione.

## <a name="train-and-publish-the-app"></a>Eseguire il training e pubblicare l'app
1. Nella barra di spostamento superiore destra selezionare il pulsante **Train** (Training) per eseguire il training dell'app LUIS.

2. Selezionare **Publish** (Pubblica) per passare alla pagina di pubblicazione. 

3. Nella parte inferiore della pagina **Publish** (Pubblica) aggiungere la chiave LUIS creata nella sezione [Creare la chiave di endpoint LUIS](#create-luis-endpoint-key).

4. Pubblicare l'app LUIS scegliendo il pulsante **Publish** (Pubblica) a destra dello slot Publish (Pubblica). 

  Nella pagina **Publish** (Pubblica) raccogliere l'ID app, l'area di pubblicazione e l'ID sottoscrizione della chiave LUIS creata nella sezione [Creare la chiave di endpoint LUIS](#create-luis-endpoint-key). È necessario modificare il codice per usare questi valori più avanti nell'articolo. 

  I valori sono inclusi tutti nell'URL endpoint nella parte inferiore della pagina **Publish** (Pubblica) per la chiave creata. 
  
  **Non** usare la chiave gratuita per questo esercizio. Per questo esercizio è necessario usare una chiave **Language Understanding** creata nel portale di Azure. 

  https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=

## <a name="audio-device"></a>Dispositivo audio
Questo articolo usa il dispositivo audio del computer. Il dispositivo può essere costituito da cuffie con microfono o da un dispositivo audio incorporato. Controllare i livelli di input audio per verificare se è necessario parlare a voce più alta del solito per consentire il riconoscimento vocale da parte del dispositivo audio. 

## <a name="download-the-luis-sample-project"></a>Scaricare il progetto di esempio LUIS
 Clonare o scaricare il repository [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) (Esempi LUIS). Aprire il [progetto di riconoscimento finalità voce](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) con Visual Studio e ripristinare i pacchetti NuGet. Il file della soluzione VS è .\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Speech SDK è già incluso come riferimento. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Screenshot di Visual Studio 2017 con il pacchetto NuGet Microsoft.CognitiveServices.Speech")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Modificare il codice C#
Aprire il file **LUIS_samples.cs** e modificare le variabili seguenti:

|Nome variabile|Scopo|
|--|--|
|luisSubscriptionKey|Corrisponde al valore della chiave di sottoscrizione dell'URL endpoint della pagina Publish (Pubblica)|
|luisRegion|Corrisponde al primo sottodominio dell'URL endpoint|
|luisAppId|Corrisponde alla route dell'URL endpoint che segue **apps/**|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Screenshot di Visual Studio 2017 con le variabili di LUIS_samples.cs")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

Nel file le finalità per le risorse umane sono già mappate.

[![](./media/luis-tutorial-speech-to-intent/intents.png "Screenshot di Visual Studio 2017 con le finalità di LUIS_samples.cs")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

Compilare ed eseguire l'app. 

## <a name="test-code-with-utterance"></a>Testare il codice con un'espressione
Selezionare **1** e dire al microfono "Chi è il capo di John Smith".

```cmd
1. Speech recognition of LUIS intent.
0. Stop.
Your choice: 1
LUIS...
Say something...
ResultId:cc83cebc9d6040d5956880bcdc5f5a98 Status:Recognized IntentId:<GetEmployeeOrgChart> Recognized text:<Who is the manager of John Smith?> Recognized Json:{"DisplayText":"Who is the manager of John Smith?","Duration":25700000,"Offset":9200000,"RecognitionStatus":"Success"}. LanguageUnderstandingJson:{
  "query": "Who is the manager of John Smith?",
  "topScoringIntent": {
    "intent": "GetEmployeeOrgChart",
    "score": 0.617331
  },
  "entities": [
    {
      "entity": "manager of john smith",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 31
    }
  ]
}

Recognition done. Your Choice:

```

La finalità corretta, **GetEmployeeOrgChart**, viene individuata con un'attendibilità del 61%. Viene restituita l'entità keyPhrase. 

Speech SDK restituisce la risposta LUIS completa. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app LUIS per le risorse umane. A tale scopo, selezionare il menu con i puntini di sospensione (...) a destra del nome dell'app nell'elenco di app, quindi selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

Ricordarsi di eliminare la directory LUIS-Samples (Esempi LUIS) dopo aver usato il codice di esempio.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Integrare un bot con Language Understanding](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website