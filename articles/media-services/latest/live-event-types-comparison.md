---
title: Tipi di LiveEvent Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra una tabella dettagliata che confronta i tipi LiveEvent.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 93f01513841d1174fea796f1615ab05df0a41af4
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150374"
---
# <a name="live-event-types-comparison"></a>Confronto tra tipi di eventi live

In Servizi multimediali di Azure, un [evento live](https://docs.microsoft.com/rest/api/media/liveevents) può essere di due tipi: codifica live e pass-through. 

## <a name="types-comparison"></a>Confronto tra tipi 

La tabella seguente confronta le funzionalità dei tipi di eventi in tempo reale. I tipi vengono impostati durante la creazione usando [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** -un codificatore live locale invia un flusso a velocità in bit più. I flussi inseriti passano attraverso l'evento Live senza altre elaborazioni. 
* **LiveEventEncodingType.Standard** : un codificatore live invia un flusso a bitrate singolo con l'evento Live e servizi multimediali consente di creare flussi a bitrate multipli on-premise. Se il feed di contributo per 720p o risoluzione superiore, il **Default720p** preimpostato codificheranno un set di coppie di risoluzione/velocità in bit 6 (seguono i dettagli più avanti nell'articolo).
* **LiveEventEncodingType.Premium1080p** : un codificatore live invia un flusso a bitrate singolo con l'evento Live e servizi multimediali consente di creare flussi a bitrate multipli on-premise. Il set di impostazioni Default1080p specifica il set di output di coppie bitrate/risoluzione (seguono i dettagli più avanti nell'articolo). 

| Funzionalità | Evento live pass-through | Standard o un evento Live Premium1080p |
| --- | --- | --- |
| Input a bitrate singolo codificato in bitrate multipli nel cloud |No |Yes |
| Risoluzione video massima per feed di contributo |4K (4096 x 2160 a 60 fotogrammi/sec) |1080p (1920 x 1088 a 30 fotogrammi/sec)|
| Livelli massimi consigliati per feed di contributo|Fino a 12|Un audio|
| Livelli massimi nell'output| Uguale all'input|Fino a 6 (vedere il set di impostazioni di sistema riportata di seguito)|
| Larghezza di banda aggregata massima per feed di contributo|60 Mbps|N/D|
| Velocità in bit massima per un singolo livello all'interno del contributo |20 Mbps|20 Mbps|
| Supporto per tracce audio in più lingue|Yes|No|
| Codec video di input supportati |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codec video di output supportati|Uguale all'input|H.264/AVC|
| Profondità bit video di input e output supportata|Fino a 10 bit incluso HDR 10/HLG|8 bit|
| Codec audio di input supportati|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codec audio di output supportati|Uguale all'input|AAC-LC|
| Risoluzione video massima del video di output|Uguale all'input|Standard - 720p, Premium1080p - 1080p|
| Frequenza massima di frame del video di input|60 fotogrammi al secondo|Standard o Premium1080p - 30 fotogrammi al secondo|
| Protocolli di input|RTMP, MP4 frammentato (Smooth Streaming)|RTMP, MP4 frammentato (Smooth Streaming)|
| Prezzo|Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) e fare clic sulla scheda "Video live"|Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) e fare clic sulla scheda "Video live"|
| Tempo di esecuzione massimo| 24 ore su 24, 365 giorni all'anno, live linear | Fino a 24 ore|
| Possibilità di trasferire dati dei sottotitoli CEA 608/708 integrati|Yes|Yes|
| Supporto per l'inserimento di slate|No|No|
| Supporto per annunci pubblicitari tramite API| No|No|
| Supporto per annunci pubblicitari tramite messaggi in banda SCTE-35|Yes|Yes|
| Possibilità di recuperare brevi fasi di stallo in feed di contributo|Yes|Parziale|
| Supporto per GOP di input non uniformi|Yes|No - La durate GOP dell’input deve essere fissa|
| Supporto per input con frequenza dei fotogrammi variabile|Yes|No: l'input deve essere una frequenza di fotogrammi fissa. Sono tollerate lievi variazioni, ad esempio durante scene ad alta velocità. Ma il feed di contributo non è possibile eliminare la frequenza dei fotogrammi (ad esempio a 15 fotogrammi al secondo).|
| Arresto automatico dell'evento live in caso di perdita del feed di input|No|Dopo 12 ore, se nessun LiveOutput è in esecuzione|

## <a name="system-presets"></a>Set di impostazioni di sistema

Le risoluzioni e velocità in bit contenuti nell'output dal codificatore live sono determinate dal [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Se si usa un' **Standard** live encoder (LiveEventEncodingType.Standard), il *Default720p* set di impostazioni specifica un set di coppie di risoluzione/velocità in bit 6 descritti di seguito. In caso contrario, se si usa un' **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), il *Default1080p* set di impostazioni specifica il set di output di coppie bitrate/risoluzione.

> [!NOTE]
> Non è possibile applicare il Default1080p preimpostate su un evento Live, se è stato configurato per la codifica live Standard, si otterrà un errore. Si otterrà inoltre un errore se si prova ad applicare la Default720p preimpostate su un codificatore live Premium1080p.

### <a name="output-video-streams-for-default720p"></a>Flussi Video di output per Default720p

Se il feed di contributo per 720p o risoluzione superiore, il **Default720p** preimpostato codificheranno il feed in 6 livelli seguenti. Nella tabella seguente, a velocità in bit è espressa in kbps, MaxFPS rappresenta tale frequenza massima consentita (in fotogrammi al secondo), profilo rappresenta il profilo H.264 usato.

