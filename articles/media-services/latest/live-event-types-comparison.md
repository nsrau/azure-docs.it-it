---
title: Tipi di LiveEvent Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra una tabella dettagliata che confronta i tipi di Live.
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
ms.openlocfilehash: 884cf8d913cec038df3b38c8af2ed0a67bd8060d
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802238"
---
# <a name="live-event-types-comparison"></a>Confronto tra tipi di eventi live

In Servizi multimediali di Azure, un [evento live](https://docs.microsoft.com/rest/api/media/liveevents) può essere di due tipi: codifica live e pass-through. 

## <a name="types-comparison"></a>Confronto tra tipi 

Nella tabella seguente vengono confrontate le funzionalità dei tipi di evento Live. I tipi vengono impostati durante la creazione con [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None** : un codificatore Live locale invia un flusso a più velocità in bit. I flussi inseriti passano attraverso l'evento live senza ulteriori elaborazioni. 
* **LiveEventEncodingType. standard** : un codificatore Live locale invia un flusso a bitrate singolo all'evento Live e servizi multimediali crea più flussi a bitrate multipli. Se il feed di contributo è di risoluzione 720p o superiore, il set di impostazioni di **Default720p** codifica un set di 6 coppie di risoluzione/velocità in bit (i dettagli seguiranno più avanti in questo articolo).
* **LiveEventEncodingType. Premium1080p** : un codificatore Live locale invia un flusso a bitrate singolo all'evento Live e servizi multimediali crea più flussi a bitrate multipli. Il set di impostazioni Default1080p specifica il set di output di coppie di risoluzione/velocità in bit (i dettagli sono seguiti più avanti nell'articolo). 

| Funzionalità | Evento live pass-through | Evento live standard o Premium1080p |
| --- | --- | --- |
| Input a bitrate singolo codificato in bitrate multipli nel cloud |No |Sì |
| Risoluzione video massima per feed di contributo |4K (4096 x 2160 a 60 fotogrammi/sec) |1080p (1920 x 1088 a 30 fotogrammi/sec)|
| Livelli massimi consigliati per feed di contributo|Fino a 12|Un audio|
| Livelli massimi nell'output| Uguale all'input|Fino a 6 (vedere di seguito i set di impostazioni di sistema)|
| Larghezza di banda aggregata massima per feed di contributo|60 Mbps|N/D|
| Velocità in bit massima per un singolo livello all'interno del contributo |20 Mbps|20 Mbps|
| Supporto per tracce audio in più lingue|Sì|No|
| Codec video di input supportati |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codec video di output supportati|Uguale all'input|H.264/AVC|
| Profondità bit video di input e output supportata|Fino a 10 bit incluso HDR 10/HLG|8 bit|
| Codec audio di input supportati|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codec audio di output supportati|Uguale all'input|AAC-LC|
| Risoluzione video massima del video di output|Uguale all'input|Standard-720p, Premium1080p-1080p|
| Frequenza massima dei fotogrammi del video di input|60 frame al secondo|Standard o Premium1080p-30 frame al secondo|
| Protocolli di input|RTMP, MP4 frammentato (Smooth Streaming)|RTMP, MP4 frammentato (Smooth Streaming)|
| Prezzo|Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) e fare clic sulla scheda "Video live"|Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) e fare clic sulla scheda "Video live"|
| Tempo di esecuzione massimo| 24 ore su 24, 365 giorni all'anno, live linear | 24 ore x 365 giorni, linea dinamica (anteprima)|
| Possibilità di trasferire dati dei sottotitoli CEA 608/708 integrati|Yes|Yes|
| Supporto per l'inserimento di slate|No|No|
| Supporto per annunci pubblicitari tramite API| No|No|
| Supporto per annunci pubblicitari tramite messaggi in banda SCTE-35|Sì|Sì|
| Possibilità di recuperare brevi fasi di stallo in feed di contributo|Sì|Parziale|
| Supporto per GOP di input non uniformi|Yes|No - La durate GOP dell’input deve essere fissa|
| Supporto per input con frequenza dei fotogrammi variabile|Sì|No: l'input deve essere una frequenza di fotogrammi fissa. Sono tollerate lievi variazioni, ad esempio durante scene ad alta velocità. Tuttavia, il feed di contributo non può eliminare la frequenza dei fotogrammi, ad esempio a 15 fotogrammi al secondo.|
| Arresto automatico dell'evento live in caso di perdita del feed di input|No|Dopo 12 ore, se nessun LiveOutput è in esecuzione|

## <a name="system-presets"></a>Set di impostazioni di sistema

Le risoluzioni e le velocità in bit contenute nell'output del codificatore Live sono determinate dal [set di impostazioni](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Se si usa un codificatore Live **standard** (LiveEventEncodingType. standard), il set di impostazioni *Default720p* specifica un set di 6 coppie di risoluzione/bitrate descritte di seguito. In caso contrario, se si usa un codificatore Live **Premium1080p** (LiveEventEncodingType. Premium1080p), il set di impostazioni *Default1080p* specifica il set di output di coppie di risoluzione/velocità in bit.

> [!NOTE]
> Non è possibile applicare il set di impostazioni Default1080p a un evento Live se è stato configurato per la codifica live standard. si riceverà un errore. Si otterrà inoltre un errore se si tenta di applicare il set di impostazioni Default720p a un codificatore Premium1080p Live.

### <a name="output-video-streams-for-default720p"></a>Flussi video di output per Default720p

