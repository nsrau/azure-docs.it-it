---
title: Tipi di LiveEvent Servizi multimediali di Azure | Microsoft Docs
description: In Servizi multimediali di Azure un evento live può essere impostato su una codifica *pass-through* o *live.* Questo articolo mostra una tabella dettagliata che confronta i tipi di eventi live.
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
ms.openlocfilehash: a28d4d96f643c12eeb6aa542db2c6af06f4fd954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244649"
---
# <a name="live-event-types-comparison"></a>Confronto tra tipi di eventi live

In Servizi multimediali di Azure, un [evento live](https://docs.microsoft.com/rest/api/media/liveevents) può essere impostato su un *pass-through* (un codificatore live locale invia un flusso a bitrate multiplo) o una *codifica live* (un codificatore live locale invia un singolo flusso di bitrate). 

In questo articolo vengono confrontate le funzionalità dei tipi di evento live.

## <a name="types-comparison"></a>Confronto tra tipi 

Nella tabella seguente vengono confrontate le funzionalità dei tipi di evento live. I tipi vengono impostati durante la creazione utilizzando [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None:** un codificatore live locale invia un flusso a bitrate multiplo. I flussi ingeriti passano attraverso l'evento live senza ulteriori elaborazioni. Definito anche evento live pass-through.
* **LiveEventEncodingType.Standard:** un codificatore live locale invia un singolo flusso di bitrate a Live Event e Servizi multimediali crea più flussi di bitrate. Se il feed di contribuzione ha una risoluzione pari o superiore a 720p, il predefinito **Default720p** codicherà un set di 6 coppie risoluzione/bitrate (i dettagli seguiranno più avanti nell'articolo).
* **LiveEventEncodingType.Premium1080p:** un codificatore live locale invia un singolo flusso di bitrate all'evento live e Servizi multimediali crea più flussi di bitrate. Il preset Default1080p specifica il set di output di coppie di risoluzione/bitrate (i dettagli seguono più avanti nell'articolo). 

| Funzionalità | Evento live pass-through | Evento live Standard o Premium1080p |
| --- | --- | --- |
| Input a bitrate singolo codificato in bitrate multipli nel cloud |No |Sì |
| Risoluzione video massima per feed di contributo |4K (4096 x 2160 a 60 fotogrammi/sec) |1080p (1920 x 1088 a 30 fotogrammi/sec)|
| Livelli massimi consigliati per feed di contributo|Fino a 12|Un audio|
| Livelli massimi nell'output| Uguale all'input|Fino a 6 (vedere Impostazioni predefinite di sistema di seguito)|
| Larghezza di banda aggregata massima per feed di contributo|60 Mbps|N/D|
| Velocità in bit massima per un singolo livello all'interno del contributo |20 Mbps|20 Mbps|
| Supporto per tracce audio in più lingue|Sì|No|
| Codec video di input supportati |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codec video di output supportati|Uguale all'input|H.264/AVC|
| Profondità bit video di input e output supportata|Fino a 10 bit incluso HDR 10/HLG|8 bit|
| Codec audio di input supportati|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codec audio di output supportati|Uguale all'input|AAC-LC|
| Risoluzione video massima del video di output|Uguale all'input|Standard - 720p, Premium1080p - 1080p|
| Frequenza fotogrammi massima del video di input|60 fotogrammi/secondo|Standard o Premium1080p - 30 fotogrammi/secondo|
| Protocolli di input|RTMP, MP4 frammentato (Smooth Streaming)|RTMP, MP4 frammentato (Smooth Streaming)|
| Price|Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) e fare clic sulla scheda "Video live"|Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) e fare clic sulla scheda "Video live"|
| Tempo di esecuzione massimo| 24 ore su 24, 365 giorni all'anno, live linear | 24 ore x 365 giorni, live lineare (anteprima)|
| Possibilità di trasferire dati dei sottotitoli CEA 608/708 integrati|Sì|Sì|
| Possibilità di attivare la trascrizione dal vivo|Sì|Sì|
| Supporto per l'inserimento di slate|No|No|
| Supporto per annunci pubblicitari tramite API| No|No|
| Supporto per annunci pubblicitari tramite messaggi in banda SCTE-35|Sì|Sì|
| Possibilità di recuperare brevi fasi di stallo in feed di contributo|Sì|Parziale|
| Supporto per GOP di input non uniformi|Sì|No - La durate GOP dell’input deve essere fissa|
| Supporto per input con frequenza dei fotogrammi variabile|Sì|No: l'input deve essere una frequenza di fotogrammi fissa. Sono tollerate lievi variazioni, ad esempio durante scene ad alta velocità. Ma l'avanzamento contributo non può far cadere la frequenza fotogrammi (ad esempio, a 15 fotogrammi al secondo).|
| Arresto automatico dell'evento live in caso di perdita del feed di input|No|Dopo 12 ore, se nessun LiveOutput è in esecuzione|

