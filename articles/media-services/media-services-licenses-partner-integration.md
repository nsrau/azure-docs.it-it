---
title: Uso di partner per distribuire licenze Widevine a Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra come usare Servizi multimediali di Azure per distribuire un flusso crittografato in modo dinamico da Servizi multimediali di Azure mediante DRM di PlayReady e Widevine. La licenza per PlayReady viene distribuita dal server licenze PlayReady di Servizi multimediali, mentre la licenza per Widevine viene distribuita dal server licenze castLabs.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako

---
# <a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>Uso di partner per distribuire licenze Widevine a Servizi multimediali di Azure
## <a name="overview"></a>Overview
Servizi multimediali di Microsoft Azure consente di distribuire contenuto protetto MPEG-DASH con DRM Widevine, che viene crittografato per la specifica CENC (Common Encryption).

A partire da Servizi Multimediali .NET SDK versione 3.5.2, Servizi multimediali consente di configurare il modello di licenza Widevine e ottenere licenze Widevine. È anche possibile usare i partner AMS seguenti per facilitare la distribuzione di licenze Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) e [castLabs](http://castlabs.com/company/partners/azure/).

## <a name="castlabs"></a>castLabs
È possibile utilizzare [castLabs](http://castlabs.com/company/partners/azure/) per distribuire licenze Widevine. Per altre informazioni, vedere [Uso di castLabs per distribuire licenze DRM a Servizi multimediali di Azure](media-services-castlabs-integration.md)

## <a name="axinom"></a>Axinom
È possibile utilizzare [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) per distribuire licenze Widevine. Per altre informazioni, vedere [Uso di Axinom per distribuire licenze DRM a Servizi multimediali di Azure](media-services-axinom-integration.md)

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Vedere anche
[Uso della crittografia comune dinamica PlayReady e/o Widevine](media-services-protect-with-drm.md)

[Blog di Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

<!--HONumber=Oct16_HO2-->


