---
title: Panoramica e confronto dei codificatori multimediali su richiesta di Azure | Microsoft Docs
description: Questo argomento offre una panoramica e un confronto tra i codificatori multimediali su richiesta di Azure.
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: 6aecea32361166d363a412b4e8f2b01d421da5cf


---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Panoramica e confronto dei codificatori multimediali su richiesta di Azure
## <a name="encoding-overview"></a>Panoramica della codifica
Servizi multimediali di Azure offre diverse opzioni per la codifica di servizi multimediali nel cloud.

Quando si iniziano ad utilizzare i servizi multimediali, è importante comprendere la differenza tra codec e formati di file.
I codec sono costituiti da software che implementa gli algoritmi di compressione/decompressione, mentre i formati di file sono contenitori che includono il video compresso.

Servizi multimediali include la funzionalità per la creazione dinamica dei pacchetti, che consente di distribuire contenuto con codifica Smooth Streaming o MP4 a bitrate adattivo nei formati supportati da Servizi multimediali, ovvero MPEG-DASH, HLS, Smooth Streaming, senza dover ricreare i pacchetti in questi formati di streaming.

>[!NOTE]
>Quando l'account AMS viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. Per sfruttare i vantaggi del servizio di [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md), è necessario seguire questa procedura:
>
>Codificare anche il file di origine in un set di file MP4 o Smooth Streaming a velocità in bit adattiva (i passaggi per la codifica sono descritti più avanti in questa esercitazione).

Servizi multimediali supporta i seguenti codificatori su richiesta descritti in questo articolo:

* [Codificatore multimediale standard](media-services-encode-asset.md#media-encoder-standard)
* [Flusso di lavoro Premium del codificatore multimediale](media-services-encode-asset.md#media-encoder-premium-workflow)

In questo articolo è fornita una breve panoramica dei codificatori multimediali su richiesta e sono presenti collegamenti ad articoli che contengono informazioni più dettagliate. L'argomento fornisce inoltre il confronto dei codificatori.

Si noti che per impostazione predefinita, in ciascun account di Servizi multimediali può essere attiva una sola attività di codifica alla volta. È tuttavia possibile riservare unità di codifica che consentano di eseguire più attività di codifica contemporaneamente, una per ciascuna unità acquistata. Per informazioni, vedere [Scalabilità dell’unità di codifica](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Codificatore multimediale standard
### <a name="how-to-use"></a>Utilizzo
[Come codificare con Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formati
[Codec e formati](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Impostazioni predefinite
Media Encoder Standard viene configurato mediante un set di impostazioni descritto [qui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadati di input e output
I metadati di input dei codificatori sono descritti [qui](media-services-input-metadata-schema.md).

I metadati di output dei codificatori sono descritti [qui](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Generare anteprime
Per informazioni, vedere l'argomento [Come generare anteprime utilizzando Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Tagliare video (ritaglio)
Per informazioni, vedere [Come tagliare video usando Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Creare sovrimpressioni
Per informazioni, vedere [Come creare sovrimpressioni usando Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Vedere anche
[Blog di Servizi multimediali](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Flusso di lavoro Premium del codificatore multimediale
### <a name="overview"></a>Overview
[Introduzione alla codifica Premium in Servizi multimediali di Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Utilizzo
Il flusso di lavoro Premium del codificatore multimediale viene configurato usando flussi di lavoro complessi. Per creare e aggiornare i file di un flusso di lavoro, è possibile usare lo strumento [Progettazione flussi di lavoro](media-services-workflow-designer.md) .

[Come usare la codifica Premium in Servizi multimediali di Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Problemi noti
Se il video di input non contiene i sottotitoli codificati, l'asset di output conterrà comunque un file TTML vuoto.


## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Articoli correlati
* [Eseguire attività di codifica avanzata personalizzando i set di impostazioni di Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quote e limitazioni](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/



<!--HONumber=Jan17_HO2-->


