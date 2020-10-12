---
title: Riproduzione di registrazioni-Azure
description: È possibile usare l'analisi video in tempo reale su IoT Edge per la registrazione video continua, in cui è possibile registrare video nel cloud per settimane o mesi. È anche possibile limitare la registrazione a clip di interesse, tramite la registrazione basata su eventi. Questo articolo illustra come riprodurre le registrazioni.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6222d2c05b2fe05945d4bcbef6dbb0d64bd4726a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84260384"
---
# <a name="playback-of-recordings"></a>Riproduzione di registrazioni 

## <a name="pre-read"></a>Pre-lettura  

* [Riproduzione di video](video-playback-concept.md)
* [Registrazione continua di video](continuous-video-recording-concept.md)
* [Registrazione di video basata su eventi](event-based-video-recording-concept.md)

## <a name="background"></a>Background  

È possibile usare analisi video in tempo reale su IoT Edge per la [registrazione video continua](continuous-video-recording-concept.md) (CVR), in cui è possibile registrare video nel cloud per settimane o mesi. È anche possibile limitare la registrazione a clip di interesse, tramite [registrazione video basata su eventi](event-based-video-recording-concept.md) (EVR). 

L'account di servizi multimediali è collegato a un account di archiviazione di Azure e, quando si registra un video nel cloud, il contenuto viene scritto in un [asset di servizi multimediali](terminology.md#asset). Servizi multimediali consente di [trasmettere il contenuto](terminology.md#streaming), dopo il completamento della registrazione o mentre la registrazione è in corso. Servizi multimediali fornisce le funzionalità necessarie per distribuire i flussi tramite i protocolli HLS o MPEG-DASH per la riproduzione di dispositivi (client). Per altri dettagli, vedere l'articolo relativo alla [riproduzione video](video-playback-concept.md) . Usare le API di servizi multimediali per generare gli URL di streaming necessari, usati dai client per riprodurre il video & audio. Per costruire l'URL di streaming per un asset, vedere [creare un localizzatore di streaming e gli URL di compilazione](../latest/create-streaming-locator-build-url.md). Una volta creato l'URL di streaming per un asset, è possibile archiviare l'associazione dell'URL con l'origine video, ovvero la fotocamera, nel sistema di gestione dei contenuti.

Ad esempio, quando si esegue il flusso tramite HLS, l'URL di streaming sarà simile a `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Per MPEG-DASH, avrà un aspetto simile a `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` .

Viene restituito un file manifesto che, tra le altre cose, descrive la durata complessiva del flusso che viene recapitato e indica se rappresenta il contenuto pre-registrato o il feed attivo in corso.

### <a name="live-vs-vod"></a>Live rispetto a VoD  

Sono stati creati protocolli di streaming come HLS e MPEG-DASH per gestire scenari come lo streaming di video live, nonché lo streaming di contenuti su richiesta/pre-registrati, come la TV e i film. Per i video live, i client HLS e MPEG-DASH sono progettati per iniziare a giocare a partire dall'ora più recente. Con i film, tuttavia, i visualizzatori si aspettano di essere in grado di iniziare dall'inizio e di spostarsi se scelgono. I manifesti HLS e MPEG-DASH hanno flag che indicano ai client se il video rappresenta un flusso Live o se il contenuto è già registrato.
Questo concetto si applica anche ai flussi HLS e MPEG-DASH da asset che contengono video registrati con analisi video in tempo reale su IoT Edge.

## <a name="browsing-and-selective-playback-of-recordings"></a>Esplorazione e riproduzione selettiva delle registrazioni  

Si consideri lo scenario in cui è stata usata l'analisi video in tempo reale su IoT Edge per registrare video solo dalle 8.00 alle 10.00 nei giorni in cui è stata aperta una scuola, per l'intero anno accademico. O forse si sta registrando video solo da 7 a 19 alle festività nazionali. In uno di questi due scenari, quando si tenta di esplorare e visualizzare la registrazione video, è necessario:

* Un modo per determinare le date e le ore di video presenti in una registrazione.
* Un modo per selezionare una parte, ad esempio dalle 9.00 alle 11.00 per il giorno dei nuovi anni, della registrazione per la riproduzione.

Servizi multimediali fornisce un'API di query (availableMedia) per risolvere il primo problema e i filtri intervallo di tempo (startTime, endTime) per risolvere il secondo.

## <a name="query-api"></a>API di query 

