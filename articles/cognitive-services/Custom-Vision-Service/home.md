---
title: Informazioni su Visione personalizzata di Azure
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare il servizio Visione personalizzata per creare classificatori di immagini personalizzati nel cloud di Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 01/10/2019
ms.author: anroth
ms.openlocfilehash: cc60166b4105cf38d3c1f73bdb558af7a9a7c831
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857482"
---
# <a name="what-is-azure-custom-vision"></a>Informazioni su Visione personalizzata di Azure

Il servizio Visione personalizzata è un servizio cognitivo che consente di creare, distribuire e migliorare classificatori di immagini personalizzati. Un classificatore di immagini è un servizio di intelligenza artificiale che consente di ordinare le immagini in classi (tag) in base a determinate caratteristiche. A differenza del servizio [Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), il servizio Visione personalizzata consente di creare classificazioni personalizzate.

## <a name="what-it-does"></a>Risultato

Il servizio Visione personalizzata usa un algoritmo di apprendimento automatico per classificare le immagini. Lo sviluppatore deve inviare i gruppi di immagini in cui sono presenti o mancano le classificazioni in questione. I tag corretti delle immagini vengono specificati al momento dell'invio. L'algoritmo esegue il training in base a questi dati e calcola la propria accuratezza usando gli stessi dati per testare il proprio funzionamento. Dopo il training del modello, è possibile testarlo, ripeterne il training e infine usarlo per classificare nuove immagini in base alle esigenze dell'app. È anche possibile esportare il modello per usarlo offline.

### <a name="classification-and-object-detection"></a>Classificazione e rilevamento degli oggetti

È possibile dividere la funzionalità di Visione personalizzata in due categorie. **Classificazione delle immagini**, che assegna una distribuzione delle classificazioni a ogni immagine. Sono supportati i modelli di classificazione sia multiclasse (un tag per ogni immagine) che multietichetta (qualsiasi numero di tag per ogni immagine). Il **rilevamento degli oggetti** è simile alla classificazione multietichetta, ma restituisce anche le coordinate dell'immagine in cui sono presenti le etichette applicate.

### <a name="optimization"></a>Ottimizzazione

In generale, il servizio Visione personalizzata usa metodi che supportano le differenze e consente di iniziare la creazione di prototipi con una quantità ridotta di dati. Per iniziare, bastano in genere 50 immagini per tag. Questo significa, però, che il servizio non è ideale per il rilevamento di lievi differenze nelle immagini, ad esempio per il rilevamento di piccole crepe o ammaccature negli scenari di controllo di qualità.

È inoltre possibile scegliere tra diversi tipi di algoritmo di Visione personalizzata ottimizzati per determinati elementi, ad esempio punti di interesse o articoli per la vendita al dettaglio. Per maggiori informazioni, vedere [Come creare un classificatore con Visione personalizzata](getting-started-build-a-classifier.md).

## <a name="what-it-includes"></a>Cosa include
Il servizio Visione personalizzata è disponibile come set di SDK nativi, nonché come interfaccia basata sul Web nella [home page di Visione personalizzata](https://customvision.ai/). È possibile creare, testare ed eseguire il training di un modello tramite l'interfaccia o con entrambi i metodi.

![Home page di Visione personalizzata in una finestra del browser Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Visione personalizzata devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Vedere la guida [Come creare un classificatore con Visione personalizzata](getting-started-build-a-classifier.md) per iniziare a usare Visione personalizzata nel Web oppure completare un'[esercitazione di classificazione immagini](csharp-tutorial.md) per implementare lo scenario nel codice.
