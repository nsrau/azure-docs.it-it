---
title: Panoramica dei codificatori multimediali su richiesta di Azure | Microsoft Docs
description: Servizi multimediali di Azure offre diverse opzioni per la codifica di servizi multimediali nel cloud. Questo articolo fornisce una panoramica dei codificatori multimediali su richiesta di Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: 5d5a00488321c9c67dd1469b1d8476636675aa8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91281015"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Panoramica dei codificatori multimediali su richiesta di Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. <br/>Esplorare l'ultima versione, [Servizi multimediali v3](../latest/index.yml). Vedere anche le [indicazioni sulla migrazione dalla versione 2 alla versione 3](../latest/migrate-from-v2-to-v3.md)

Servizi multimediali di Azure offre diverse opzioni per la codifica di servizi multimediali nel cloud.

Quando si iniziano ad utilizzare i servizi multimediali, è importante comprendere la differenza tra codec e formati di file.
I codec sono costituiti da software che implementa gli algoritmi di compressione/decompressione, mentre i formati di file sono contenitori che includono il video compresso.

Servizi multimediali include la funzionalità per la creazione dinamica dei pacchetti, che consente di distribuire contenuto con codifica Smooth Streaming o MP4 a bitrate adattivo nei formati supportati da Servizi multimediali, ovvero MPEG-DASH, HLS, Smooth Streaming, senza dover ricreare i pacchetti in questi formati di streaming.

Quando viene creato l'account di servizi multimediali, all'account viene aggiunto un endpoint di streaming **predefinito** con stato **interrotto** . Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. La fatturazione per gli endpoint di streaming si verifica ogni volta che l'endpoint si trova in uno stato di **esecuzione** .

Servizi multimediali supporta il codificatore on demand seguente:

* [Codificatore multimediale standard](media-services-encode-asset.md#media-encoder-standard)

Questo articolo fornisce una breve panoramica dei codificatori multimediali su richiesta e i collegamenti agli articoli con informazioni più dettagliate.

Per impostazione predefinita, in ciascun account di Servizi multimediali può essere attiva una sola attività di codifica alla volta. È tuttavia possibile riservare unità di codifica che consentano di eseguire più attività di codifica contemporaneamente, una per ciascuna unità acquistata. Per informazioni, vedere [Scalabilità dell’unità di codifica](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Codificatore multimediale standard

### <a name="how-to-use"></a>Uso
[Come codificare con Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formati
[Codec e formati](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Impostazioni predefinite
Media Encoder Standard viene configurato mediante un set di impostazioni descritto [qui](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadati di input e output
I metadati di input dei codificatori sono descritti [qui](media-services-input-metadata-schema.md).

I metadati di output dei codificatori sono descritti [qui](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Generare anteprime
Per informazioni, vedere l'argomento [Come generare anteprime utilizzando Media Encoder Standard](media-services-advanced-encoding-with-mes.md).

### <a name="trim-videos-clipping"></a>Tagliare video (ritaglio)
Per informazioni, vedere [Come tagliare video usando Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Creare sovrimpressioni
Per informazioni, vedere [Come creare sovrimpressioni usando Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Vedere anche
[Blog di Servizi multimediali](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

### <a name="known-issues"></a>Problemi noti
Se il video di input non contiene i sottotitoli codificati, l'asset di output conterrà comunque un file TTML vuoto.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Articoli correlati
* [Eseguire attività di codifica avanzata personalizzando i set di impostazioni di Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quote e limitazioni](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
