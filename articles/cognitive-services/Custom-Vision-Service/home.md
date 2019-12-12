---
title: Informazioni su Visione personalizzata
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare il servizio Visione personalizzata per creare classificatori di immagini personalizzati nel cloud di Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 829921ae5600873fdc8f2946ae3ff355bffb06a8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978595"
---
# <a name="what-is-custom-vision"></a>Informazioni su Visione personalizzata

Visione personalizzata è un servizio cognitivo che consente di compilare, distribuire e migliorare classificatori di immagini. Un classificatore di immagini è un servizio di intelligenza artificiale che applica etichette (che rappresentano _classi_) alle immagini, in base alle relative caratteristiche visive. A differenza del servizio [Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), il servizio Visione personalizzata consente di determinare le etichette da applicare.

## <a name="what-it-does"></a>Risultato

Il servizio Visione personalizzata usa un algoritmo di Machine Learning per applicare etichette alle immagini. Lo sviluppatore deve inviare gruppi di immagini in cui sono presenti o assenti le caratteristiche in questione, etichettando personalmente le immagini al momento dell'invio. L'algoritmo esegue quindi il training in base a questi dati e calcola la propria accuratezza usando le stesse immagini per testare il proprio funzionamento. Dopo il training dell'algoritmo, è possibile testarlo, ripeterne il training e infine usarlo per classificare nuove immagini in base alle esigenze dell'app. È anche possibile esportare il modello per usarlo offline.

### <a name="classification-and-object-detection"></a>Classificazione e rilevamento degli oggetti

È possibile dividere la funzionalità di Visione personalizzata in due categorie. La **classificazione delle immagini** applica una o più etichette a un'immagine. Il **rilevamento degli oggetti** è simile, ma restituisce anche le coordinate nell'immagine in cui si trovano le etichette applicate.

### <a name="optimization"></a>Ottimizzazione

Il servizio Visione personalizzata è ottimizzato in modo da riconoscere rapidamente le principali differenze tra immagini, quindi è possibile iniziare a creare un prototipo del modello con una piccola quantità di dati. Per iniziare sono in genere sufficienti 50 immagini. Il servizio non è ideale per il rilevamento di lievi differenze nelle immagini, ad esempio per il rilevamento di piccole crepe o ammaccature negli scenari di controllo di qualità.

È inoltre possibile scegliere tra diverse varianti dell'algoritmo di Visione personalizzata, ottimizzate per immagini con determinati soggetti, ad esempio luoghi di interesse o articoli in vendita al dettaglio. Per altre informazioni, vedere la guida [Creare un classificatore](getting-started-build-a-classifier.md).

## <a name="what-it-includes"></a>Cosa include

Il servizio Visione personalizzata è disponibile come set di SDK nativi, nonché come interfaccia basata sul Web nella [home page di Visione personalizzata](https://customvision.ai/). È possibile creare e testare un modello ed eseguirne il training usando una delle due interfacce o entrambe.

![Home page di Visione personalizzata in una finestra del browser Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Visione personalizzata devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Vedere la guida [Creare un classificatore](getting-started-build-a-classifier.md) per iniziare a usare Visione personalizzata sul Web oppure completare un'[esercitazione sulla classificazione delle immagini](csharp-tutorial.md) per implementare uno scenario di base nel codice.