| Bitrate | Larghezza | Altezza: | MaxFPS | Profilo |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alto |
| 2200 |960 |540 |30 |Alto |
| 1350 |704 |396 |30 |Alto |
| 850 |512 |288 |30 |Alto |
| 550 |384 |216 |30 |Alto |
| 200 |340 |192 |30 |Alto |

> [!NOTE]
> Se è necessario personalizzare il set di impostazioni di codifica live, aprire un ticket di supporto tramite il portale di Azure. È necessario specificare la tabella di risoluzione e la velocità in bit desiderata. Verificare che vi sia un solo livello a 720p e un massimo di 6 livelli. Specificare anche che si sta richiedendo un set di impostazioni per un codificatore live Standard.
> È possibile modificare i valori specifici delle velocità in bit e le soluzioni nel corso del tempo

### <a name="output-video-streams-for-default1080p"></a>Flussi Video di output per Default1080p

Se il feed di contributo di risoluzione 1080p, il **Default1080p** preimpostato codificheranno il feed in 6 livelli seguenti.

| Bitrate | Larghezza | Altezza: | MaxFPS | Profilo |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Alto |
| 3000 |1280 |720 |30 |Alto |
| 1600 |960 |540 |30 |Alto |
| 800 |640 |360 |30 |Alto |
| 400 |480 |270 |30 |Alto |
| 200 |320 |180 |30 |Alto |

> [!NOTE]
> Se è necessario personalizzare il set di impostazioni di codifica live, aprire un ticket di supporto tramite il portale di Azure. È necessario specificare la tabella di risoluzione e la velocità in bit desiderata. Verificare che vi sia un solo a 1080p e al massimo 6 livelli. Specificare anche che si sta richiedendo un set di impostazioni per un codificatore live Premium1080p.
> È possibile modificare i valori specifici delle velocità in bit e le soluzioni nel corso del tempo.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Output Audio Stream per Default720p e Default1080p

Per entrambe *Default720p* e *Default1080p* predefiniti, audio viene codificato a stereo AAC-LC a 128 kbps. La frequenza di campionamento segue che della traccia audio nel feed di contributo.

## <a name="implicit-properties-of-the-live-encoder"></a>Proprietà implicita del codificatore live

La sezione precedente descrive le proprietà del codificatore live che può essere controllata in modo esplicito, tramite il set di impostazioni, ad esempio il numero di livelli, le risoluzioni e velocità in bit. In questa sezione illustra le proprietà implicite.

### <a name="group-of-pictures-gop-duration"></a>Gruppo della durata pictures (GOP)

Il codificatore live segue il [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) struttura del contributo feed, ovvero i livelli di output avrà la stessa durata GOP. Di conseguenza, si consiglia di configurare il codificatore locale per generare un feed di contributo che ha risolto durata GOP (in genere ogni 2 secondi). Ciò garantisce che i flussi HLS e MPEG-DASH in uscita dal servizio anche ha corretto la durata GOP. Variazioni di lieve entità nella durata GOP probabile tollerabile dalla maggior parte dei dispositivi.

### <a name="frame-rate"></a>Frequenza dei fotogrammi

Il codificatore live segue le durate dei singoli frame video nel contributo feed, ovvero che i livelli di output avrà frame con la stessa durata. Di conseguenza, è consigliabile configurare il codificatore locale per generare un feed di contributo che ha risolto frequenza dei fotogrammi (al massimo 30 fotogrammi al secondo). Ciò garantisce che i flussi HLS e MPEG-DASH in uscita dal servizio anche è corretto le durate frequenze di fotogrammi. Variazioni di lieve entità in frequenze di fotogrammi possono essere tollerati dalla maggior parte dei dispositivi, ma non c'è garanzia che il codificatore live produrrà un output che verrà riprodotto correttamente. Il codificatore live locale dovrebbe non essere eliminazione frame (ad es. in condizioni di batteria in esaurimento) o la modifica la frequenza dei fotogrammi in alcun modo.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Risoluzione di contributo feed e i livelli di output

Il codificatore live è configurato per evitare upconverting il feed di contributo. Di conseguenza la risoluzione massima dei livelli di output non supererà che del contributo del feed.

Ad esempio, se si invia un contributo feed 720p per un evento Live configurato per Default1080p codifica live, l'output includerà solo 5 livelli, a partire da 720p a 3 Mbps, passare a con risoluzione 1080p e 200 Kbps. Oppure, se si invia un contributo feed a 360p in un evento Live configurato per il livello Standard della codifica live, l'output conterrà 3 livelli (con risoluzioni di 288p, 216p e 192p). Nel caso degenere, se si invia un feed di contributo di, ad esempio, 160 x 90 pixel a un codificatore live Standard, l'output conterrà un livello alla risoluzione di 160 x 90 alla stessa velocità in bit a quella del contributo del feed.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Velocità in bit di contributo feed e i livelli di output

Il codificatore live è configurato per accettare le impostazioni a velocità in bit nel set di impostazioni, indipendentemente dalla velocità in bit del contributo del feed. Di conseguenza potrebbe superare la velocità in bit dei livelli di output che del contributo del feed. Ad esempio, se si invia un contributo feed con una risoluzione 720p a 1 Mbps, i livelli di output verranno rimangono uguali a quelle di [tabella](live-event-types-comparison.md#output-video-streams-for-default720p) sopra.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica dello streaming live](live-streaming-overview.md)
