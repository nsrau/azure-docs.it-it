---
title: Esportare il modello per l'uso nei dispositivi mobili - Servizio visione artificiale personalizzato
titleSuffix: Azure Cognitive Services
description: Informazioni su come esportare il modello per l'uso nella creazione di applicazioni per dispositivi mobili.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 554a392a7f815a6e646927f137b1e6c2856099bd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561085"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Esportare il modello per l'uso con i dispositivi mobili

Il Servizio visione artificiale personalizzato consente di esportare i classificatori per l'esecuzione offline. È possibile incorporare un classificatore esportato in un'applicazione ed eseguirlo in locale in un dispositivo per la classificazione in tempo reale.

Servizio visione artificiale personalizzato supporta le esportazioni seguenti:

* __Tensorflow__ per __Android__.
* __CoreML__ per __iOS11__.
* __ONNX__ per __Windows ML__.
* Un __contenitore__ Windows o Linux. Il contenitore include un modello Tensorflow e il codice del servizio, per usare l'API di Servizio visione artificiale personalizzato. 

> [!IMPORTANT]
> Servizio visione artificiale personalizzato esporta solo domini __compatti__. I modelli generati dai domini compatti sono ottimizzati per i vincoli di classificazione in tempo reale nei dispositivi mobili. I classificatori creati con un dominio compatto possono essere leggermente meno precisi rispetto a un dominio standard con la stessa quantità di dati di training.
>
> Per informazioni su come migliorare i classificatori, vedere il documento [Improving your classifier](getting-started-improving-your-classifier.md) (Miglioramento di un classificatore).

## <a name="convert-to-a-compact-domain"></a>Eseguire la conversione in un dominio compatto

> [!NOTE]
> I passaggi descritti in questa sezione si applicano solo se si ha un classificatore esistente non impostato su un dominio compatto.

Per convertire il dominio di un classificatore esistente, seguire questa procedura:

1. Nella [pagina di Servizio visione artificiale personalizzato](https://customvision.ai) selezionare l'icona __Home__ per visualizzare un elenco dei progetti. È anche possibile usare la pagina [https://customvision.ai/projects](https://customvision.ai/projects) per visualizzare i progetti.

    ![Immagine dell'icona Home ed elenco dei progetti](./media/export-your-model/projects-list.png)

2. Selezionare un progetto e quindi selezionare l'icona a forma di __ingranaggio__ in alto a destra nella pagina.

    ![Immagine dell'icona a forma di ingranaggio](./media/export-your-model/gear-icon.png)

3. Nella sezione __Domini__ selezionare un dominio __compatto__. Selezionare __Salva modifiche__ per salvare le modifiche.

    ![Immagine della selezione dei domini](./media/export-your-model/domains.png)

4. Nella parte superiore della pagina fare clic sul __pulsante__relativo al training per ripetere il training usando il nuovo dominio.

## <a name="export-your-model"></a>Esportare il modello

Per esportare il modello dopo la ripetizione del training, seguire questa procedura:

1. Passare alla scheda **Prestazioni** e selezionare __Esporta__. 

    ![Immagine dell'icona di esportazione](./media/export-your-model/export.png)

    > [!TIP]
    > Se la voce __Esporta__ non è disponibile, l'iterazione selezionata non usa un dominio compatto. Usare la sezione relativa alle__iterazioni__ di questa pagina per selezionare un'iterazione che usa un dominio compatto e quindi selezionare __Esporta__.

2. Selezionare il formato di esportazione e quindi selezionare __Esporta__ per scaricare il modello.

## <a name="next-steps"></a>Passaggi successivi

Per integrare il modello esportato in un'applicazione, esplorare uno degli articoli o degli esempi seguenti:

* [Usare il modello Tensorflow con Python](export-model-python.md)
* [Usare il modello ONNX con Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Vedere l'esempio per il [modello CoreML in un'applicazione iOS](https://go.microsoft.com/fwlink/?linkid=857726) per la classificazione delle immagini in tempo reale con Swift.
* Vedere l'esempio per il [modello Tensorflow in un'applicazione Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) per la classificazione delle immagini in tempo reale in Android.
* Vedere l'esempio per il [modello CoreML con Novell](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) per la classificazione delle immagini in tempo reale in un'app per iOS Novell.
