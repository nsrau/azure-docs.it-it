---
title: Informazioni sul Servizio di riconoscimento vocale (anteprima)
description: 'Il servizio Voce, parte dei Servizi Cognitivi Microsoft, unisce diversi servizi vocali di Azure precedentemente disponibili in modo separato: Riconoscimento vocale Bing (che comprende il riconoscimento vocale e la sintesi vocale), Riconoscimento vocale personalizzato e Traduzione vocale.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 0cd6d984ac9329112aa388e8d8ee808d4c3e6227
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445635"
---
# <a name="what-is-the-speech-service-preview"></a>Informazioni sul Servizio di riconoscimento vocale (anteprima)

Il servizio Voce si basa sulle tecnologie utilizzate in altri prodotti Microsoft, tra cui Cortana e Microsoft Office.  Questo stesso servizio è disponibile a tutti i clienti come servizio cognitivo. 

> [!NOTE]
> Il servizio Voce è attualmente disponibile in anteprima pubblica. Si consiglia di consultare regolarmente questa pagina per aggiornamenti della documentazione, esempi di codici aggiuntivi e altro.

Con una sola sottoscrizione, il servizio Voce offre agli sviluppatori un modo semplice per dotare le loro applicazioni di potenti funzioni abilitate per la sintesi vocale. Le applicazioni possono ora offrire comandi vocali, trascrizione, dettatura, sintesi vocale e traduzione.

## <a name="speech-service-features"></a>Funzionalità del servizio Voce

|Funzione|DESCRIZIONE|
|-|-|
|[Riconoscimento vocale](speech-to-text.md)| Trascrive i flussi audio in testo che l'applicazione può accettare come input. Si integra anche con il servizio [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) per captare la finalità dell'utente dalle parole.|
|[Sintesi vocale](text-to-speech.md)| Converte il testo normale in parlato dal suono naturale che viene trasmesso all'applicazione in un file audio. Sono disponibili più voci, che variano per genere o accento, per molte lingue supportate. |
|[Traduzione vocale](speech-translation.md)| Può essere usata per traslare i flussi audio in tempo quasi reale o elaborare la voce registrata. |
|[Speech Devices SDK](speech-devices-sdk.md)| Con l'introduzione del Servizio di riconoscimento vocale unificato, Microsoft e i suoi partner offrono una piattaforma hardware/software integrata ottimizzata per lo sviluppo di dispositivi con funzioni vocali |

## <a name="using-the-speech-service"></a>Utilizzo del Servizio di riconoscimento vocale

Il servizio di riconoscimento vocale viene reso disponibile in due modi. [L'SDK](speech-sdk.md) elimina i dettagli dei protocolli di rete. L’[API REST](rest-apis.md) funziona con qualsiasi linguaggio di programmazione, ma non offre tutte le funzioni offerte dall’SDK.

|<br>Metodo|Sintesi vocale<br>Riconoscimento vocale|Sintesi vocale<br>Sintesi vocale|Sintesi vocale<br>Traduzione|<br>DESCRIZIONE|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|Yes|No |Yes|Librerie per determinati linguaggi di programmazione che semplificano lo sviluppo.|
|[REST](rest-apis.md)|Yes|Yes|No |Un'API semplice basato su HTTP che rende più facile aggiungere comandi vocali all'applicazione.|

## <a name="next-steps"></a>Passaggi successivi

Ottenere una versione di prova gratuita della chiave di sottoscrizione per il servizio Voce.

> [!div class="nextstepaction"]
> [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md)
