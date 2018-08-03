---
title: Traduzione vocale tramite i servizi di riconoscimento vocale | Microsoft Docs
description: Di seguito viene descritto come usare la traduzione vocale nel servizio Voce.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 6acfcf0ae8ab4c63e6cc943f93da6b947f3d118c
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071097"
---
# <a name="translate-speech-using-speech-service"></a>Traduzione vocale tramite servizio Voce

[Speech SDK](speech-sdk.md)traduzione vocale nell'applicazione. L’SDK fornisce la funzionalità completa del servizio. Il processo di base per l'esecuzione della traduzione vocale include i passaggi seguenti:

1. Creare una factory di riconoscimento vocale e fornire una chiave di sottoscrizione al servizio Voce e un'[area](regions.md)o un token di autorizzazione. A questo punto si configura la lingua di partenza e la lingua di arrivo della traduzione e si specifica se si desidera un testo o una sintesi vocale.

2. Ottenere un sistema di riconoscimento dalla factory. Per la conversione, selezionare un sistema di riconoscimento della traduzione. (Gli altri sistemi di riconoscimento sono per *Riconoscimento vocale*) Esistono diverse varianti di sistemi di riconoscimento di traduzione in base all'origine audio in uso.

4. Collegare gli eventi per il funzionamento asincrono, se desiderato. Il sistema di riconoscimento chiama i gestori dell'evento quando hanno risultati intermedi e finali. In caso contrario, l'applicazione riceve un risultato finale di traduzione.

5. Avviare il riconoscimento e la traduzione.

# <a name="sdk-samples"></a>Esempi di SDK

Per il set di esempi più recente, vedere il [repository GitHub degli esempi di Speech SDK di Servizi cognitivi](https://aka.ms/csspeech/samples).

# <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
