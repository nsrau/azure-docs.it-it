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
ms.openlocfilehash: dd9ac9654f916653af974e816485630423466ae5
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815436"
---
# <a name="swagger-documentation"></a>Documentazione di Swagger

Il servizio riconoscimento vocale offre una specifica di spavalderia per interagire con alcune API REST usate per importare dati, creare modelli, testare l'accuratezza del modello, creare endpoint personalizzati, accodare trascrizioni batch e gestire le sottoscrizioni. La maggior parte delle operazioni disponibili tramite il Portale del Servizio riconoscimento vocale personalizzato possono essere completate a livello di codice usando queste API.

> [!NOTE]
> Sia le operazioni di riconoscimento vocale che quelle di sintesi vocale sono supportate e disponibili come API REST, documentate nella specifica di Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Generazione di codice dalla specifica di Swagger

La [specifica di Swagger](https://cris.ai/swagger/ui/index) include opzioni che consentono di testare rapidamente diversi percorsi. A volte è tuttavia preferibile generare il codice per tutti i percorsi, creando una singola libreria di chiamate su cui poter basare le soluzioni future. Verrà ora illustrato il processo per generare una libreria Python.

È necessario impostare spavalderia sulla stessa area della sottoscrizione del servizio di riconoscimento vocale. È possibile confermare l'area nel portale di Azure nella risorsa del servizio di riconoscimento vocale. Per un elenco completo di aree supportate, vedere [Aree](regions.md).

1. Vai a https://editor.swagger.io
2. Fare clic su **File** e quindi su **Import** (Importa).
3. Immettere l'URL di spavalderia, inclusa l'area per la sottoscrizione del servizio vocale `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Fare clic su **Generate Client** (Genera client) e selezionare Python
5. Salvare la libreria client

È possibile usare la libreria Python generata con gli esempi di [servizio vocale su GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Documenti di riferimento

* [REST (spavalderia): trascrizione e personalizzazione di batch](https://westus.cris.ai/swagger/ui/index)
* [API REST: sintesi vocale](rest-speech-to-text.md)
* [API REST: sintesi vocale](rest-text-to-speech.md)

## <a name="next-steps"></a>Passaggi successivi

* [Esempi di servizi vocali su GitHub](https://aka.ms/csspeech/samples).
* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
