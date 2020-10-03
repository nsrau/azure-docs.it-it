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
ms.openlocfilehash: 6bb50e427fa85a170c5ad23a63d67c01e898a17d
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665685"
---
# <a name="swagger-documentation"></a>Documentazione di Swagger

Il servizio riconoscimento vocale offre una specifica di spavalderia per interagire con alcune API REST usate per importare dati, creare modelli, testare l'accuratezza del modello, creare endpoint personalizzati, accodare trascrizioni batch e gestire le sottoscrizioni. La maggior parte delle operazioni disponibili tramite il Portale del Servizio riconoscimento vocale personalizzato possono essere completate a livello di codice usando queste API.

> [!NOTE]
> Sia le operazioni di riconoscimento vocale che quelle di sintesi vocale sono supportate e disponibili come API REST, documentate nella specifica di Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Generazione di codice dalla specifica di Swagger

La [specifica di Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) include opzioni che consentono di testare rapidamente diversi percorsi. A volte è tuttavia preferibile generare il codice per tutti i percorsi, creando una singola libreria di chiamate su cui poter basare le soluzioni future. Verrà ora illustrato il processo per generare una libreria Python.

È necessario impostare spavalderia sulla stessa area della sottoscrizione del servizio di riconoscimento vocale. È possibile confermare l'area nel portale di Azure nella risorsa del servizio di riconoscimento vocale. Per un elenco completo delle aree supportate, vedere [aree](regions.md).

1. In un browser passare alla specifica di spavalderia per l'area geografica:  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. In questa pagina fare clic su **definizione API**e quindi su **spavalderia**. Copiare l'URL della pagina visualizzata.
1. In un nuovo browser passare a https://editor.swagger.io
1. Fare clic su **file**, quindi su **Importa URL**, incollare l'URL e fare clic su **OK**.
1. Fare clic su **genera client** e selezionare **Python**. La libreria client viene scaricata nel computer in un `.zip` file.
1. Estrarre tutti gli elementi dal download. `tar -xf`Per estrarre tutti gli elementi, è possibile usare.
1. Installare il modulo estratto nell'ambiente Python:  
       `pip install path/to/package/python-client`
1. Il pacchetto installato è denominato `swagger_client` . Verificare che l'installazione abbia avuto esito positivo:  
       `python -c "import swagger_client"`

È possibile usare la libreria Python generata con gli esempi di [servizio vocale su GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Documentazione di riferimento

* [REST (spavalderia): trascrizione e personalizzazione di batch](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [API REST: Riconoscimento vocale](rest-speech-to-text.md)
* [API REST: Sintesi vocale](rest-text-to-speech.md)

## <a name="next-steps"></a>Passaggi successivi

* [Esempi di servizi vocali su GitHub](https://aka.ms/csspeech/samples).
* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](overview.md#try-the-speech-service-for-free)