Se il feed di contributo è di risoluzione 720p o superiore, il set di impostazioni **Default720p** codifica il feed nei 6 livelli seguenti. Nella tabella riportata di seguito, la velocità in bit è in Kbps, MaxFPS rappresenta il numero massimo di frame consentito (in frame al secondo), il profilo rappresenta il profilo H. 264 utilizzato.

| Bitrate | Larghezza | Altezza | MaxFPS | Profilo |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alto |
| 2200 |960 |540 |30 |Alto |
| 1350 |704 |396 |30 |Alto |
| 850 |512 |288 |30 |Alto |
| 550 |384 |216 |30 |Alto |
| 200 |340 |192 |30 |Alto |

> [!NOTE]
> Se è necessario personalizzare il set di impostazioni per la codifica live, aprire un ticket di supporto tramite il portale di Azure. È necessario specificare la tabella di risoluzione e la velocità in bit desiderata. Verificare che vi sia un solo livello a 720p e un massimo di 6 livelli. Specificare anche che si richiede un set di impostazioni per un codificatore Live standard.
> I valori specifici della velocità in bit e delle risoluzioni possono essere regolati nel tempo

### <a name="output-video-streams-for-default1080p"></a>Flussi video di output per Default1080p

Se il feed di contributo è di risoluzione 1080p, il set di impostazioni **Default1080p** codifica il feed nei 6 livelli seguenti.

| Bitrate | Larghezza | Altezza | MaxFPS | Profilo |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Alto |
| 3000 |1280 |720 |30 |Alto |
| 1600 |960 |540 |30 |Alto |
| 800 |640 |360 |30 |Alto |
| 400 |480 |270 |30 |Alto |
| 200 |320 |180 |30 |Alto |

> [!NOTE]
> Se è necessario personalizzare il set di impostazioni per la codifica live, aprire un ticket di supporto tramite il portale di Azure. È necessario specificare la tabella di risoluzione e la velocità in bit desiderata. Verificare che sia presente un solo livello a 1080p e al massimo 6 livelli. Specificare anche che si richiede un set di impostazioni per un codificatore Premium1080p Live.
> I valori specifici della velocità in bit e delle risoluzioni possono essere modificati nel tempo.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Flusso audio di output per Default720p e Default1080p

Per i set di impostazioni *Default720p* e *Default1080p* , l'audio viene codificato in stereo AAC-LC a 128 kbps. La frequenza di campionamento segue quella della traccia audio nel feed di contributo.

## <a name="implicit-properties-of-the-live-encoder"></a>Proprietà implicite del codificatore Live

La sezione precedente descrive le proprietà del codificatore Live che possono essere controllate in modo esplicito tramite il set di impostazioni, ad esempio il numero di livelli, le risoluzioni e le velocità in bit. In questa sezione vengono chiarite le proprietà implicite.

### <a name="group-of-pictures-gop-duration"></a>Durata del gruppo di immagini (GOP)

Il codificatore Live segue la struttura [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) del feed di contributo, che indica che i livelli di output avranno la stessa durata GOP. È quindi consigliabile configurare il codificatore locale per produrre un feed di contributo con durata GOP fissa (in genere 2 secondi). In questo modo, i flussi HLS e MPEG DASH in uscita dal servizio hanno anche durate fisse. È probabile che le piccole variazioni nelle durate GOP siano tollerate dalla maggior parte dei dispositivi.

### <a name="frame-rate"></a>Frequenza dei fotogrammi

Il codificatore Live segue anche le durate dei singoli fotogrammi video nel feed di contributo, il che significa che i livelli di output avranno frame con le stesse durate. È quindi consigliabile configurare il codificatore locale per produrre un feed di contributo con frequenza dei fotogrammi fissa (al massimo 30 frame al secondo). In questo modo si garantisce che i flussi HLS e MPEG DASH in uscita dal servizio abbiano anche una durata fissa delle frequenze dei frame. La maggior parte dei dispositivi può tollerare variazioni minime nelle frequenze dei frame, ma non vi è alcuna garanzia che il codificatore Live produrrà un output che verrà riprodotto correttamente. Il codificatore Live locale non deve eliminare i frame, ad esempio in condizioni di batteria insufficiente) o variare in alcun modo la frequenza dei fotogrammi.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Risoluzione dei livelli di feed e output dei contributi

Il codificatore Live è configurato per evitare la conversione del feed di contributo. Di conseguenza la risoluzione massima dei livelli di output non supera quella del feed di contributo.

Se ad esempio si invia un feed di contributo a 720p a un evento Live configurato per la codifica live Default1080p, l'output avrà solo 5 livelli, a partire da 720p in 3Mbps, fino a 1080p a 200 Kbps. In alternativa, se si invia un feed di contributo in 360p a un evento Live configurato per la codifica live standard, l'output conterrà 3 livelli (alle risoluzioni di 288p, 216P e 192p). Nel caso di degenerazione, se si invia un feed di contributo di, ad esempio, 160x90 pixel a un codificatore Live standard, l'output conterrà un livello alla risoluzione 160x90 con la stessa velocità in bit del feed di contributo.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Velocità in bit del feed di contributi e dei livelli di output

Il codificatore Live viene configurato in modo da rispettare le impostazioni della velocità in bit nel set di impostazioni, indipendentemente dalla velocità in bit del feed di contributo. Di conseguenza, il bitrate dei livelli di output può superare quello del feed di contributo. Se, ad esempio, si invia un feed di contributo con una risoluzione di 720p a 1 Mbps, i livelli di output rimarranno invariati rispetto alla [tabella](live-event-types-comparison.md#output-video-streams-for-default720p) precedente.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica dello streaming live](live-streaming-overview.md)