Quando si usa CVR, i dispositivi di riproduzione (client) non possono richiedere un manifesto che copra la riproduzione dell'intera registrazione.  Una registrazione di più anni produrrebbe un file manifesto troppo grande per la riproduzione e potrebbe essere difficile da analizzare in parti utilizzabili sul lato client.  Il client deve conoscere gli intervalli DateTime che contengono dati nella registrazione, in modo da poter eseguire richieste valide senza molto lavoro. Ecco dove entra la nuova API di query: i client possono ora usare questa API sul lato server per individuare il contenuto.

Dove il valore di precisione può essere uno dei seguenti: Year, month, Day o full (come illustrato di seguito). 

|Precision|anno|month|day|completi|
|---|---|---|---|---|
|Query|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|Risposta|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|Risposta di fedeltà completa. Se non si sono verificati Gap, l'inizio sarebbe startTime e end sarebbe endTime.|
|Vincola|&#x2022;startTime <= endTime<br/>&#x2022;devono essere entrambi in formato aaaa; in caso contrario, restituisce un errore.<br/>Il valore di &#x2022;può essere diverso da un numero di anni.<br/>I valori &#x2022;sono inclusivi.|&#x2022;startTime <= endTime<br/>&#x2022;deve essere in formato AAAA-MM; in caso contrario, restituisce un errore.<br/>I valori &#x2022;possono avere una distanza massima di 12 mesi.<br/>I valori &#x2022;sono inclusivi.|&#x2022;startTime <= endTime<br/>&#x2022;deve essere in formato AAAA-MM-GG, in caso contrario restituisce un errore.<br/>I valori &#x2022;possono avere una distanza massima di 31 giorni.<br/>I valori sono inclusivi.|&#x2022;startTime < endTime<br/>I valori di &#x2022;possono essere a un massimo di 25 ore.<br/>I valori &#x2022;sono inclusivi.|

#### <a name="additional-request-format-considerations"></a>Considerazioni aggiuntive sul formato della richiesta

* Tutti gli orari sono in formato UTC
* Il parametro della stringa di query di precisione è obbligatorio.  
* I parametri della stringa di query startTime e endTime sono obbligatori per i valori di mese, giorno e precisione intera.  
* I parametri della stringa di query startTime e endTime sono facoltativi per il valore di precisione Year (nessuno, o entrambi sono supportati).  

    * Se il startTime viene omesso, si presuppone che sia il primo anno nella registrazione.
    * Se il endTime viene omesso, si presuppone che sia l'ultimo anno nella registrazione.
    * Ad esempio, se la registrazione è iniziata in 2011 e continua fino a 2020, quindi:

        * /availableMedia? Precision = Year è uguale a/availableMedia? Precision = Year&startTime = 2011&endTime = 2020
        * /availableMedia? Precision = Year&startTime = 2015 è uguale a/availableMedia? Precision = Year&startTime = 2015&endTime = 2020
        * /availableMedia? Precision = Year&endTime = 2018 è uguale a/availableMedia? Precision = Year&startTime = 2011&endTime = 2018

Se non sono disponibili dati multimediali per gli intervalli di tempo, viene restituito un elenco vuoto.

Se l'asset non contiene una registrazione da un grafico multimediale, viene restituita una risposta HTTP 400 con un messaggio di errore che informa che questa funzionalità è disponibile solo per il contenuto registrato tramite un grafico multimediale.

Se la durata specificata dai parametri è maggiore di quella consentita dall'intervallo di tempo massimo per un determinato tipo di query, viene restituito un HTTP 400 con un messaggio di errore che spiega l'intervallo di tempo massimo consentito per il tipo di query richiesto.

Supponendo che l'intervallo di tempo sia valido per i parametri specificati, non verrà restituito alcun errore per le query che non rientrano nell'intervallo di tempo dei dati nella registrazione.  Ciò significa che se la registrazione è iniziata 7 ore fa, ma il cliente chiede i supporti disponibili da {UtcNow – 24 ore} a UtcNow, si restituiranno solo le {UtcNow – 7} ore di dati.

### <a name="response-format"></a>Formato risposta  

La chiamata availableMedia restituisce un set di valori temporali.

La risposta sarà un corpo JSON, con valori timeRanges che corrispondono a una matrice di date UTC ISO 8601 per l'anno (in formato aaaa), mese (in formato AAAA-MM) o giorno (AAAA-MM-GG), a seconda della precisione richiesta.  Il timeRanges completo conterrà un valore di inizio e di fine formattato come una data/ora UTC ISO 8601 (nel formato AAAA-MM-GGThh: mm: SS. sssZ).

