---
title: Documentazione di Swagger - Servizi Voce
titleSuffix: Azure Cognitive Services
description: La documentazione di Swagger consente di generare automaticamente gli SDK per diversi linguaggi di programmazione. Tutte le operazioni nel servizio sono supportate da Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 04/12/2019
ms.author: erhopf
ms.openlocfilehash: 6cf3ab6480900aa763598120e6ff7e308f5044e1
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743214"
---
# <a name="swagger-documentation"></a>Documentazione di Swagger

I servizi Voce offrono una specifica di Swagger per interagire con alcune API REST usate per importare dati, creare modelli, testare l'accuratezza dei modelli, creare endpoint personalizzati, inserire in coda trascrizioni batch e gestire sottoscrizioni. La maggior parte delle operazioni disponibili tramite il Portale del Servizio riconoscimento vocale personalizzato possono essere completate a livello di codice usando queste API. 

> [!NOTE]
> Sia le operazioni di riconoscimento vocale che quelle di sintesi vocale sono supportate e disponibili come API REST, documentate nella specifica di Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Generazione di codice dalla specifica di Swagger

La [specifica di Swagger](https://cris.ai/swagger/ui/index) include opzioni che consentono di testare rapidamente diversi percorsi. A volte è tuttavia preferibile generare il codice per tutti i percorsi, creando una singola libreria di chiamate su cui poter basare le soluzioni future. Verrà ora illustrato il processo per generare una libreria Python.

Sarà necessario impostare Swagger sulla stessa area della sottoscrizione del servizio Voce. È possibile verificare l'area nella risorsa dei servizi Voce del portale di Azure. Per un elenco completo di aree supportate, vedere [Aree](regions.md).

1. Passare a https://editor.swagger.io.
2. Fare clic su **File** e quindi su **Import** (Importa).
3. Immettere l'URL di Swagger che include l'area della sottoscrizione dei servizi Voce `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Fare clic su **Generate Client** (Genera client) e selezionare Python
5. Salvare la libreria client

È possibile usare la libreria Python generata con gli [esempi di servizi Voce in GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Documentazione di riferimento

* [REST (Swagger): Trascrizione e personalizzazione batch](https://westus.cris.ai/swagger/ui/index)
* [API REST: Riconoscimento vocale](rest-speech-to-text.md)
* [API REST: Sintesi vocale](rest-text-to-speech.md)

## <a name="next-steps"></a>Passaggi successivi

* [Speech Services samples on GitHub (Esempi di servizi Voce in GitHub)](https://aka.ms/csspeech/samples).
* [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
