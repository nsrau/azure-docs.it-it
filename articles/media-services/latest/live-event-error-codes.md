---
title: Codici di errore dell'evento live di servizi multimediali di Azure | Microsoft Docs
description: Questo articolo elenca i codici di errore dell'evento Live.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599470"
---
# <a name="media-services-live-event-error-codes"></a>Codici di errore dell'evento live di servizi multimediali

La tabella seguente elenca i codici di errore dell' [evento Live](live-events-outputs-concept.md) :

|Errore|Descrizione|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|Questo errore si verifica quando il codificatore in ingresso invia flussi che superano 30 fps per la codifica di eventi/canali live.|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|Questo errore si verifica quando il codificatore in ingresso invia flussi che superano le risoluzioni seguenti: 1920X1088 per la codifica di eventi/canali live e 4096 x 2160 per gli eventi/canali live pass-through.|

## <a name="see-also"></a>Vedere anche

[Codici di errore dell'endpoint di streaming (Origin)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: flusso live con servizi multimediali](stream-live-tutorial-with-api.md)