## <a name="system-presets"></a>Predefiniti di sistema

Le risoluzioni e le velocità in bit contenute nell'output del codificatore live sono determinate dal [file presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Se si utilizza un codificatore live **standard** (LiveEventEncodingType.Standard), il preset *Default720p* specifica un set di 6 coppie di risoluzione/bitrate descritte di seguito. In caso contrario, se si utilizza un codificatore live **Premium1080p** (LiveEventEncodingType.Premium1080p), il predefinito *Default1080p* preimpostato specifica il set di output di coppie di risoluzione/bitrate.

> [!NOTE]
> Non è possibile applicare il predefinito Default1080p a un evento live se è stato configurato per la codifica live Standard - verrà visualizzato un errore. Si otterrà anche un errore se si tenta di applicare il Preset Default720p a un codificatore live Premium1080p.

### <a name="output-video-streams-for-default720p"></a>Flussi video di output per Default720p

Se il feed di contribuzione ha una risoluzione pari o superiore a 720p, il predefinito **Default720p** codificherà il feed nei 6 livelli successivi. Nella tabella seguente, Bitrate è in kbps, MaxFPS rappresenta che la frequenza fotogrammi massima consentita (in frame/secondo), Profilo rappresenta il profilo H.264 utilizzato.

| Bitrate | Larghezza | Altezza: | MaxFPS | Profilo |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alto |
| 2200 |960 |540 |30 |Alto |
| 1350 |704 |396 |30 |Alto |
| 850 |512 |288 |30 |Alto |
| 550 |384 |216 |30 |Alto |
| 200 |340 |192 |30 |Alto |

> [!NOTE]
> Se è necessario personalizzare il predefinito di codifica live, aprire un ticket di supporto tramite il portale di Azure.If you need to customize the live encoding preset, please open a support ticket via Azure Portal. È necessario specificare la tabella di risoluzione e la velocità in bit desiderata. Verificare che vi sia un solo livello a 720p e un massimo di 6 livelli. Specificare inoltre che si richiede un predefinito per un codificatore live standard.
> I valori specifici delle velocità in bit e delle risoluzioni possono essere regolati nel tempo

### <a name="output-video-streams-for-default1080p"></a>Flussi video di output per Default1080p

Se il feed di contribuzione ha una risoluzione di 1080p, il predefinito **Default1080p** codifica il feed nei 6 livelli seguenti.

| Bitrate | Larghezza | Altezza: | MaxFPS | Profilo |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Alto |
| 3000 |1280 |720 |30 |Alto |
| 1600 |960 |540 |30 |Alto |
| 800 |640 |360 |30 |Alto |
| 400 |480 |270 |30 |Alto |
| 200 |320 |180 |30 |Alto |

