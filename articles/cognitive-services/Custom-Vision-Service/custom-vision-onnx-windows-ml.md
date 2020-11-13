---
title: Usare un modello ONNX con Windows ML - Servizio visione artificiale personalizzato
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare un'app per la piattaforma UWP di Windows che usi un modello ONNX esportato da Servizi cognitivi di Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: 58ced0c45d66223ac3e40112126e92a4539db32d
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616077"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Usare un modello ONNX di Visione personalizzata con Windows ML (anteprima)

Informazioni su come usare un modello ONNX esportato dal Servizio visione artificiale personalizzato con Windows ML (anteprima).

In questa guida si apprenderà come usare un file ONNX esportato dal Servizio visione artificiale personalizzato con Windows ML. Si userà l'applicazione UWP di esempio con il classificatore di immagini sottoposto a training.

## <a name="prerequisites"></a>Prerequisiti

* Windows 10, versione 1809 o successive
* Windows SDK per build 17763 o successiva
* Visual Studio 2017 15.7 o versioni successive con il carico di lavoro di __sviluppo UWP__ abilitato.
* Modalità di sviluppo abilitata nel PC. Per altre informazioni, vedere [Abilitare il dispositivo per lo sviluppo](/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Informazioni sull'app di esempio

L'applicazione inclusa è un'app di Windows UWP generica. Consente di selezionare un'immagine dal computer, che viene quindi elaborata da un modello di classificazione archiviato localmente. I tag e i punteggi restituiti dal modello vengono visualizzati accanto all'immagine.

## <a name="get-the-example-code"></a>Ottenere il codice di esempio

L'applicazione di esempio è disponibile nel repository di esempio [ONNX visione personalizzata di servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) su GitHub. Clonarlo nel computer locale e aprire *SampleOnnxEvaluationApp. sln* in Visual Studio.

## <a name="test-the-application"></a>Test dell'applicazione

1. Usare il tasto `F5` per avviare l'applicazione da Visual Studio. Potrebbe essere richiesto di abilitare la modalità di sviluppo.
1. All'avvio dell'applicazione, usare il pulsante per selezionare un'immagine per l'assegnazione dei punteggi. Il modello ONNX predefinito viene sottoposto a training per la classificazione di diversi tipi di plancton.

## <a name="use-your-own-model"></a>Usare il modello personalizzato

Per usare il proprio modello di classificazione delle immagini, attenersi alla procedura seguente:

1. Creare ed eseguire il training di un classificatore con il Servizio visione artificiale personalizzato. Per istruzioni su come eseguire questa operazione, vedere [creare ed eseguire il training di un classificatore](./getting-started-build-a-classifier.md). Usare uno dei domini **Compact** , ad esempio **General (Compact)**. 
   * Se si dispone di un classificatore esistente che utilizza un dominio diverso, è possibile convertirlo in **Compact** nelle impostazioni del progetto. Quindi, ripetere il training del progetto prima di continuare.
1. Esportare il modello. Passare alla scheda prestazioni e selezionare un'iterazione di cui è stato eseguito il training con un dominio **compatto** . Selezionare il pulsante **Esporta** visualizzato. Quindi selezionare **ONNX** , quindi **Esporta**. Quando il file è pronto, selezionare il pulsante **Download**. Per altre informazioni sulle opzioni di esportazione, vedere [esportare il modello](./export-your-model.md).
1. Aprire il file con *estensione zip* scaricato ed estrarre il file *Model. Onnx* . Questo file contiene il modello di classificazione.
1. Nella Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla cartella **Asset** e scegliere __Aggiungi elemento esistente__. Selezionare il file ONNX.
1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul file ONNX e scegliere **Proprietà**. Modificare le proprietà seguenti per il file:
   * __Azione di compilazione__ -> __Contenuto__
   * __Copia nella directory di output__ -> __Copia se più recente__
1. Aprire quindi _MainPage.XAML.cs_ e modificare il valore di `_ourOnnxFileName` nel nome del file ONNX.
1. Utilizzare `F5` per compilare ed eseguire il progetto.
1. Fare clic sul pulsante per selezionare l'immagine da valutare.

## <a name="next-steps"></a>Passaggi successivi

Per scoprire altri modi per esportare e usare un modello del Servizio visione artificiale personalizzato, vedere i documenti seguenti:

* [Esportare il modello](./export-your-model.md)
* [Use exported Tensorflow model in an Android application](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) (Usare il modello Tensorflow esportato in un'applicazione Android)
* [Use exported CoreML model in a Swift iOS application](https://go.microsoft.com/fwlink/?linkid=857726) (Usare il modello CoreML esportato in un'applicazione iOS Swift)
* [Use exported CoreML model in an iOS application with Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) (Usare il modello CoreML esportato in un'applicazione iOS con Xamarin)

Per altre informazioni sull'uso dei modelli ONNX con Windows ML, vedere [integrare un modello nell'app con Windows ml](/windows/ai/windows-ml/integrate-model).