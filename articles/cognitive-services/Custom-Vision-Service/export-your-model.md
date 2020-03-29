---
title: Esportare il modello per l'uso nei dispositivi mobili - Servizio visione artificiale personalizzato
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come esportare il modello per l'uso nella creazione di applicazioni per dispositivi mobili o per l'esecuzione in locale per la classificazione in tempo reale.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718959"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Esportare il modello per l'uso con i dispositivi mobili

Il Servizio visione artificiale personalizzato consente di esportare i classificatori per l'esecuzione offline. È possibile incorporare un classificatore esportato in un'applicazione ed eseguirlo in locale in un dispositivo per la classificazione in tempo reale.

## <a name="export-options"></a>Opzioni di esportazione

Servizio visione artificiale personalizzato supporta le esportazioni seguenti:

* __Tensorflow__ per __Android__.
* __CoreML__ per __iOS11__.
* __ONNX__ per __Windows ML__.
* __[Vision AI Developer Kit](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* Contenitore __Docker__ per l'architettura Windows, Linux o ARM. Il contenitore include un modello Tensorflow e un codice di servizio per usare l'API di visione personalizzata.

> [!IMPORTANT]
> Servizio visione artificiale personalizzato esporta solo domini __compatti__. I modelli generati dai domini compatti sono ottimizzati per i vincoli di classificazione in tempo reale nei dispositivi mobili. I classificatori creati con un dominio compatto possono essere leggermente meno precisi rispetto a un dominio standard con la stessa quantità di dati di training.
>
> Per informazioni su come migliorare i classificatori, vedere il documento [Improving your classifier](getting-started-improving-your-classifier.md) (Miglioramento di un classificatore).

## <a name="convert-to-a-compact-domain"></a>Eseguire la conversione in un dominio compatto

> [!NOTE]
> I passaggi descritti in questa sezione si applicano solo se si dispone di un modello esistente non impostato per il dominio compatto.

Per convertire il dominio di un modello esistente, attenersi alla seguente procedura:

1. Nel [sito Web Visione personalizzata](https://customvision.ai)selezionare l'icona __Home__ per visualizzare un elenco dei progetti.

    ![Immagine dell'icona Home ed elenco dei progetti](./media/export-your-model/projects-list.png)

1. Selezionare un progetto e quindi selezionare l'icona a forma di __ingranaggio__ in alto a destra nella pagina.

    ![Immagine dell'icona a forma di ingranaggio](./media/export-your-model/gear-icon.png)

1. Nella sezione __Domini__ selezionare uno dei domini __compatti.__ Selezionare __Salva modifiche__ per salvare le modifiche. 

    > [!NOTE]
    > Per Vision AI Dev Kit, il progetto deve essere creato con il dominio __Generale (Compact)__ ed è necessario specificare l'opzione **Vision AI Dev Kit** nella sezione Funzionalità di **esportazione.**

    ![Immagine della selezione dei domini](./media/export-your-model/domains.png)

1. Nella parte superiore della pagina fare clic sul __pulsante__relativo al training per ripetere il training usando il nuovo dominio.

## <a name="export-your-model"></a>Esportare il modello

Per esportare il modello dopo la ripetizione del training, seguire questa procedura:

1. Passare alla scheda **Prestazioni** e selezionare __Esporta__. 

    ![Immagine dell'icona di esportazione](./media/export-your-model/export.png)

    > [!TIP]
    > Se la voce __Esporta__ non è disponibile, l'iterazione selezionata non usa un dominio compatto. Usare la sezione relativa alle__iterazioni__ di questa pagina per selezionare un'iterazione che usa un dominio compatto e quindi selezionare __Esporta__.

1. Selezionare il formato di esportazione desiderato, quindi selezionare __Esporta__ per scaricare il modello.

## <a name="next-steps"></a>Passaggi successivi

Integrare il modello esportato in un'applicazione esplorando uno degli articoli o degli esempi seguenti:Integrate your exported model into an application by exploring one of the following articles or samples:

* [Usa il tuo modello Tensorflow con Python](export-model-python.md)
* [Usare il modello ONNX con Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Vedere l'esempio per il [modello CoreML in un'applicazione iOS](https://go.microsoft.com/fwlink/?linkid=857726) per la classificazione delle immagini in tempo reale con Swift.See the sample for CoreML model in an iOS application for real-time image classification with Swift.
* Vedere l'esempio per il [modello Tensorflow in un'applicazione Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) per la classificazione delle immagini in tempo reale in Android.See the sample for Tensorflow model in an Android application for real-time image classification on Android.
* Vedere l'esempio per il modello CoreML con Xamarin per la classificazione delle immagini in tempo reale in un'app Xamarin iOS.See the sample for [CoreML model with Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) for real-time image classification in a Xamarin iOS app.
