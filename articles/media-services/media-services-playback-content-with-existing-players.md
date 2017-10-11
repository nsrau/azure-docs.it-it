---
title: Usare i lettori esistenti per la riproduzione dei contenuti - Azure | Documentazione Microsoft
description: "Questo argomento elenca i lettori esistenti che è possibile usare per la riproduzione dei propri contenuti."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: juliako
ms.openlocfilehash: 48f373b013b1192c353352b801876d706d91dd28
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="playing-your-content-with-existing-players"></a>Riproduzione di contenuti con i lettori esistenti
Servizi multimediali di Azure supporta molti formati di streaming noti, ad esempio Smooth Streaming, HTTP Live Streaming (HLS) e MPEG-DASH. Questo argomento descrive i lettori esistenti che possono essere usati per testare i propri flussi.

### <a name="the-azure-portal-media-services-content-player"></a>Lettore di contenuti di Servizi multimediali del portale di Azure
Il portale di **Azure** fornisce un lettore di contenuti che può essere usato per testare il proprio video.

Fare clic sul video desiderato (assicurarsi che sia stato [pubblicato](media-services-portal-publish.md)) e fare clic sul pulsante **Play** nella parte inferiore del portale.

Considerazioni applicabili:

* Il **LETTORE DI CONTENUTI DI SERVIZI MULTIMEDIALI** esegue la riproduzione dall'endpoint di streaming predefinito. Se si vuole eseguire la riproduzione da un endpoint di streaming diverso, usare un altro lettore, ad esempio [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player
Usare il [lettore multimediale di Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) per riprodurre i contenuti (in chiaro o protetti) in uno dei seguenti formati:

* Smooth Streaming
* MPEG DASH
* HLS
* MP4 progressivo

### <a name="flash-player"></a>Flash Player
#### <a name="aes-encrypted-with-token"></a>Con crittografia AES con token
[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Lettori Silverlight
#### <a name="monitoring"></a>Monitoraggio
[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

#### <a name="playready-with-token"></a>PlayReady con token
[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Lettori DASH
[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### <a name="other"></a>Altri
Per testare gli URL HLS è inoltre possibile usare:

* **Safari** in un dispositivo iOS o
* **Lettore HLS 3ivx** in un dispositivo Windows.

## <a name="developing-video-players"></a>Sviluppo di lettori video
Per informazioni su come sviluppare i propri lettori, vedere [Sviluppo di lettori di contenuti video](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
