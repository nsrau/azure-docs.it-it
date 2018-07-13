---
title: Informazioni generali su Machine Learning del Servizio visione artificiale personalizzato - Servizi cognitivi di Azure | Microsoft Docs
description: Il Servizio visione artificiale personalizzato è un servizio cognitivo di Microsoft che consente di creare classificatori di immagine personalizzati nella piattaforma di Azure.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: de45fc399470a806fb7456cbbe936cecf659ee7c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37087682"
---
# <a name="what-is-the-custom-vision-service"></a>Informazioni sul Servizio visione artificiale personalizzato

Il Servizio visione artificiale personalizzato è un servizio cognitivo di Microsoft che consente di creare classificatori di immagine personalizzati. Consente di creare, distribuire e migliorare un classificatore di immagini in modo semplice e rapido. Il Servizio visione artificiale personalizzato offre un'API REST e un'interfaccia Web per caricare le immagini ed eseguire il training del classificatore.

## <a name="what-does-custom-vision-service-do-well"></a>Quando è particolarmente utile il Servizio visione artificiale personalizzato?

Il Servizio visione artificiale personalizzato è particolarmente utile quando l'elemento da classificare è prominente nell'immagine. 

Per creare un classificatore o un rilevatore sono sufficienti poche immagini. 50 immagini per ogni classe sono sufficienti per iniziare il prototipo. Il Servizio visione artificiale personalizzato usa metodi che supportano le differenze e consente di iniziare la creazione di prototipi con una quantità di dati ridotta. Ciò significa che il Servizio visione artificiale personalizzato non è adatto agli scenari in cui si vuole rilevare differenze minime. Ad esempio, in caso di ammaccature o fratture minime negli scenari di controllo qualità.

## <a name="release-notes"></a>Note sulla versione

### <a name="may-7-2018"></a>7 maggio 2018
- È stata introdotta la funzionalità Rilevamento di oggetti di anteprima per i progetti della versione di valutazione gratuita limitata.
- È stato eseguito l'aggiornamento alle API 2.0
- Il livello S0 è stato espanso fino a 250 tag e 50.000 immagini. 
- Miglioramenti di back-end significativi per la pipeline di Machine Learning per i progetti di classificazione delle immagini. Questi aggiornamenti saranno disponibili per i progetti sottoposti a training dopo il 27 aprile 2018.
- È stata aggiunta l'esportazione di modelli in ONNX, per l'uso con Machine Learning di Windows.
- È stata aggiunta l'esportazione di modelli in Dockerfile. Ciò consente di scaricare gli artefatti per la creazione di contenitori Windows o Linux personalizzati, inclusi DockerFile, un modello TensorFlow e un codice di servizio. 
- Per i modelli di cui è stato eseguito il training esportati in TensorFlow nei domini General (Compact) e Landmark (Compact), i [valori medi sono ora (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) per uniformità tra i progetti. 

### <a name="march-1-2018"></a>1 marzo 2018
- L'anteprima a pagamento è stata caricata nel portale di Azure. I progetti possono ora essere collegati alle risorse di Azure con un livello F0 (gratuito) o S0 (Standard). 50 progetti di livello S0 che consentono fino a 100 tag e 25.000 immagini. 
- Modifiche di back-end alla pipeline di Machine Learning/parametro di normalizzazione. Ciò offrirà ai clienti un migliore controllo sul compromesso precisione/richiamo quando si modifica la soglia di probabilità. Come parte di queste modifiche, il valore predefinito della soglia di probabilità nel portale CustomVision.ai è stato impostato su 50%.

### <a name="december-19-2017"></a>19 dicembre 2017

- È stata aggiunta l'esportazione in Android (TensorFlow), oltre all'esportazione già rilasciata in iOS (CoreML). Ciò consente l'esportazione di un modello compatto con training da eseguire offline in un'applicazione.
- Sono stati aggiunti i domini compatti Retail e Landmark per consentire l'esportazione dei modelli per questi domini.
- Sono state rilasciate le versioni [1.2 dell'API Training](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [1.1 dell'API Prediction](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Le API aggiornate supportano l'esportazione di modelli, è stata introdotta una nuova operazione dell'API Prediction che non salva le immagini in "Predictions" e sono state aggiunte operazioni in batch nell'API Training.
- Modifiche all'esperienza utente, inclusa la possibilità di visualizzare i domini usati per eseguire il training di un'iterazione.
- Sono stati aggiornati l'[SDK C# e l'esempio](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come creare un classificatore](getting-started-build-a-classifier.md)