> [!NOTE]
> Se è necessario personalizzare il predefinito di codifica live, aprire un ticket di supporto tramite il portale di Azure.If you need to customize the live encoding preset, please open a support ticket via Azure Portal. È necessario specificare la tabella di risoluzione e la velocità in bit desiderata. Verificare che vi sia un solo livello a 1080p e al massimo 6 strati. Specificare inoltre che si sta richiedendo un predefinito per un codificatore live Premium1080p.
> I valori specifici delle velocità in bit e delle risoluzioni possono essere regolati nel tempo.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Flusso audio di output per Default720p e Default1080p

Per entrambi i predefiniti *Default720p e* *Default1080p,* l'audio viene codificato in stereo AAC-LC a 128 kbps. La frequenza di campionamento segue quella della traccia audio nel feed di contribuzione.

## <a name="implicit-properties-of-the-live-encoder"></a>Proprietà implicite del codificatore live

La sezione precedente descrive le proprietà del codificatore live che possono essere controllate in modo esplicito, tramite il preset, ad esempio il numero di livelli, le risoluzioni e i bitrate. In questa sezione vengono clarinfie le proprietà implicite.

### <a name="group-of-pictures-gop-duration"></a>Durata del gruppo di immagini (GOP)

Il codificatore live segue la struttura [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) del feed di contributo, il che significa che i livelli di output avranno la stessa durata del GOP. Pertanto, è consigliabile configurare il codificatore locale per produrre un feed di contribuzione con durata GOP fissa (in genere 2 secondi). Ciò garantirà che i flussi HLS e MPEG DASH in uscita dal servizio abbiano anche durate GOP fisse. Piccole variazioni nella durata del GOP sono suscettibili di essere tollerate dalla maggior parte dei dispositivi.

### <a name="frame-rate"></a>Frequenza dei fotogrammi

Il codificatore live segue anche le durate dei singoli fotogrammi video nel feed di contributo, il che significa che i livelli di output avranno fotogrammi con le stesse durate. Pertanto, è consigliabile configurare il codificatore locale per produrre un feed di contribuzione con frequenza fotogrammi fissa (al massimo 30 fotogrammi al secondo). In questo modo si garantisce che i flussi HLS e MPEG DASH in uscita dal servizio abbiano anche una durata di fotogrammi fissa. Piccole variazioni nella frequenza fotogrammi possono essere tollerate dalla maggior parte dei dispositivi, ma non vi è alcuna garanzia che l'encoder live produrrà un output che verrà riprodotto correttamente. Il codificatore live locale non dovrebbe eliminare i fotogrammi (ad esempio in condizioni di batteria insufficiente) o variare in qualsiasi modo la frequenza fotogrammi.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Risoluzione dei livelli di avanzamento e output dei contributi

Il codificatore live è configurato per evitare l'upconverting del feed di contribuzione. Di conseguenza, la risoluzione massima dei livelli di output non supererà quella del feed di contribuzione.

Ad esempio, se invii un feed di contributo a 720p a un evento live configurato per la codifica live Default1080p, l'output avrà solo 5 livelli, a partire da 720p a 3Mbps, scendendo a 1080p a 200 kbps. Oppure, se invii un feed di contributo a 360p in un evento live configurato per la codifica live Standard, l'output conterrà 3 livelli (con risoluzioni di 288p, 216p e 192p). Nel caso degenerato, se si invia un feed di contribuzione di, ad esempio, 160x90 pixel a un codificatore live Standard, l'output conterrà un livello con risoluzione 160x90 con la stessa velocità in bit del feed di contribuzione.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Bitrate dei livelli di avanzamento e output dei contributi

Il codificatore live è configurato per rispettare le impostazioni di bitrate nel preset, indipendentemente dalla velocità in bit del feed di contributo. Di conseguenza, la velocità in bit dei livelli di output può superare quella del feed di contribuzione. Ad esempio, se si invia un feed di contribuzione con una risoluzione di 720p a 1 Mbps, i layer di output rimarranno gli stessi della [tabella](live-event-types-comparison.md#output-video-streams-for-default720p) precedente.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica dello streaming live](live-streaming-overview.md)
