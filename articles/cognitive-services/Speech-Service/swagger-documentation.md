---
title: Documentazione di spavalderia-servizio riconoscimento vocale
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
ms.openlocfilehash: 232435a424d2461bce4598356a986473cb1d3644
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552579"
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
