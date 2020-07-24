---
title: Riproduzione video-Azure
description: Segnaposto
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 17b1f93c18dfb013916c4c0d3756fb97a73e2675
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87042961"
---
# <a name="video-playback"></a>Riproduzione di video 

## <a name="suggested-pre-reading"></a>Letture consigliate 

* [Panoramica di Analisi di video live in IoT Edge](overview.md)
* [Terminologia di Analisi di video live in IoT Edge](terminology.md)
* [Concetti relativi al grafo multimediale](media-graph-concept.md)

## <a name="overview"></a>Panoramica  

È possibile usare i [grafici multimediali](media-graph-concept.md) per registrare i video in un [Asset](terminology.md#asset)di servizi multimediali di Azure. Questo documento illustra i passaggi da eseguire per riprodurre un asset usando le funzionalità di streaming esistenti di servizi multimediali di Azure.

## <a name="streaming-endpoint"></a>endpoint di streaming 

È possibile usare servizi multimediali di Azure per [trasmettere](terminology.md#streaming) l'asset ai lettori video usando protocolli di streaming multimediali basati su http di settore, come http live streaming (HLS) e MPEG-Dash. Questa conversione di file multimediali da contenuto registrato in formati di streaming viene gestita da un [endpoint di streaming](../latest/streaming-endpoint-concept.md), ovvero una risorsa di cui è necessario eseguire il provisioning nell'account del servizio multimediale di Azure.

## <a name="streaming-policy"></a>Criteri di streaming 

Servizi multimediali di Azure offre diversi metodi per proteggere i flussi video, come illustrato nell'articolo [proteggere i contenuti con la crittografia dinamica di servizi multimediali](../latest/content-protection-overview.md) . A un livello elevato, le opzioni per la protezione del contenuto sono:

* **Streaming in chiaro, in** cui non viene applicata alcuna crittografia durante il flusso.
* **Usare Advanced Encryption Standard (AES-128)** e implementare un metodo per fornire le chiavi per decrittografare il video solo per i visualizzatori autenticati.
* **Usare i sistemi di Rights Management digitali (DRM)** : per controllare l'uso, la modifica e il recapito dei video ai dispositivi che applicano questi criteri.

Per ottenere la protezione del contenuto, è possibile definire e creare un [criterio di flusso](../latest/streaming-policy-concept.md) nell'account del servizio multimediale e usarlo per lo streaming di tutti gli asset (presupponendo che tutti i flussi abbiano gli stessi requisiti per la sicurezza). È anche possibile usare uno dei criteri predefiniti, ad esempio Predefined_ClearStreamingOnly.

## <a name="streaming-locator"></a>Localizzatore di streaming  

Dopo aver avviato un endpoint di streaming nell'account del servizio multimediale e aver definito i criteri di flusso, è possibile passare a trasmettere i supporti registrati da un asset tramite protocolli HLS o DASH. Per i lettori web e le app per dispositivi mobili è necessario un URL che punta al flusso HLS o DASH. È possibile compilare questo URL usando il [localizzatore di streaming](../latest/streaming-locators-concept.md). Come illustrato in questo articolo e illustrato nell'esempio [creare un localizzatore di streaming e URL di compilazione](../latest/create-streaming-locator-build-url.md) , l'URL di streaming è costituito dall'endpoint di streaming, dai criteri di streaming e dal localizzatore di streaming.

## <a name="content-recorded-using-file-sink"></a>Contenuto registrato con il sink di file  

Come descritto in [media Graph file sink](media-graph-concept.md#file-sink), è possibile usare i grafici dei supporti per registrare i video nella file system locale del dispositivo perimetrale usando un sink di file nel grafico multimediale. Il sink di file genera file [MP4](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4) ed è possibile usare l'elemento [ &lt; video &gt; ](https://developer.mozilla.org/docs/Web/HTML/Element/video) HTML5 per riprodurre tale contenuto. 

## <a name="next-steps"></a>Passaggi successivi

[Azure IoT Edge](../../iot-edge/index.yml)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->
