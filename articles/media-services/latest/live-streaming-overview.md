---
title: Panoramica dello streaming live con Servizi multimediali di Azure v3 Documenti Microsoft
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
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e2c4e5b6c10b06d82a1933962cb2d97e031876a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068030"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Streaming live con Servizi multimediali di Azure v3

Servizi multimediali di Azure consente di offrire eventi live per i clienti nel cloud di Azure. Per eseguire lo streaming di eventi live con Servizi multimediali, è necessario quanto segue:  

- Una fotocamera usata per riprendere l'evento live.<br/>Per alcune idee per la configurazione, vedere [Simple and portable event video gear setup]( https://link.medium.com/KNTtiN6IeT) (Configurazione semplice e portabile di attrezzature video per eventi).

    Se non si ha accesso a una videocamera, strumenti come [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) possono essere utilizzati per generare un feed live da un file video.
- Un codificatore video live in grado di convertire i segnali provenienti da una fotocamera (o da un altro dispositivo, come un portatile) in un feed di contributi inviato a Servizi multimediali. Il feed di contributi può includere segnali relativi alla pubblicità, ad esempio i marcatori SCTE-35.<br/>Per un elenco dei codificatori consigliati di streaming live, vedere [Codificatori di streaming live consigliati](recommended-on-premises-live-encoders.md). Inoltre, dai un'occhiata a questo blog: Produzione live [streaming con OBS](https://link.medium.com/ttuwHpaJeT).
- I componenti in Servizi multimediali, che consentono di inserire, visualizzare in anteprima, includere in un pacchetto, registrare, crittografare e trasmettere l'evento live ai clienti o a una rete CDN per un'ulteriore distribuzione.

Questo articolo fornisce una panoramica e una guida dello streaming live con Servizi multimediali e collegamenti ad altri articoli pertinenti.
 
