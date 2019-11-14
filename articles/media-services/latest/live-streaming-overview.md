---
title: Panoramica dello streaming live con servizi multimediali di Azure V3 | Microsoft Docs
description: Questo articolo offre una panoramica dello streaming live con Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/12/2019
ms.author: juliako
ms.openlocfilehash: 8d7db428d7f71383abf5425d7cc1ddbbab3b7a52
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037887"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Streaming live con Servizi multimediali di Azure v3

Servizi multimediali di Azure consente di offrire eventi live per i clienti nel cloud di Azure. Per eseguire lo streaming di eventi live con Servizi multimediali, è necessario quanto segue:  

- Una fotocamera usata per riprendere l'evento live.<br/>Per alcune idee per la configurazione, vedere [Simple and portable event video gear setup]( https://link.medium.com/KNTtiN6IeT) (Configurazione semplice e portabile di attrezzature video per eventi).

    Se non si ha accesso a una fotocamera, è possibile usare strumenti come [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) per generare un feed live da un file video.
- Un codificatore video live in grado di convertire i segnali provenienti da una fotocamera (o da un altro dispositivo, come un portatile) in un feed di contributi inviato a Servizi multimediali. Il feed di contributi può includere segnali relativi alla pubblicità, ad esempio i marcatori SCTE-35.<br/>Per un elenco dei codificatori consigliati di streaming live, vedere [Codificatori di streaming live consigliati](recommended-on-premises-live-encoders.md). Vedere anche questo Blog: [produzione di streaming live con OBS](https://link.medium.com/ttuwHpaJeT).
- I componenti in Servizi multimediali, che consentono di inserire, visualizzare in anteprima, includere in un pacchetto, registrare, crittografare e trasmettere l'evento live ai clienti o a una rete CDN per un'ulteriore distribuzione.

Questo articolo fornisce una panoramica e informazioni aggiuntive sullo streaming live con servizi multimediali e collegamenti ad altri articoli pertinenti.
 
> [!NOTE]
> Non è attualmente possibile usare il portale di Azure per gestire le risorse v3. Usare l'[API REST](https://aka.ms/ams-v3-rest-ref), l'[interfaccia della riga di comando](https://aka.ms/ams-v3-cli-ref) o uno degli [SDK](media-services-apis-overview.md#sdks) supportati.

## <a name="dynamic-packaging"></a>Creazione dinamica dei pacchetti

Con servizi multimediali è possibile sfruttare i vantaggi della creazione [dinamica dei pacchetti](dynamic-packaging-overview.md), che consente di visualizzare in anteprima e trasmettere i flussi live in [formati MPEG Dash, HLS e Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) dal feed di contributi inviato al servizio. Gli utenti possono riprodurre il flusso live con qualsiasi lettore compatibile per HLS, DASH o Smooth Streaming. È possibile usare [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) nelle applicazioni web o per dispositivi mobili per distribuire lo streaming in uno di questi protocolli.

## <a name="dynamic-encryption"></a>Crittografia dinamica

La crittografia dinamica consente di crittografare dinamicamente i contenuti live o su richiesta con AES-128 o uno dei tre sistemi principali Digital Rights Management (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. Per altre informazioni, vedere [crittografia dinamica](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Manifesto dinamico

Il filtro dinamico viene usato per controllare il numero di tracce, formati, velocità in bit e finestre temporali di presentazione inviate ai lettori. Per altre informazioni, vedere [filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Tipi di evento live

Gli [eventi live](https://docs.microsoft.com/rest/api/media/liveevents) sono responsabili dell'inserimento e dell'elaborazione dei feed video live. Un evento Live può essere di uno dei due tipi seguenti: pass-through e codifica live. Per informazioni dettagliate sullo streaming live in servizi multimediali V3, vedere [eventi live e output Live](live-events-outputs-concept.md).

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.svg)

Quando si usa l' **evento Live**pass-through, si fa affidamento sul codificatore Live locale per generare un flusso video a bitrate multipli e inviarlo come feed di contributo all'evento Live (usando il protocollo di input RTMP o frammentato-MP4). L'evento Live trasporta quindi i flussi video in ingresso verso Dynamic Packager (endpoint di streaming) senza ulteriore transcodifica. Questo evento Live pass-through è ottimizzato per eventi live a esecuzione prolungata o streaming live lineare 24x365. 

### <a name="live-encoding"></a>Codifica live  

![codifica live](./media/live-streaming/live-encoding.svg)

Quando si usa la codifica cloud con servizi multimediali, è necessario configurare il codificatore Live locale per l'invio di un video a bitrate singolo come feed di contributo (fino a 32Mbps aggregato) per l'evento Live (usando il protocollo di input RTMP o frammentato-MP4). L'evento Live consente di transcodificare il flusso a bitrate singolo in ingresso in [flussi video a bitrate multipli](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) con diverse risoluzioni per migliorare la distribuzione e renderlo disponibile per la distribuzione ai dispositivi di riproduzione tramite protocolli standard di settore, ad esempio MPEG-Dash, Apple http live streaming (HLS) e Microsoft Smooth Streaming. 

### <a name="live-transcription"></a>Trascrizione in tempo reale

La trascrizione in tempo reale è una funzionalità che è possibile usare con gli eventi live che sono pass-through o la codifica live. Per ulteriori informazioni, vedere [trascrizione in tempo reale](live-transcription.md). Quando questa funzionalità è abilitata, il servizio usa la funzionalità di [riconoscimento vocale](../../cognitive-services/speech-service/speech-to-text.md) dei servizi cognitivi per trascrivere le parole pronunciate nell'audio in ingresso nel testo. Questo testo viene quindi reso disponibile per il recapito insieme a video e audio nei protocolli MPEG-DASH e HLS.

> [!NOTE]
> Attualmente, la trascrizione in tempo reale è disponibile come funzionalità di anteprima negli Stati Uniti occidentali 2.

## <a name="live-streaming-workflow"></a>Flusso di lavoro dello streaming live

Per comprendere il flusso di lavoro di streaming live in servizi multimediali V3, è necessario prima esaminare e comprendere i concetti seguenti: 

- [Endpoint di streaming](streaming-endpoint-concept.md)
- [Eventi live e output live](live-events-outputs-concept.md)
- [Localizzatori di streaming](streaming-locators-concept.md)

### <a name="general-steps"></a>Passaggi generali

1. Nell'account di servizi multimediali verificare che l' **endpoint di streaming** (Origin) sia in esecuzione. 
2. Creare un [Evento live](live-events-outputs-concept.md). <br/>Quando si crea l'evento, è possibile impostarne l'avvio automatico. In alternativa, è possibile avviare l'evento quando si è pronti ad avviare lo streaming.<br/> Quando l'avvio automatico è impostato su true, l'evento live verrà avviato subito dopo la creazione. La fatturazione inizia non appena viene avviata l'esecuzione dell'evento live. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione. Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).
3. Ottenere gli URL di inserimento e configurare il codificatore locale in modo da usare l'URL per inviare il feed di contributo.<br/>Vedere i [codificatori live consigliati](recommended-on-premises-live-encoders.md).
4. Ottenere l'URL di anteprima e usarlo per verificare che l'input dal codificatore venga effettivamente ricevuto.
5. Creare un nuovo oggetto **Asset**. 

    Ogni output Live è associato a un asset, che usa per registrare il video nel contenitore di archiviazione BLOB di Azure associato. 
6. Creare un **output in tempo reale** e usare il nome dell'asset creato in modo che il flusso possa essere archiviato nell'asset.

    Gli output live iniziano al momento della creazione e terminano quando vengono eliminati. Quando si elimina l'output Live, non si elimina l'asset e il contenuto sottostanti nell'asset.
7. Creare un **localizzatore di streaming** con i [tipi di criteri di streaming predefiniti](streaming-policy-concept.md).

    Per pubblicare l'output Live, è necessario creare un localizzatore di streaming per l'asset associato. 
8. Elencare i percorsi nel **localizzatore di streaming** per ottenere gli URL da usare (questi sono deterministici).
9. Ottenere il nome host per l' **endpoint di streaming** (Origin) da cui si vuole eseguire il flusso.
10. Combinare l'URL del passaggio 8 con il nome host del passaggio 9 per ottenere l'URL completo.
11. Se si vuole interrompere la creazione dell'**evento live** visualizzabile, è necessario arrestare lo streaming dell'evento ed eliminare il **localizzatore di streaming**.
12. Se al termine dello streaming degli eventi si vuole eliminare le risorse di cui in precedenza è stato effettuato il provisioning, attenersi alla procedura seguente.

    * Interrompere il push del flusso dal codificatore.
    * Arrestare l'evento live. Dopo l'arresto, l'evento live non è soggetto ad alcun addebito. Quando occorrerà riavviarlo, avrà lo stesso URL di inserimento, per cui non sarà necessario riconfigurare il codificatore.
    * È possibile arrestare l'endpoint di streaming, a meno che non si voglia continuare a fornire l'archivio dell'evento live come flusso su richiesta. Se l'evento live è stato arrestato, non è soggetto ad alcun addebito.

L'asset a cui viene archiviata l'output Live, diventa automaticamente un asset su richiesta quando viene eliminato l'output in tempo reale. È necessario eliminare tutti gli output Live prima che un evento live possa essere arrestato. È possibile usare un flag facoltativo [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) per rimuovere automaticamente gli output Live all'arresto. 

> [!TIP]
> Vedere l' [esercitazione su Live streaming](stream-live-tutorial-with-api.md). l'articolo esamina il codice che implementa i passaggi descritti in precedenza.

## <a name="other-important-articles"></a>Altri articoli importanti

- [Codificatori live consigliati](recommended-on-premises-live-encoders.md)
- [Utilizzo di un DVR cloud](live-event-cloud-dvr.md)
- [Confronto tra le funzionalità dei tipi di eventi live](live-event-types-comparison.md)
- [Stati e fatturazione](live-event-states-billing.md)
- [Latency](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
* [Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3](migrate-from-v2-to-v3.md)
