---
title: "Gestire la velocità e la concorrenza della codifica con Servizi multimediali di Azure | Microsoft Docs"
description: "Questo articolo illustra brevemente la procedura per gestire la velocità e la concorrenza delle attività o dei processi di codifica con Servizi multimediali di Azure."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: 0463904fd9bf1138587d0d214e572ddd38cc2184
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Gestire la velocità e la concorrenza della codifica

Questo articolo illustra brevemente la procedura per gestire la velocità e la concorrenza delle attività o dei processi di codifica.

## <a name="overview"></a>Panoramica

Nei Servizi multimediali, un **tipo di unità riservata** determina la velocità di elaborazione delle attività multimediali. È possibile scegliere uno dei seguenti tipi di unità riservata: **S1**, **S2** o **S3**. Lo stesso processo di codifica viene eseguito più velocemente quando si usa ad esempio il tipo di unità riservata **S2** rispetto al tipo **S1**. L'argomento sulle [unità di codifica della scalabilità](media-services-scale-media-processing-overview.md) mostra una tabella che consente di scegliere tra diverse velocità di codifica.

Oltre al tipo di unità riservata, è possibile specificare il provisioning dell'account con le **unità riservate**. Il numero delle unità riservate sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione quando un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

Per informazioni dettagliate ed esempi che illustrano come ridimensionare le unità di codifica, vedere [questo](media-services-scale-media-processing-overview.md) argomento.

## <a name="next-step"></a>Passaggio successivo

[Ridimensionare le unità di codifica](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

