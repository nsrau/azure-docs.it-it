---
title: Informazioni su Visione personalizzata
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare il servizio Visione personalizzata di Azure per creare nel cloud di Azure classificatori di immagini e rilevatori di oggetti di intelligenza artificiale personalizzati.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 09/14/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: riconoscimento delle immagini, identificatore di immagini, app per il riconoscimento delle immagini, visione personalizzata
ms.openlocfilehash: 47227a60b1ed45499afdb42fbc062abc5ae51ff9
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605463"
---
# <a name="what-is-custom-vision"></a>Informazioni su Visione personalizzata

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Visione personalizzata di Azure è un servizio di riconoscimento delle immagini che consente di creare, distribuire e migliorare identificatori di immagini personalizzati. Un identificatore di immagini applica etichette (che rappresentano classi oppure oggetti) alle immagini, in base alle relative caratteristiche visive. A differenza del servizio [Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), il servizio Visione personalizzata consente di specificare le etichette e di eseguire il training di modelli personalizzati per il rilevamento.

## <a name="what-it-does"></a>Funzione

Il servizio Visione personalizzata usa un algoritmo di Machine Learning per analizzare le immagini. Lo sviluppatore invia gruppi di immagini in cui sono presenti o assenti le caratteristiche in questione, etichettando personalmente le immagini al momento dell'invio. L'algoritmo esegue quindi il training in base a questi dati e calcola la propria accuratezza usando le stesse immagini per testare il proprio funzionamento. Dopo il training dell'algoritmo, è possibile testarlo, ripeterne il training e infine usarlo nell'app per il riconoscimento delle immagini per classificare nuove immagini. È anche possibile esportare il modello per usarlo offline.

### <a name="classification-and-object-detection"></a>Classificazione e rilevamento degli oggetti

È possibile dividere la funzionalità di Visione personalizzata in due categorie. La **classificazione delle immagini** applica una o più etichette a un'immagine. Il **rilevamento degli oggetti** è simile, ma restituisce anche le coordinate nell'immagine in cui si trovano le etichette applicate.

### <a name="optimization"></a>Optimization

Il servizio Visione personalizzata è ottimizzato in modo da riconoscere rapidamente le principali differenze tra immagini, quindi è possibile iniziare a creare un prototipo del modello con una piccola quantità di dati. Per iniziare sono in genere sufficienti 50 immagini. Il servizio non è ideale per il rilevamento di lievi differenze nelle immagini, ad esempio per il rilevamento di piccole crepe o ammaccature negli scenari di controllo di qualità.

È inoltre possibile scegliere tra diverse varianti dell'algoritmo di Visione personalizzata, ottimizzate per immagini con determinati soggetti, ad esempio luoghi di interesse o articoli in vendita al dettaglio. Per altre informazioni, vedere la guida [Come creare un classificatore](getting-started-build-a-classifier.md) o [Come creare un rilevatore di oggetti](get-started-build-detector.md).

## <a name="what-it-includes"></a>Cosa include

Il servizio Visione personalizzata è disponibile come set di SDK nativi, nonché come interfaccia basata sul Web nel [sito Web di Visione personalizzata](https://customvision.ai/). È possibile creare e testare un modello ed eseguirne il training usando una delle due interfacce o entrambe.

![Sito Web di Visione personalizzata in una finestra del browser Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Visione personalizzata devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Vedere la guida [Come creare un classificatore](getting-started-build-a-classifier.md) per iniziare a usare Visione personalizzata nel portale Web oppure completare una [guida di avvio rapido con la libreria client](quickstarts/image-classification.md) per implementare gli scenari di base nel codice.
