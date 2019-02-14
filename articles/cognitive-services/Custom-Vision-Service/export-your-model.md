---
title: Esportare il modello per l'uso nei dispositivi mobili - Servizio visione artificiale personalizzato
titlesuffix: Azure Cognitive Services
description: Informazioni su come esportare il modello per l'uso nella creazione di applicazioni per dispositivi mobili.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 080a4691869c4deaf86f0e27961e80b60d439f05
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857246"
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

Integrare il modello esportato in un'applicazione. Sono disponibili diverse applicazioni di esempio:

* Esempio per l'[uso del modello CoreML esportato in un'applicazione iOS](https://go.microsoft.com/fwlink/?linkid=857726) per la classificazione di immagini in tempo reale con Swift
* Applicazione iOS di esempio per l'[uso del modello CoreML esportato con Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) per la classificazione di immagini in tempo reale 
* Esempio per l'[uso del modello Tensorflow esportato in un'applicazione Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) per la classificazione di immagini in tempo reale con Swift 
* [Uso del modello Tensorflow con Windows](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-model-python)
* Esempio per l'[uso del modello ONNX esportato con Windows Machine Learning](https://azure.microsoft.com/resources/samples/cognitive-services-onnx-customvision-sample/)