Per la query availableMedia, l'API si disconnette dalla sequenza temporale del video. Eventuali discontinuità nella sequenza temporale vengono visualizzati come gap nella risposta.

#### <a name="response-example-for-availablemedia"></a>Esempio di risposta per availableMedia

##### <a name="example-1-live-recording-with-no-gaps"></a>Esempio 1: registrazione in tempo reale senza gap

Ecco una risposta che Mostra tutti i supporti disponibili per il 21 dicembre 2019, in cui la registrazione è stata completata al 100%. La risposta ha una sola coppia di inizio/fine.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>Esempio 2: registrazione in tempo reale con gap di 2 secondi

Si supponga, per qualche motivo, che la fotocamera non abbia inviato un video valido per un intervallo di 2 secondi in un giorno. Ecco una risposta che Mostra tutti i supporti disponibili per il 21 dicembre 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Esempio 3: registrazione in tempo reale con gap di 8 ore

Si supponga che la fotocamera e/o la struttura locale perdano il consumo di energia per un periodo di 8 ore durante il giorno, dalle 4 UTC alle 12.00 UTC e che non vi sia alcuna fonte di alimentazione di backup. Ecco una risposta che Mostra tutti i supporti disponibili per un tale giorno

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>Esempio 4: registrazione in tempo reale in cui nessun video viene registrato nelle festività estive

Si supponga di aver registrato il video solo quando la scuola si trovava nella sessione e la registrazione è stata interrotta dal 17 giugno al 6 settembre. Una query per i mesi disponibili avrà un aspetto simile al seguente:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

Se, successivamente, vengono richiesti i giorni disponibili in giugno, verrà visualizzato quanto segue:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>Esempio 5: registrazione in tempo reale in cui nessun video viene registrato nei fine settimana o nelle festività

Si supponga di aver registrato il video solo durante l'orario di lavoro. Una query per i giorni disponibili avrà un aspetto simile al seguente.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>Filtri intervallo di tempo

Come indicato in precedenza, questi filtri consentono di selezionare parti della registrazione (ad esempio, dalle 9.00 alle 11.00 per il giorno dei nuovi anni) per la riproduzione. Quando si esegue lo streaming tramite HLS, l'URL di streaming sarà simile a `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Per selezionare una parte della registrazione, è necessario aggiungere un valore startTime e un parametro endTime, ad esempio: `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` . I filtri di intervallo di tempo sono quindi modificatori URL usati per descrivere la parte della sequenza temporale della registrazione inclusa nel manifesto di streaming:

* `starttime` è un indicatore DateTime ISO 8601 che descrive l'ora di inizio desiderata della sequenza temporale del video nel manifesto restituito.
* `endtime` è un indicatore DateTime ISO 8601 che descrive l'ora di fine desiderata della sequenza temporale video restituita nel manifesto.

La lunghezza massima (nel tempo) di un manifesto di questo tipo non può superare 24 ore.

Ecco i vincoli sui filtri.

|startTime| endTime |Risultato|
|---|---|---|
|Assente |Assente |Restituisce la parte più recente del video nell'asset, fino a una lunghezza massima di 4 ore.<br/><br/>Se l'asset non è stato scritto (non sono presenti nuovi dati video in arrivo), viene restituito un manifesto su richiesta (VoD). In caso contrario, viene restituito un manifesto Live.|
|Presente|Assente|    Restituisce un manifesto con qualsiasi video disponibile più recente di startTime, se un manifesto di questo tipo sarebbe inferiore a 24 ore.<br/>Se la lunghezza del manifesto è superiore a 24 ore, viene restituito un errore.<br/>Se l'asset non è stato scritto (non sono presenti nuovi dati video in arrivo), viene restituito un manifesto su richiesta (VoD). In caso contrario, viene restituito un manifesto Live.
|Assente|Presente |Errore: se startTime è presente, endTime è obbligatorio.|
|Presente|Presente|Restituisce un manifesto VoD con qualsiasi video disponibile tra startTime e endTime.<br/>L'intervallo (startTime, endTime) non può superare 24 ore.|

### <a name="response-examples"></a>Esempi di risposta  

#### <a name="example-1-live-recording-with-no-gaps"></a>Esempio 1: registrazione in tempo reale senza gap

