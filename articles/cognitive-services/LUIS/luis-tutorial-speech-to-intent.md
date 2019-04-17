---
title: Usare Speech SDK per C#
titleSuffix: Azure Cognitive Services
description: Il Servizio di riconoscimento vocale consente di usare una singola richiesta per ricevere l'audio e restituire gli oggetti JSON di stima LUIS. In questo articolo viene scaricato e usato un progetto C# in Visual Studio per pronunciare un'espressione in un microfono e ricevere le informazioni di stima LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/08/2018
ms.author: diberry
ms.openlocfilehash: 9d6173ee25f28aa884513d126c06a8a7c722098d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273839"
---
# <a name="integrate-speech-service-with-your-language-understanding-app"></a>Integrare il servizio Voce con l'app Language Understanding
Il [Servizio di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) consente di usare una singola richiesta per ricevere l'audio e restituire gli oggetti JSON di stima LUIS. In questo articolo viene scaricato e usato un progetto C# in Visual Studio per pronunciare un'espressione in un microfono e ricevere le informazioni di stima LUIS. Il progetto usa il pacchetto Speech [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) già incluso come riferimento. 

Per questo articolo è necessario un account del sito Web [LUIS][LUIS] gratuito per importare l'applicazione.

## <a name="create-luis-endpoint-key"></a>Creare la chiave di endpoint LUIS
Nel portale di Azure [creare](luis-how-to-azure-subscription.md) una chiave di **Servizi cognitivi** (LUIS) per l'app LUIS.  

## <a name="import-human-resources-luis-app"></a>Importare l'app LUIS per le risorse umane
Le finalità e le espressioni usate in questo articolo derivano dall'app LUIS per le risorse umane disponibile nel repository GitHub [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding). Scaricare il file [HumanResources.json](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources.json), salvarlo con l'estensione `.json` e [importarlo](luis-how-to-start-new-app.md#import-new-app) in LUIS. 

Questa app include le finalità, le entità e le espressioni correlate al dominio Human Resources (Risorse umane). Le espressioni di esempio includono:

|Espressioni di esempio|
|--|
|Chi è il manager di John Smith?|
|Che cosa gestisce John Smith?|
|Dov'è il modulo 123456?|
|Dispongo di tempo libero retribuito?|


## <a name="add-keyphrase-prebuilt-entity"></a>Aggiungere l'entità predefinita KeyPhrase
Dopo aver importato l'app, selezionare **Entities** (Entità) e quindi **Add prebuilt entity** (Aggiungi entità predefinita). Aggiungere l'entità **KeyPhrase**. L'entità KeyPhrase estrae l'oggetto principale dall'espressione.

## <a name="train-and-publish-the-app"></a>Eseguire il training e pubblicare l'app
1. Nella barra di spostamento superiore destra selezionare il pulsante **Train** (Training) per eseguire il training dell'app LUIS.

2. Selezionare **Manage** (Gestisci) nella barra in alto a destra, quindi selezionare **Keys and endpoints** (Chiavi ed endpoint) nella barra di spostamento sinistra. 

3. Nella pagina **Keys and endpoints** (Chiavi ed endpoint) assegnare la chiave LUIS creata nella sezione [Creare la chiave di endpoint LUIS](#create-luis-endpoint-key).

   In questa pagina, raccogliere l'ID app, l'area di pubblicazione e l'ID sottoscrizione della chiave LUIS creata nella sezione [Creare la chiave di endpoint LUIS](#create-luis-endpoint-key). È necessario modificare il codice per usare questi valori più avanti nell'articolo. 
  
   **Non** usare la chiave gratuita per questo esercizio. Per questo esercizio è necessario usare una chiave **Language Understanding** creata nel portale di Azure. 

   https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=


4. Pubblicare l'app LUIS scegliendo il pulsante **Publish** (Pubblica) nella barra in alto a destra. 

## <a name="audio-device"></a>Dispositivo audio
Questo articolo usa il dispositivo audio del computer. Il dispositivo può essere costituito da cuffie con microfono o da un dispositivo audio incorporato. Controllare i livelli di input audio per verificare se è necessario parlare a voce più alta del solito per consentire il riconoscimento vocale da parte del dispositivo audio. 

## <a name="download-the-luis-sample-project"></a>Scaricare il progetto di esempio LUIS
 Clonare o scaricare il repository [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding). Aprire il [progetto di riconoscimento finalità voce](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-speech-intent-recognition) con Visual Studio e ripristinare i pacchetti NuGet. Il file della soluzione VS è documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Speech SDK è già incluso come riferimento. 

[![SScreenshot di Visual Studio 2017 che mostra il pacchetto NuGet Microsoft.CognitiveServices.Speech(./media/luis-tutorial-speech-to-intent/nuget-package.png "Screenshot di Visual Studio 2017 che mostra il pacchetto NuGet Microsoft.CognitiveServices.Speech")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Modificare il codice C#
Aprire il file `Program.cs` e modificare le variabili seguenti:

|Nome variabile|Scopo|
|--|--|
|LUIS_assigned_endpoint_key|Corrisponde al valore subscription-key dell'URL endpoint della pagina Publish (Pubblica)|
|LUIS_endpoint_key_region|Corrisponde al primo sottodominio dell'URL endpoint, ad esempio `westus`|
|LUIS_app_ID|Corrisponde alla route dell'URL endpoint che segue **apps/**|

Nel file `Program.cs` le finalità per le risorse umane sono già mappate.

Compilare ed eseguire l'app. 

## <a name="test-code-with-utterance"></a>Testare il codice con un'espressione
Dire al microfono "Quali sono i dentisti autorizzati a Redmond?"

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

La finalità corretta, **GetEmployeeBenefits**, è stata individuata con un'attendibilità del 85%. Viene restituita l'entità keyPhrase. 

Speech SDK restituisce la risposta LUIS completa. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app LUIS per le risorse umane. A tale scopo, selezionare l'app e nella barra degli strumenti contestuale disponibile sopra l'elenco selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

Non dimenticare di eliminare la directory dopo aver usato il codice di esempio.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Integrare LUIS con un bot](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
