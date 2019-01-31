---
title: Impostazioni locali e lingue supportate nel Servizio di riconoscimento vocale personalizzato in Azure | Microsoft Docs
description: Panoramica delle lingue supportate del Servizio di riconoscimento vocale personalizzato in Servizi cognitivi.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: e5c1f7d9648f404512f366be4f4d16ea0cb0f778
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217119"
---
# <a name="supported-locales-in-custom-speech-service"></a>Impostazioni locali supportate nel Servizio di riconoscimento vocale personalizzato

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

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