Ecco una risposta che Mostra tutti i supporti disponibili per il 21 dicembre 2019, in cui la registrazione è stata completata al 100%. La risposta ha una sola coppia di inizio/fine.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Quando la registrazione è continua, è possibile richiedere manifesti HLS o DASH per qualsiasi parte del tempo all'interno della coppia iniziale/finale, purché l'intervallo sia di 24 ore o meno. Un esempio di richiesta di manifesto HLS di 4 ore per la precedente è:

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>Esempio 2: registrazione in tempo reale con gap di 2 secondi

Si supponga, per qualche motivo, che la fotocamera non abbia inviato un video valido per un intervallo di 2 secondi in un giorno. Ecco una risposta che mostra i supporti disponibili per il 21 dicembre 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Con una registrazione come la precedente, è possibile richiedere manifesti HLS e DASH con qualsiasi coppia startTime/endTime, purché l'intervallo sia inferiore a 24 ore. Se questi valori si sono riferiti al Gap a 04:01:08AM UTC, il manifesto restituito segnalerebbe la discontinuità nella sequenza temporale dei contenuti multimediali, in base alle specifiche pertinenti per tali protocolli.

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Esempio 3: registrazione in tempo reale con gap di 8 ore

Si supponga che la fotocamera e/o la struttura locale perdano il consumo di energia per un periodo di 8 ore durante il giorno, dalle 4 UTC alle 12.00 UTC e che non vi sia alcuna fonte di alimentazione di backup. Ecco una risposta che Mostra tutti i supporti disponibili per tale giorno.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Con una registrazione di questo tipo:

* Se si richiede un manifesto in cui i filtri di intervallo startTime/endTime si trovavano all'interno delle parti "disponibili" della sequenza temporale, vale a dire da mezzanotte alle 4.00 o da mezzogiorno a mezzanotte, il servizio restituisce un manifesto che copre l'ora da startTime a endTime, ad esempio:

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* Se si richiede un manifesto in cui startTime e endTime si trovavano all'interno del "Hole" al centro, ad esempio dalle 8.00 alle 10.00 UTC, il servizio si comporterebbe allo stesso modo di un filtro asset per produrre un risultato vuoto.

    [Si tratta di una richiesta che ottiene una risposta vuota] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* Se si richiede un manifesto in cui solo uno dei startTime o endTime si trova all'interno del ' Hole ', il manifesto restituito includerà solo una parte di tale intervallo di tempo. Blocca il valore startTime o endTime al limite più vicino valido. Se, ad esempio, è stato richiesto un flusso di 3 ore dalle 10.00 alle 13.00, la risposta conterrà una quantità di supporti pari a 1 HR per le 12.00 alle 13.00

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    Il manifesto restituito inizierà a 2019-12-21T12:00:00.000 Z, anche se la richiesta ha richiesto un inizio di 10. Quando si compila un sistema di gestione video con un plug-in del lettore, è necessario prestare attenzione a segnalarlo al visualizzatore. Un visualizzatore ignaro verrebbe confuso per quanto riguarda il motivo per cui la sequenza temporale di riproduzione inizia a mezzogiorno e non le 10.00 come richiesto

## <a name="recording-and-playback-latencies"></a>Latenze di registrazione e riproduzione

Quando si usa analisi video in tempo reale su IoT Edge per registrare in un asset, si specifica una proprietà segmentLength che indica al modulo di aggregare una durata minima del video (in secondi) prima di essere registrata nel cloud. Ad esempio, se segmentLength è impostato su 300, il modulo accumulerà 5 minuti di video prima del caricamento di 1 5 minuti "blocco", quindi passa alla modalità di accumulo per i prossimi 5 minuti e carica nuovamente. L'aumento del segmentLength offre il vantaggio di ridurre i costi delle transazioni di archiviazione di Azure, in quanto il numero di letture e scritture non sarà più frequente di una volta ogni segmentLength secondi.

Di conseguenza, lo streaming del video da servizi multimediali verrà ritardato di almeno molto tempo. 

Un altro fattore che determina la latenza di riproduzione (il ritardo tra il momento in cui un evento si verifica davanti alla fotocamera, il momento in cui può essere visualizzato in un dispositivo di riproduzione) è la durata [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) del gruppo di immagini. Poiché la [riduzione del ritardo dei flussi live con tre semplici tecniche](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) spiega, più lunga è la durata del GOP, più lunga è la latenza. Sono comuni le fotocamere IP usate negli scenari di sorveglianza e sicurezza configurate per l'uso di GOPs più di 30 secondi. Questa operazione ha un notevole effetto sulla latenza complessiva.

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione sulla registrazione video continua](continuous-video-recording-tutorial.md)