> [!NOTE]
> È possibile usare il portale di [Azure](https://portal.azure.com/) per gestire [gli eventi live](live-events-outputs-concept.md)v3 , visualizzare gli [asset](assets-concept.md)v3, ottenere informazioni sull'accesso alle API. Per tutte le altre attività di gestione, ad esempio Trasformazioni e processi, utilizzare [l'API REST](https://docs.microsoft.com/rest/api/media/), l'interfaccia [della riga di comando](https://aka.ms/ams-v3-cli-ref)o uno degli [SDK](media-services-apis-overview.md#sdks)supportati.

## <a name="dynamic-packaging"></a>Creazione dinamica dei pacchetti

Con Servizi multimediali, puoi sfruttare [Dynamic Packaging](dynamic-packaging-overview.md), che ti permette di visualizzare in anteprima e trasmettere i tuoi live streaming nei [formati MPEG DASH, HLS e Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) dal feed di contributo che viene inviato al servizio. Gli utenti possono riprodurre il flusso live con qualsiasi lettore compatibile per HLS, DASH o Smooth Streaming. È possibile usare [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) nelle applicazioni web o per dispositivi mobili per distribuire lo streaming in uno di questi protocolli.

## <a name="dynamic-encryption"></a>Crittografia dinamica

La crittografia dinamica consente di crittografare dinamicamente i contenuti live o on-demand con AES-128 o uno dei tre principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. Per ulteriori informazioni, vedere [Crittografia dinamica](content-protection-overview.md).

> [!NOTE]
> Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="dynamic-manifest"></a>Manifesto dinamico

Il filtro dinamico viene utilizzato per controllare il numero di tracce, formati, bitrate e intervalli di tempo di presentazione inviati ai lettori. Per ulteriori informazioni, vedere [filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Tipi di evento live

Gli [eventi live](https://docs.microsoft.com/rest/api/media/liveevents) sono responsabili dell'inserimento e dell'elaborazione dei feed video live. Un evento live può essere impostato su un *pass-through* (un codificatore live locale invia un flusso a bitrate multiplo) o una *codifica live* (un codificatore live locale invia un singolo flusso a bitrate). Per informazioni dettagliate sul live streaming in Servizi multimediali v3, consultate [Eventi live e output live.](live-events-outputs-concept.md)

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.svg)

Quando si utilizza **l'evento live**pass-through , si fa affidamento sul codificatore live locale per generare un flusso video a bitrate multiplo e inviarlo come feed di contributo all'evento Live Event (utilizzando il protocollo di input RTMP o fragmented-MP4). L'evento live passa quindi attraverso i flussi video in ingresso al packager dinamico (Endpoint di streaming) senza ulteriore transcodifica. Tale evento live pass-through è ottimizzato per eventi live a esecuzione prolungata o 24x365 live streaming lineare. 

### <a name="live-encoding"></a>Codifica live  

![codifica live](./media/live-streaming/live-encoding.svg)

Quando si usa la codifica cloud con Servizi multimediali, è necessario configurare il codificatore live locale per inviare un singolo video in bitrate come feed di contributo (fino a 32Mbps aggregato) all'evento live (utilizzando il protocollo di input RTMP o fragmented-MP4). L'evento live transcodifica il flusso di bitrate singolo in ingresso in [più flussi video bitrate](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) a risoluzioni diverse per migliorare la distribuzione e lo rende disponibile per la consegna ai dispositivi di riproduzione tramite protocolli standard del settore come MPEG-DASH, Apple HTTP Live Streaming (HLS) e Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Trascrizione dal vivo (anteprima)

La trascrizione in tempo reale è una funzionalità che è possibile utilizzare con gli eventi live che sono una codifica pass-through o live. Per ulteriori informazioni, consultate [Trascrizione dal vivo.](live-transcription.md) Quando questa funzionalità è abilitata, il servizio utilizza la funzionalità [di sintesi](../../cognitive-services/speech-service/speech-to-text.md) vocale di Servizi cognitivi per trascrivere le parole pronunciate nell'audio in ingresso in testo. Questo testo viene quindi reso disponibile per la consegna insieme a video e audio nei protocolli MPEG-DASH e HLS.

> [!NOTE]
> Attualmente, la trascrizione dal vivo è disponibile come funzione di anteprima in West US 2.

## <a name="live-streaming-workflow"></a>Flusso di lavoro dello streaming live

Per comprendere il flusso di lavoro di live streaming in Servizi multimediali v3, è necessario innanzitutto esaminare e comprendere i concetti seguenti:To understand the live streaming workflow in Media Services v3, you have first review and understand the following concepts: 

- [Endpoint di streaming](streaming-endpoint-concept.md)
- [Eventi live e output live](live-events-outputs-concept.md)
- [Localizzatori di streaming](streaming-locators-concept.md)

### <a name="general-steps"></a>Passaggi generali

1. Nell'account di Servizi multimediali verificare che **l'endpoint** di streaming (origine) sia in esecuzione. 
2. Creare un [evento live](live-events-outputs-concept.md). <br/>Quando si crea l'evento, è possibile impostarne l'avvio automatico. In alternativa, è possibile avviare l'evento quando si è pronti ad avviare lo streaming.<br/> Quando l'avvio automatico è impostato su true, l'evento live verrà avviato subito dopo la creazione. La fatturazione inizia non appena viene avviata l'esecuzione dell'evento live. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione. Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).
3. Ottenere gli URL di inserimento e configurare il codificatore locale per l'utilizzo dell'URL per inviare il feed di contribuzione.<br/>Vedere i [codificatori live consigliati](recommended-on-premises-live-encoders.md).
4. Ottenere l'URL di anteprima e usarlo per verificare che l'input dal codificatore venga effettivamente ricevuto.
5. Creare un nuovo oggetto **Asset.** 

    Ogni output live è associato a un asset, che usa per registrare il video nel contenitore di archiviazione BLOB di Azure associato. 
6. Create un **Live Output** e utilizzate il nome della risorsa creata in modo che il flusso possa essere archiviato nella risorsa.

    Gli output live iniziano al momento della creazione e terminano quando vengono eliminati. Quando eliminate l'Output live, non eliminate l'asset sottostante e il contenuto dell'asset.
7. Creare un **localizzatore** di streaming con i tipi di [criteri di streaming incorporati.](streaming-policy-concept.md)

    Per pubblicare l'output live, è necessario creare un localizzatore di streaming per l'asset associato. 
8. Elencare i percorsi nel **localizzatore di streaming** per ottenere gli URL da usare (questi sono deterministici).
9. Ottenere il nome host per **l'endpoint** di streaming (origine) da cui si desidera eseguire lo streaming.
10. Combinare l'URL del passaggio 8 con il nome host del passaggio 9 per ottenere l'URL completo.
11. Se si vuole interrompere la creazione dell'**evento live** visualizzabile, è necessario arrestare lo streaming dell'evento ed eliminare il **localizzatore di streaming**.
12. Se al termine dello streaming degli eventi si vuole eliminare le risorse di cui in precedenza è stato effettuato il provisioning, attenersi alla procedura seguente.

    * Interrompere il push del flusso dal codificatore.
    * Arrestare l'evento live. Dopo l'arresto, l'evento live non è soggetto ad alcun addebito. Quando occorrerà riavviarlo, avrà lo stesso URL di inserimento, per cui non sarà necessario riconfigurare il codificatore.
    * È possibile arrestare l'endpoint di streaming, a meno che non si voglia continuare a fornire l'archivio dell'evento live come flusso su richiesta. Se l'evento live è stato arrestato, non è soggetto ad alcun addebito.

L'asset in cui viene eseguito l'archiviazione dell'output live diventa automaticamente un asset su richiesta quando l'output in tempo reale viene eliminato. È necessario eliminare tutti gli output in tempo reale prima di poter arrestare un evento live. È possibile utilizzare un flag facoltativo [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) per rimuovere automaticamente gli output in tempo reale all'arresto. 

> [!TIP]
> Consultate [L'esercitazione sullo streaming live](stream-live-tutorial-with-api.md)esamina il codice che implementa i passaggi descritti in precedenza.

## <a name="other-important-articles"></a>Altri articoli importanti

- [Codificatori live consigliati](recommended-on-premises-live-encoders.md)
- [Utilizzo di un DVR cloud](live-event-cloud-dvr.md)
- [Confronto tra le funzionalità dei tipi di eventi live](live-event-types-comparison.md)
- [Stati e fatturazione](live-event-states-billing.md)
- [Latenza](live-event-latency.md)

## <a name="frequently-asked-questions"></a>Domande frequenti

Vedere l'articolo [Domande frequenti.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva allo streaming in diretta] (live-events-wirecast-quickstart.md(
* [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
* [Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3](migrate-from-v2-to-v3.md)
