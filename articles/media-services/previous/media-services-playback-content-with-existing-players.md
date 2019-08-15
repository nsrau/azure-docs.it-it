---
title: Usare i lettori esistenti per la riproduzione dei contenuti - Azure | Documentazione Microsoft
description: Questo argomento elenca i lettori esistenti che è possibile usare per la riproduzione dei propri contenuti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0da8ce396ce07b7c9859fcfccb5cb524c0ce21c1
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "69015301"
---
# <a name="playing-your-content-with-existing-players"></a>Riproduzione di contenuti con i lettori esistenti
Servizi multimediali di Azure supporta molti formati di streaming noti, ad esempio Smooth Streaming, HTTP Live Streaming (HLS) e MPEG-DASH. Questo argomento descrive i lettori esistenti che possono essere usati per testare i propri flussi.

### <a name="the-azure-portal-media-services-content-player"></a>Lettore di contenuti di Servizi multimediali del portale di Azure
Il portale di **Azure** fornisce un lettore di contenuti che può essere usato per testare il proprio video.

Fare clic sul video desiderato (assicurarsi che sia stato [pubblicato](media-services-portal-publish.md)) e fare clic sul pulsante **Play** nella parte inferiore del portale.

Considerazioni applicabili:

* Il **LETTORE DI CONTENUTI DI SERVIZI MULTIMEDIALI** esegue la riproduzione dall'endpoint di streaming predefinito. Se si vuole eseguire la riproduzione da un endpoint di streaming diverso, usare un altro lettore, ad esempio [Azure Media Player](https://aka.ms/azuremediaplayer).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player

Usare il [lettore multimediale di Azure](https://aka.ms/azuremediaplayer) per riprodurre i contenuti (in chiaro o protetti) in uno dei seguenti formati:

* Smooth Streaming
* MPEG DASH
* HLS
* MP4 progressivo

### <a name="flash-player"></a>Flash Player

#### <a name="aes-encrypted-with-token"></a>Con crittografia AES con token

[https://aestoken.azurewebsites.net](https://aestoken.azurewebsites.net)

#### <a name="playready-with-token"></a>PlayReady con token

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Lettori DASH

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Altro
Per testare gli URL HLS è inoltre possibile usare:

* **Safari** in un dispositivo iOS o
* **Lettore HLS 3ivx** in un dispositivo Windows.

## <a name="developing-video-players"></a>Sviluppo di lettori video
Per informazioni su come sviluppare i propri lettori, vedere [Sviluppo di lettori di contenuti video](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
