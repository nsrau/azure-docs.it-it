---
title: Esaminare l'output di Video Indexer di Azure | Microsoft Docs
description: In questo argomento viene esaminato l'output di Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cflower
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 7f61833d70cea3a6d176f5a08eb0db1c30e70e86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "35378653"
---
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>Esaminare l'output di Video Indexer generato dall'API v1

> [!Note]
> Le API v1 di Video Indexer sono ora deprecate e verranno rimosse l'1 agosto 2018. È consigliabile iniziare a usare le API v2 di Video Indexer per evitare interruzioni del servizio.
>
> Per le attività di sviluppo con le API Video Indexer v2, fare riferimento alle istruzioni disponibili [qui](https://api-portal.videoindexer.ai/). 

Quando si chiama l'API **Get Breakdowns** (Ottieni scomposizioni) e lo stato della risposta è OK, viene restituito un output JSON dettagliato come contenuto della risposta. Il contenuto JSON include le informazioni dettagliate sul video specificato, tra cui trascrizioni, OCR e persone. I dettagli includono parole chiave (argomenti), visi e blocchi. Ogni blocco include intervalli di tempo, righe di trascrizione, righe OCR, sentiment, visi e anteprime dei blocchi.

È possibile usare l'API **Get Breakdowns** (Ottieni scomposizioni) per ottenere la scomposizione completa di un video sotto forma di contenuto JSON.  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
È anche possibile esaminare visivamente le informazioni dettagliate riepilogate del video, premendo il pulsante **Play** del video nel portale di Video Indexer. Per altre informazioni, vedere [Visualizzare e analizzare le informazioni dettagliate per i video](video-indexer-view-edit.md).

![Informazioni dettagliate](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

In questo articolo viene esaminato il contenuto JSON restituito dall'API **Get Breakdowns** (Ottieni scomposizioni). Potrebbe risultare utile vedere l'articolo relativo ai [concetti](video-indexer-concepts.md).

> [!NOTE]
> La scadenza di tutti i token di accesso in Video Indexer è di un'ora.

## <a name="root-elements"></a>Elementi radice

Attributo | DESCRIZIONE
---|---
id|ID del video. Ad esempio, 63c6d532ff.
partition|Partizione logica che l'utente può specificare durante il caricamento per consentirne la ricerca in un secondo momento.
name|Nome del video. Ad esempio, Monitoraggio di Azure.
description|Descrizione del video. Ad esempio, "John Kemnetz e Scott Hanselman illustrano come sbloccare le potenzialità dei dati di monitoraggio di Azure con Monitoraggio di Azure".
userName|Autore del video. Ad esempio, video di Channel9.
createTime |Ora di creazione. Ad esempio, 2017-03-31T16:36:41.4504249+00:00.
privacyMode|Il video può avere una delle modalità seguenti: **Private** o **Public**. **Public**: il video è visibile per tutti gli utenti nell'account e per chiunque disponga di un collegamento al video. **Private**: il video è visibile per tutti gli utenti nell'account.
isOwned|True, se l'utente corrente è proprietario del video. In caso contrario, false.  
isBase|True, se la scomposizione è basata su un video di origine. False, se la scomposizione è relativa a una playlist derivata da un'altra scomposizione.
durationInSeconds|Durata del video.
summarizedInsights|Contiene un elemento [summarizedInsights](#summarizedInsights).
breakdowns|Può contenere uno o più elementi [breakdowns](#breakdowns)
social|Contiene un elemento **social** che indica il numero di Mi piace e di visualizzazioni del video.

## <a name="summarizedinsights"></a>summarizedInsights

Questa sezione mostra il riepilogo delle informazioni dettagliate.

Attributo | DESCRIZIONE
---|---
name|Nome del video. Ad esempio, Monitoraggio di Azure.
shortId|ID del video. Ad esempio, 63c6d532ff.
privacyMode|La scomposizione può avere una delle modalità seguenti: **Private** o **Public**. **Public**: il video è visibile per tutti gli utenti nell'account e per chiunque disponga di un collegamento al video. **Private**: il video è visibile per tutti gli utenti nell'account.
duration|Contiene una durata che definisce il tempo associato a un'informazione dettagliata. La durata è espressa in secondi.
thumbnailUrl|URL completo dell'anteprima del video. Ad esempio, "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Si noti che se il video è privato l'URL contiene un token di accesso di un'ora. Dopo un'ora l'URL non sarà più valido e sarà necessario ottenere di nuovo la scomposizione con un nuovo URL incluso oppure chiamare GetAccessToken per ottenere un nuovo token di accesso e creare manualmente l'URL completo ('https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]').
faces|Può contenere uno o più elementi [faces](#faces)
topics|Può contenere uno o più elementi [topics](#topics)
sentiments|Può contenere uno o più elementi [sentiments](#sentiments)
audioEffects| Può contenere uno o più elementi [audioEffects](#audioEffects)
brands| Può contenere zero o più elementi [brands](#brands)
Statistiche|Per altre informazioni, vedere [Statistiche](#Statistics)
.
### <a name="statistics"></a>Statistiche

|NOME|DESCRIZIONE|
|---|---|
|CorrespondenceCount|Numero di corrispondenze nel video.|
|WordCount|Numero di parole per ogni voce.|
|SpeakerNumberOfFragments|Quantità di frammenti della voce in un video.|
|SpeakerLongestMonolog|Monologo più lungo della voce. Se la voce comprende periodi di silenzio all'interno del monologo, questi vengono inclusi. I periodi di silenzio all'inizio e alla fine del monologo vengono rimossi.| 
|SpeakerTalkToListenRatio|Il calcolo è basato sul tempo impiegato per il monologo della voce (senza i periodi di silenzio intermedi) diviso per il tempo totale del video. Il tempo viene arrotondato alla terza posizione decimale.|

## <a name="breakdowns"></a>breakdowns

Questa sezione mostra gli elementi inclusi nelle informazioni dettagliate.

Attributo | DESCRIZIONE
---|---
id|ID della scomposizione. Ad esempio, 63c6d532ff.
state|Stato di elaborazione dell'ID scomposizione specificato. Può avere uno dei valori seguenti: Uploaded, Processing, Processed, Failed.
processingProgress|Stato di avanzamento. Ad esempio 10%.
externalId| È possibile configurare il valore externalId durante il caricamento. Ad esempio, "4f9c3500-eca7-4ab3-987e-a745017af698". È quindi possibile cercare successivamente i video tramite questo ID esterno.
externalUrl|È possibile configurare il valore externalUrl durante il caricamento. 
metadata|È possibile configurare il valore metadata durante il caricamento. 
insights|Può contenere uno o più elementi [insights](#insights)
thumbnailUrl|URL completo dell'anteprima del video. Ad esempio, "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO..". Si noti che se il video è privato l'URL contiene un token di accesso di un'ora. Dopo un'ora l'URL non sarà più valido e sarà necessario ottenere di nuovo la scomposizione con un nuovo URL incluso oppure chiamare GetAccessToken per ottenere un nuovo token di accesso e creare manualmente l'URL completo ('https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]').
publishedUrl|URL pubblicato. Ad esempio, "https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest".
viewToken|Token di connessione.
sourceLanguage|Lingua di origine. Sono supportate le lingue seguenti: cinese, inglese, francese, tedesco, italiano, giapponese, portoghese, russo, spagnolo.
Linguaggio|Lingua della trascrizione.

## <a name="insights"></a>insights

Attributo | DESCRIZIONE 
---|---
transcriptBlocks|Può contenere uno o più elementi [transcriptBlocks](#transcriptBlocks)
topics|Può contenere uno o più elementi [topics](#topics)
faces|Può contenere uno o più elementi [faces](#faces)
participants|Può contenere uno o più elementi [participants](#participants)
contentModeration|Può contenere un elemento [contentModeration](#contentModeration)
audioEffectsCategories|Può contenere uno o più elementi [audioEffectsCategories](#audioEffectsCategories)

## <a name="faces"></a>faces

### <a name="summarizedinsights"></a>summarizedInsights

Gli elementi **faces** visualizzati in **summarizedInsights** presentano un riepilogo di ogni viso rilevato nel video.

Attributo | DESCRIZIONE 
---|---
id|ID di una persona. Ad esempio, 11775.
shortId|ID breve. È possibile che una playlist derivi da diverse scomposizioni, quindi questo ID è necessario per individuare la scomposizione specifica di origine per ogni viso.  
name|Se il viso viene riconosciuto, viene aggiunto il nome della persona. Ad esempio, "Scott Hanselman". Se il viso è sconosciuto, viene aggiunto il testo "Unknown #". 
description|Se il viso viene riconosciuto, la descrizione viene popolata in base alla ricerca dell'API Bing. In caso contrario, la descrizione è **null**.
title|Se il viso viene riconosciuto, la descrizione viene popolata in base alla ricerca dell'API Bing. In caso contrario, il titolo è **null**.
thumbnailFullUrl|URL completo dell'anteprima del viso. Ad esempio, "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Si noti che se il video è privato l'URL contiene un token di accesso di un'ora. Dopo un'ora l'URL non sarà più valido e sarà necessario ottenere di nuovo la scomposizione con un nuovo URL incluso oppure chiamare GetAccessToken per ottenere un nuovo token di accesso e creare manualmente l'URL completo ('https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]').
appearances|Può contenere uno o più elementi [appearances](#appearances)
seenDuration|Durata della visualizzazione del viso, in secondi.
seenDurationRatio|Presenza rispetto alla durata del video (0-1).

### <a name="breakdown-insights"></a>Informazioni dettagliate sulle scomposizioni

Gli elementi **faces** visualizzati in **breakdowns** forniscono dettagli su ogni viso rilevato nel video.

Attributo | DESCRIZIONE 
---|---
id|ID di una persona. Ad esempio, 11775.
bingId|
name|Se il viso viene riconosciuto, viene aggiunto il nome della persona. Ad esempio, "Scott Hanselman". Se il viso è sconosciuto, viene aggiunto il testo "Unknown #". 
thumbnailId|Ad esempio, 616468f0-1636-4efa-94e7-262f2e575059.
description|Se il viso viene riconosciuto, la descrizione viene popolata in base alla ricerca dell'API Bing. In caso contrario, la descrizione è **null**.
title|Se il viso viene riconosciuto, la descrizione viene popolata in base alla ricerca dell'API Bing. In caso contrario, il titolo è **null**.
imageUrl|Questo URL fa riferimento a un'immagine tratta dal video di origine.  
confidence|Punteggio di attendibilità. Un punteggio elevato corrisponde a una maggiore attendibilità.
knownPersonId|ID di una persona nota, ad esempio una celebrità. Se una persona non è nota, l'ID contiene zeri. Ad esempio, e3eaff5f-ee1b-4eac-80ce-ebac47aadf64.

## <a name="topics"></a>topics

### <a name="summarizedinsights"></a>summarizedInsights

Gli elementi **topics** visualizzati in **summarizedInsights** presentano un riepilogo di ogni argomento rilevato nel video.

Attributo | DESCRIZIONE 
---|---
name|Nome dell'argomento, ad esempio "Azure". 
appearances|Può contenere uno o più elementi [appearances](#appearances).
isTranscript|True, se rilevato in una trascrizione. False, se rilevato in OCR.

### <a name="breakdown-insights"></a>Informazioni dettagliate sulle scomposizioni

Gli elementi **topics** visualizzati in **breakdowns** forniscono dettagli su ogni argomento rilevato nel video.

|Attributo | DESCRIZIONE |
|---|---|
|id|ID univoco dell'argomento.|
|name|Nome dell'argomento.|
|stem|Questo attributo non viene usato attualmente.|
|words|Questo attributo non viene usato attualmente.|
|rank|Punteggio della rilevanza. Un punteggio elevato corrisponde a una maggiore rilevanza.|

## <a name="sentiments"></a>sentiments

Attributo | DESCRIZIONE
---|---
sentimentKey| Sono attualmente supportati i sentiment seguenti: Positive, Neutral, Negative. 
appearances|Può contenere uno o più elementi [appearances](#appearances)|.
seenDurationRatio|Presenza rispetto alla durata del video (0-1).

## <a name="audioeffects"></a>audioEffects

Attributo | DESCRIZIONE 
---|---
audioEffectKey| I valori validi sono: Speech, Silence, HandClaps.
appearances|Può contenere uno o più elementi [appearances](#appearances)
seenDurationRatio|Presenza rispetto alla durata del video (0-1).
seenDuration|Durata della presenza dell'effetto audio, in secondi.

## <a name="appearances"></a>appearances

Attributo | DESCRIZIONE 
---|---
startTime| Valore temporale.
endTime|Valore temporale.
startSeconds| Valore temporale.
endSeconds| Valore temporale.

## <a name="participants"></a>participants

Attributo | DESCRIZIONE 
---|---
id|ID del partecipante.
name|Nome del partecipante. Ad esempio, Speaker #1.
pictureUrl|L'attributo **pictureUrl** è riservato per un utilizzo futuro.

## <a name="contentmoderation"></a>contentModeration

Attributo | DESCRIZIONE 
---|---
adultClassifierValue|Livello di attendibilità relativo alla presenza di contenuti per adulti nel video.
racyClassifierValue|Livello di attendibilità relativo alla presenza di contenuti audaci nel video.
bannedWordsCount|Numero di termini volgari. 
bannedWordsRatio|Percentuale di termini volgari rispetto al numero totale di parole.
reviewRecommended|Valore booleano che indica se il video deve essere verificato manualmente.
isAdult|Valori booleani che indicano se il video viene considerato un video per adulti dopo la verifica manuale.

## <a name="audioeffectscategories"></a>audioEffectsCategories

Attributo | DESCRIZIONE 
---|---
type|ID della categoria.
key|Può avere uno dei valori seguenti: Speech, Silence, HandClaps. 

## <a name="transcriptblocks"></a>transcriptBlocks

Attributo | DESCRIZIONE
---|---
id|ID del blocco.
lines|Può contenere uno o più elementi [lines](#lines)
sentimentIds|L'attributo **sentimentIds** è riservato per un utilizzo futuro.
thumbnailIds|L'attributo **thumbnailIds** è riservato per un utilizzo futuro.
Valutazione|Sentiment nel blocco (0-1, da negativo a positivo).
faces|Può contenere uno o più elementi [faces](#faces).
ocrs|Può contenere uno o più elementi [ocrs](#ocrs).
audioEffectInstances|Può contenere uno o più elementi [audioEffectInstances](#audioEffectInstances).
scenes|Può contenere uno o più elementi [scenes](#scenes).
annotations|Può contenere zero o più elementi [annotations](#annotations).

## <a name="ocrs"></a>ocrs

Indica il punto nel video in cui è stato trovato il contenuto testuale. 

Attributo | DESCRIZIONE 
---|---
timeRange|Intervallo di tempo nel video originale.
adjustedTimeRange|Intervallo di tempo rispetto alla playlist corrente. Poiché è possibile creare una playlist da righe diverse di video diversi, è possibile prendere un video di un'ora e usarne solo una riga, ad esempio, 10:00-10:15. In questo caso si avrà una playlist con 1 riga, in cui l'intervallo di tempo è 10:00-10:15 ma l'intervallo di tempo rettificato è 00:00-00:15.
lines|Può contenere uno o più elementi [lines](#lines).

## <a name="lines"></a>lines

### <a name="transcriptblocks"></a>transcriptBlocks

Gli elementi **lines** visualizzati in **transcriptBlocks** forniscono una descrizione delle righe di trascrizioni rilevate nel video.

Attributo | DESCRIZIONE 
---|---
id| ID della riga.
timeRange|Intervallo di tempo nel video originale.
adjustedTimeRange|Intervallo di tempo rispetto alla playlist corrente. Poiché è possibile creare una playlist da righe diverse di video diversi, è possibile prendere un video di un'ora e usarne solo una riga, ad esempio, 10:00-10:15. In questo caso si avrà una playlist con 1 riga, in cui l'intervallo di tempo è 10:00-10:15 ma l'intervallo di tempo rettificato è 00:00-00:15.
participantID| ID del parlante per questa riga.
text| Trascrizione.
isIncluded| È sempre true nelle scomposizioni di base. Nelle playlist derivate le righe incluse nel video di origine vengono impostate su isIncluded=true. Tutte le altre righe sono false.

### <a name="ocrs"></a>ocrs

Gli elementi **lines** visualizzati in **ocrs**, forniscono una descrizione delle righe di OCR rilevate nel video.

Attributo | DESCRIZIONE 
---|---
id|ID di OCR.
width|Questo attributo non viene usato attualmente.
height|Questo attributo non viene usato attualmente.
Linguaggio|Lingua del riconoscimento ottico dei caratteri.
textData|Testo risultante dal riconoscimento ottico dei caratteri.
confidence|Punteggio di attendibilità. Un punteggio elevato corrisponde a una maggiore attendibilità.

## <a name="scenes"></a>scenes

Attributo | DESCRIZIONE 
---|---
id|ID della scena.
timeRange|Contiene un elemento **timeRange**.
keyFrame|Indicatore temporale del fotogramma chiave.
shots|Può contenere uno o più elementi [shots](#shots).

## <a name="shots"></a>shots

Attributo | DESCRIZIONE 
---|---
id||ID dello scatto.
timeRange|Contiene un elemento **timeRange**.
keyFrame|Indicatore temporale del fotogramma chiave.

## <a name="audioeffectinstances"></a>audioEffectInstances

Attributo | DESCRIZIONE 
---|---
type|Indice dell'evento audio: Laughter = 1, HandClaps = 2, Music = 3, Speech = 4, Silence = 5
ranges|Può contenere uno o più elementi [ranges](#ranges).

## <a name="ranges"></a>ranges

Gli elementi **ranges** visualizzati in **audioEffectInstances** forniscono una descrizione degli effetti audio rilevati in tali intervalli.

Attributo | DESCRIZIONE 
---|---
timeRange|Intervallo di tempo nel video originale.
adjustedTimeRange|Intervallo di tempo rispetto alla playlist corrente. Poiché è possibile creare una playlist da righe diverse di video diversi, è possibile prendere un video di un'ora e usarne solo una riga, ad esempio, 10:00-10:15. In questo caso si avrà una playlist con 1 riga, in cui l'intervallo di tempo è 10:00-10:15 ma l'intervallo di tempo rettificato è 00:00-00:15.

## <a name="annotations"></a>annotations

Restituisce tag basati su oggetti riconoscibili, esseri viventi, panorami, azioni e modelli visivi.

|Attributo|DESCRIZIONE|
|---|---|
|id|ID dell'annotazione.|
|NOME|Nome dell'annotazione, ad esempio, Person, Athletic game, Black Frames.|
|Appearances|Può contenere uno o più elementi appearances.|

## <a name="brands"></a>brands

Nomi di marchi di aziende e prodotti rilevati nella trascrizione del riconoscimento vocale e/o nell'OCR del video. Non include il riconoscimento visivo dei marchi o il rilevamento dei logo.

Attributo | DESCRIZIONE
---|---
id | ID di un marchio.
name | Nome del marchio da Bing o un marchio personalizzato.  
wikiId | Suffisso dell'URL di Wikipedia del marchio. Ad esempio, "Target_Corporation" è il suffisso di [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
wikiUrl | URL di Wikipedia del marchio, se presente. Ad esempio, [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
confidence | Valore di attendibilità della funzionalità di rilevamento dei marchi di Video Indexer (0-1).
description | Descrizione del marchio estratta da Wikipedia. 
appearances | Può contenere uno o più elementi appearances.
seenDuration | Presenza rispetto alla durata del video (0-1).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare una scomposizione personalizzata, vedere [View and edit Video Indexer insights](video-indexer-view-edit.md) (Visualizzare e modificare le informazioni dettagliate di Video Indexer).

Per informazioni su come incorporare i widget nell'applicazione, vedere [Embed Video Indexer widgets into your applications](video-indexer-embed-widgets.md) (Incorporare i widget di Video Indexer nelle applicazioni). 

## <a name="see-also"></a>Vedere anche 

[API Video Indexer](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[Panoramica di Video Indexer](video-indexer-overview.md)

