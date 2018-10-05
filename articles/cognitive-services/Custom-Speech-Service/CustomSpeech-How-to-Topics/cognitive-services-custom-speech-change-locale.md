---
title: Impostazioni locali e lingue supportate - Servizio di riconoscimento vocale personalizzato
titlesuffix: Azure Cognitive Services
description: Panoramica delle lingue supportate del Servizio di riconoscimento vocale personalizzato in Servizi cognitivi.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 34af6673689244364ab3a1fe3f2a6ab056ea8598
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223956"
---
# <a name="supported-locales-in-custom-speech-service"></a>Impostazioni locali supportate nel Servizio di riconoscimento vocale personalizzato
Il Servizio di riconoscimento vocale personalizzato supporta attualmente la personalizzazione dei modelli nelle impostazioni locali seguenti:

| Tipo di modello | Supporto per le lingue |
|----|-----|
| Modelli acustici | Inglese Stati Uniti (en-US) |
| Modelli linguistici | Inglese Stati Uniti (en-US), cinese (zh-CN) |

Benché la personalizzazione del modello acustico sia supportata solo nella lingua inglese (Stati Uniti), l'importazione di dati acustici in cinese è supportata ai fini del test offline dei modelli linguistici personalizzati in lingua cinese.

Prima di intraprendere qualsiasi azione, è necessario selezionare le impostazioni locali appropriate. Le impostazioni locali correnti sono indicate nel titolo della tabella in tutti i dati, modelli e pagine della distribuzione. Per modificare le impostazioni locali, fare clic sul pulsante "Change Locale" (Modifica impostazioni locali) sotto il titolo della tabella. Verrà visualizzata una pagina di conferma delle impostazioni locali. Fare clic su "OK" per tornare alla tabella.

Si dovrebbe continuare con i passaggi successivi.
* Imparare a [creare un modello acustico personalizzato](cognitive-services-custom-speech-create-acoustic-model.md) per migliorare la precisione del riconoscimento
* Imparare a [creare un modello linguistico personalizzato](cognitive-services-custom-speech-create-language-model.md) per migliorare la percentuale del riconoscimento
* Seguire le [indicazioni sulla trascrizione](cognitive-services-custom-speech-transcription-guidelines.md) per preparare i dati
