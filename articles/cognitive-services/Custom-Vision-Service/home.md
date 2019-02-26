---
title: Informazioni su Visione personalizzata di Azure
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare il servizio Visione personalizzata per creare classificatori di immagini personalizzati nel cloud di Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 6cbc6e351147ed5b4c31463b5cf319417f34da34
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456776"
---
# <a name="what-is-azure-custom-vision"></a>Informazioni su Visione personalizzata di Azure

Visione personalizzata di Azure è un servizio cognitivo che consente di creare, distribuire e migliorare classificatori di immagini. Un classificatore di immagini è un servizio di intelligenza artificiale che applica etichette (che rappresentano _classi_) alle immagini, in base alle relative caratteristiche visive. A differenza del servizio [Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), il servizio Visione personalizzata consente di determinare le etichette da applicare.

## <a name="what-it-does"></a>Risultato

Il servizio Visione personalizzata usa un algoritmo di Machine Learning per applicare etichette alle immagini. Lo sviluppatore deve inviare gruppi di immagini in cui sono presenti o assenti le caratteristiche in questione, etichettando personalmente le immagini al momento dell'invio. L'algoritmo esegue quindi il training in base a questi dati e calcola la propria accuratezza usando le stesse immagini per testare il proprio funzionamento. Dopo il training dell'algoritmo, è possibile testarlo, ripeterne il training e infine usarlo per classificare nuove immagini in base alle esigenze dell'app. È anche possibile esportare il modello per usarlo offline.

## <a name="classification-and-object-detection"></a>Classificazione e rilevamento degli oggetti

È possibile dividere la funzionalità di Visione personalizzata in due categorie. La **classificazione delle immagini** applica una o più etichette a un'immagine. Il **rilevamento degli oggetti** è simile, ma restituisce anche le coordinate nell'immagine in cui si trovano le etichette applicate.

## <a name="optimization"></a>Ottimizzazione

Il servizio Visione personalizzata è ottimizzato per riconoscere rapidamente le differenze principali tra le immagini. È così possibile iniziare rapidamente a creare prototipi del modello con una quantità ridotta di dati. Per iniziare sono in genere sufficienti 50 immagini. Questo significa, però, che il servizio non è ideale per il rilevamento di lievi differenze nelle immagini, ad esempio per il rilevamento di piccole crepe o ammaccature negli scenari di controllo di qualità.

È inoltre possibile scegliere tra diverse varianti dell'algoritmo di Visione personalizzata, ottimizzate per immagini con determinati soggetti, ad esempio luoghi di interesse o articoli in vendita al dettaglio. Per maggiori informazioni, vedere [Come creare un classificatore con Visione personalizzata](getting-started-build-a-classifier.md).

## <a name="what-it-includes"></a>Cosa include
Il servizio Visione personalizzata è disponibile come set di SDK nativi, nonché come interfaccia basata sul Web nella [home page di Visione personalizzata](https://customvision.ai/). È possibile creare e testare un modello ed eseguirne il training usando una delle due interfacce o entrambe.

![Home page di Visione personalizzata in una finestra del browser Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Visione personalizzata devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Vedere la guida [Creare un classificatore](getting-started-build-a-classifier.md) per iniziare a usare Visione personalizzata sul Web oppure completare un'[esercitazione sulla classificazione delle immagini](csharp-tutorial.md) per implementare uno scenario di base nel codice.
