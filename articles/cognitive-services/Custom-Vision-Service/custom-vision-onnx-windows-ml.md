---
title: 'Esercitazione: Usare un modello ONNX con Windows ML - Servizio visione artificiale personalizzato'
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare un'app per la piattaforma UWP di Windows che usi un modello ONNX esportato da Servizi cognitivi di Azure.
services: cognitive-services
author: larryfr
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: larryfr
ms.openlocfilehash: 65956ab9fa182794b4cb9ac1fc1800198f2dcdaa
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561115"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Esercitazione: Usare un modello ONNX di Visione personalizzata con Windows ML (anteprima)

Informazioni su come usare un modello ONNX esportato dal Servizio visione artificiale personalizzato con Windows ML (anteprima).

Le informazioni contenute in questo documento illustrano come usare un file ONNX esportato dal Servizio visione artificiale personalizzato con Windows ML. Viene fornito un esempio di applicazione UWP Windows. Un modello sottoposto a training è incluso con l'esempio. Sono inoltre disponibili i passaggi per usare il modello personalizzato con questo esempio.

> [!div class="checklist"]
> * Informazioni sull'app di esempio
> * Ottenere il codice di esempio
> * Eseguire l'esempio
> * Usare il modello personalizzato

## <a name="prerequisites"></a>Prerequisiti

* Windows 10 build 17738 o versione successiva

* Windows SDK per build 17738 o versione successiva

* Visual Studio 2017 15.7 o versioni successive con il carico di lavoro di __sviluppo UWP__ abilitato.

* Modalità di sviluppo abilitata. Per altre informazioni, vedere il documento [Abilitare il dispositivo per lo sviluppo](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Informazioni sull'app di esempio

L'applicazione è un'applicazione UWP Windows generica. Consente di selezionare un'immagine dal computer, che viene quindi inviata al modello. I tag e i punteggi restituiti dal modello vengono visualizzati accanto all'immagine.

## <a name="get-the-example-code"></a>Ottenere il codice di esempio

L'applicazione di esempio è disponibile all'indirizzo [https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/](https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/).

## <a name="run-the-example"></a>Eseguire l'esempio

1. Usare il tasto `F5` per avviare l'applicazione da Visual Studio. Potrebbe essere richiesto di abilitare la modalità di sviluppo. Per altre informazioni, vedere il documento [Abilitare il dispositivo per lo sviluppo](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

1. All'avvio dell'applicazione, usare il pulsante per selezionare un'immagine per l'assegnazione dei punteggi.

## <a name="use-your-own-model"></a>Usare il modello personalizzato

Per usare il modello personalizzato, eseguire la procedura seguente:

1. [Creare ed eseguire il training](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) di un classificatore con il Servizio visione artificiale personalizzato. Per esportare il modello, selezionare un dominio __compatto__, ad esempio **General (compact)** (Generale - compatto). Per esportare un classificatore esistente, convertire il dominio in compatto selezionando l'icona a forma di ingranaggio in alto a destra. In __Impostazioni__ scegliere un modello compatto, salvare ed eseguire il training del progetto.  

1. [Esportare il modello](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) passando alla scheda Prestazioni. Selezionare un'iterazione sottoposta a training con un dominio compatto. Verrà visualizzato un pulsante "Export" (Esporta). Selezionare *Export* (Esporta), *ONNX*, *ONNX1.2* e quindi *Export* (Esporta). Quando il file è pronto, selezionare il pulsante *Download*.

1. Posizionare il file ONNX nella cartella __Asset__ del progetto. 

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella Asset e scegliere __Aggiungi elemento esistente__. Selezionare il file ONNX.

1. In Esplora soluzioni selezionare il file ONNX dalla cartella Asset. Modificare le proprietà seguenti per il file:

    * __Azione di compilazione__ -> __Contenuto__
    * __Copia nella directory di output__ -> __Copia se più recente__

1. Impostare la variabile `_onnxFileNames` sul nome del file ONNX. Modificare anche `ClassLabel` impostando il numero di etichette contenute nel modello.

1. Compilare ed eseguire.

1. Fare clic sul pulsante per selezionare l'immagine da valutare.

## <a name="next-steps"></a>Passaggi successivi

Per scoprire altri modi per esportare e usare un modello del Servizio visione artificiale personalizzato, vedere i documenti seguenti:

* [Esportare il modello](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Use exported Tensorflow model in an Android application](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) (Usare il modello Tensorflow esportato in un'applicazione Android)
* [Use exported CoreML model in a Swift iOS application](https://go.microsoft.com/fwlink/?linkid=857726) (Usare il modello CoreML esportato in un'applicazione iOS Swift)
* [Use exported CoreML model in an iOS application with Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) (Usare il modello CoreML esportato in un'applicazione iOS con Xamarin)

Per altre informazioni sull'uso di modelli ONNX con Windows ML, vedere il documento [Integrare un modello nella tua app con Windows ML](/windows/ai/windows-ml/integrate-model).
