---
title: Documentazione Swagger - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La documentazione di Swagger consente di generare automaticamente gli SDK per diversi linguaggi di programmazione. Tutte le operazioni nel servizio sono supportate da Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fcc43caf895dadfaf832a47c3254f9b828bcb71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77430806"
---
# <a name="swagger-documentation"></a>Documentazione di Swagger

Il servizio di riconoscimento vocale offre una specifica Swagger per interagire con una manciata di API REST usate per importare dati, creare modelli, testare l'accuratezza del modello, creare endpoint personalizzati, accodare trascrizioni batch e gestire sottoscrizioni. La maggior parte delle operazioni disponibili tramite il Portale del Servizio riconoscimento vocale personalizzato possono essere completate a livello di codice usando queste API.

> [!NOTE]
> Sia le operazioni di riconoscimento vocale che quelle di sintesi vocale sono supportate e disponibili come API REST, documentate nella specifica di Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Generazione di codice dalla specifica di Swagger

La [specifica di Swagger](https://cris.ai/swagger/ui/index) include opzioni che consentono di testare rapidamente diversi percorsi. A volte è tuttavia preferibile generare il codice per tutti i percorsi, creando una singola libreria di chiamate su cui poter basare le soluzioni future. Verrà ora illustrato il processo per generare una libreria Python.

È necessario impostare Swagger sulla stessa area dell'abbonamento al servizio di riconoscimento vocale. È possibile confermare l'area nel portale di Azure nella risorsa del servizio di riconoscimento vocale. Per un elenco completo delle aree supportate, vedere [Aree](regions.md).

1. Passare a https://editor.swagger.io.
2. Fare clic su **File** e quindi su **Import** (Importa).
3. Immettere l'URL swagger che include l'area per l'abbonamento al servizio di riconoscimento vocale`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Fare clic su **Genera client** e selezionare Python
5. Salvare la libreria client

È possibile utilizzare la libreria Python generata con gli esempi del [servizio di riconoscimento vocale in GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Documentazione di riferimento

* [REST (Swagger): trascrizione e personalizzazione del batch](https://westus.cris.ai/swagger/ui/index)
* [API REST: sintesi vocale: sintesi vocale](rest-speech-to-text.md)
* [API REST: sintesi vocale](rest-text-to-speech.md)

## <a name="next-steps"></a>Passaggi successivi

* [Esempi del servizio di riconoscimento vocale in GitHub](https://aka.ms/csspeech/samples).
* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
