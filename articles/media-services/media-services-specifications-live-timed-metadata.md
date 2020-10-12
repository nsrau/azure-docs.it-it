---
title: Servizi multimediali di Azure-segnalazione dei metadati temporizzati nello streaming live
description: Questa specifica descrive i metodi per segnalare i metadati temporizzati durante l'inserimento e lo streaming in servizi multimediali di Azure. Questo include il supporto per i segnali di metadati temporizzati generici (ID3), nonché la segnalazione SCTE-35 per la segnalazione della condizione di inserimento e di giunzione di annunci.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: f826ee9ef3c9fff0b721a9c79d3c12e0adbd5f7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336395"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Segnalazione dei metadati programmati in streaming live 

Ultimo aggiornamento: 2019-08-22

### <a name="conformance-notation"></a>Nota di conformità

Le parole chiave "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY" e "OPTIONAL" in questo documento vanno interpretate come descritto nella specifica RFC 2119

## <a name="1-introduction"></a>1. Introduzione 

Per segnalare l'inserimento di annunci o eventi di metadati personalizzati in un lettore client, i broadcaster usano spesso i metadati programmati incorporati all'interno del video. Per abilitare questi scenari, servizi multimediali fornisce il supporto per il trasporto di metadati temporizzati dal punto di inserimento del canale di streaming live all'applicazione client.
Questa specifica descrive diverse modalità supportate da servizi multimediali per i metadati temporizzati all'interno di segnali di streaming live.

1. [SCTE-35] segnalazione che è conforme agli standard indicati da [SCTE-35], [SCTE-214-1], [SCTE-214-3] e [RFC8216]

2. [SCTE-35] segnalazione che è conforme alla specifica legacy [Adobe-debutto] per la segnalazione di ad RTMP.
   
3. Modalità di segnalazione dei metadati a tempo generico, per i messaggi che **non** sono [SCTE-35] e che possono contenere [ID3v2] o altri schemi personalizzati definiti dallo sviluppatore di applicazioni.

## <a name="11-terms-used"></a>1,1 termini usati

| Termine                | Definizione                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Interrompi annuncio            | Un percorso o un punto nel tempo in cui è possibile pianificare il recapito di uno o più annunci; uguale a disponibile e opportunità di selezione host.                                                                                                                     |
| Servizio decisionale di Active Directory | servizio esterno che decide quali annunci e durate verranno visualizzati all'utente. I servizi vengono in genere forniti da un partner e non rientrano nell'ambito di questo documento.                                                                    |
| Segnale                 | Indicazione del tempo e dei parametri della prossima pausa pubblicitaria. Si noti che i segnali possono indicare un'opzione in sospeso a un'interrotta pubblicitaria, un passaggio in sospeso all'annuncio successivo all'interno di un'interruzioni pubblicitaria e un passaggio in sospeso da un'interruzioni pubblicitaria al contenuto principale.           |
| Packager            | L'endpoint di streaming di servizi multimediali di Azure offre funzionalità di creazione dinamica dei pacchetti per DASH e HLS e viene definito "Packager" nel settore multimediale.                                                                              |
| Ora di presentazione   | L'ora in cui un evento viene presentato a un visualizzatore. L'ora rappresenta il momento nella scala cronologica dei supporti in cui un visualizzatore vedrà l'evento. Ad esempio, l'ora per la presentazione di un messaggio di comando SCTE-35 splice_info() è splice_time(). |
| Ora di arrivo        | L'ora di arrivo di un messaggio evento. L'ora è in genere diversa dall'ora di presentazione dell'evento, poiché i messaggi evento vengono inviati prima dell'ora di presentazione dell'evento.                                                    |
| Traccia di tipo sparse        | traccia multimediale che non è continua ed è sincronizzata temporalmente con una traccia padre o di controllo.                                                                                                                                                  |
| Origine              | Il servizio di streaming multimediale di Azure                                                                                                                                                                                                             |
| Sink di canale        | Il servizio di streaming live multimediale di Azure                                                                                                                                                                                                        |
| HLS                 | Protocollo Apple HTTP Live Streaming                                                                                                                                                                                                            |
| DASH                | Dynamic Adaptive Streaming Over HTTP                                                                                                                                                                                                          |
| Smooth              | Smooth Streaming Protocol                                                                                                                                                                                                                     |
| MPEG2-TS            | MPEG-2 Transport Stream                                                                                                                                                                                                                      |
| RTMP                | Real-Time Multimedia Protocol                                                                                                                                                                                                                 |
| uimsbf              | Intero senza segno, a partire dal numero di bit più significativo.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1,2 riferimenti normativi

I documenti seguenti contengono le disposizioni, che, per riferimento in questo testo, costituiscono le disposizioni di questo documento. Tutti i documenti sono soggetti a revisione da parte dei corpi degli standard e i lettori sono invitati a esaminare la possibilità di applicare le edizioni più recenti dei documenti elencati di seguito. Si ricorda inoltre che le edizioni più recenti dei documenti a cui si fa riferimento potrebbero non essere compatibili con questa versione della specifica dei metadati temporizzati per servizi multimediali di Azure.


| Standard          | Definizione                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-debutto] | [Specifica di segnalazione dell'inserimento di programmi digitali in prima serata 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [Guida di riferimento al linguaggio ActionScript FLASH](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| AMF0            | ["Formato del messaggio di azione AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | Guida all'interoperabilità di DASH Industry Forum v 4,2 [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Metadati temporizzati per HTTP Live Streaming- [https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Metadati temporizzati in Common Media Application Format (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| ID3v2           | Versione tag ID3 2.4.0  [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: parte 12 formato file supporti ISO base, FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Information Technology: flusso adattivo dinamico su HTTP (DASH), parte 1: Descrizione della presentazione multimediale e formati di segmento. 2014 maggio. Pubblicato. URL: https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Information Technology--formato di applicazione multimediale (MPEG-A)-parte 19: Common Media Application Format (CMAF) per i supporti segmentati. Gennaio 2018. Pubblicato. URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Information Technology--MPEG Systems Technologies--parte 7: crittografia comune nei file di formato file multimediali di base ISO. 2016 febbraio. Pubblicato. URL: https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 15 maggio 2014](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-inserimento]  | [Specifica di inserimento Live MP4 frammentato di servizi multimediali di Azure](./media-services-fmp4-live-ingest-overview.md)                                                      |
| [RFC8216]         | R. Pantos, ed.; W. può. HTTP Live Streaming. 2017 agosto. Si tratta di un messaggio informativo. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| RFC4648         | Codifica di dati base 16, Base32 e Base64- [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| RTMP            | ["Protocollo di messaggistica Real-Time di Adobe", 21 dicembre 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35:2019-messaggio di inserimento del programma digitale per il cavo- https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016-MPEG DASH per i servizi IP-Based Cable Part 1: estensioni e vincoli MPD                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH per i servizi di IP-Based Cable Part 3: DASH/FF profile                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1-pianificazione degli eventi e interfaccia di notifica                                                                                                                                                  |
| [SCTE-250]        | API di gestione di eventi e segnali                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. inserimento dei metadati programmato

Servizi multimediali di Azure supporta i metadati in banda in tempo reale per i protocolli [RTMP] e Smooth Streaming [MS-SSTR-ingeri]. I metadati in tempo reale possono essere usati per definire eventi personalizzati, con schemi personalizzati personalizzati (JSON, Binary, XML) e formati definiti dal settore come ID3, oppure SCTE-35 per la segnalazione di Active Directory in un flusso di broadcast. 

Questo articolo fornisce informazioni dettagliate su come inviare segnali di metadati temporizzati personalizzati usando i protocolli di inserimento supportati di servizi multimediali di Azure. Questo articolo illustra anche come i manifesti per HLS, DASH e Smooth Streaming vengono decorati con i segnali di metadati programmati, nonché come vengono trasportati in banda quando il contenuto viene recapitato tramite CMAF (frammenti MP4) o segmenti del flusso di trasporto (TS) per HLS. 

Gli scenari di casi d'uso comuni per i metadati temporizzati includono:

 - SCTE-35 segnala ad per attivare interruzioni pubblicitarie in un evento Live o broadcast lineare
 - Metadati ID3 personalizzati che possono attivare eventi in un'applicazione client (browser, iOS o Android)
 - Metadati JSON, binari o XML definiti personalizzati per attivare eventi in un'applicazione client
 - Telemetria da un codificatore Live, una fotocamera IP o un drone
 - Eventi da una fotocamera IP, ad esempio movimento, rilevamento viso e così via.
 - Informazioni sulla posizione geografica da una fotocamera per le azioni, un drone o un dispositivo in movimento
 - Testi di canzoni
 - Limiti del programma su un feed live lineare
 - Immagini o metadati aumentati da visualizzare in un feed live
 - Punteggi sportivi o informazioni sul clock del gioco
 - Pacchetti pubblicitari interattivi da visualizzare insieme al video nel browser
 - Quiz o sondaggi
  
Gli eventi live e i pacchetti di servizi multimediali di Azure sono in grado di ricevere questi segnali di metadati temporizzati e di convertirli in un flusso di metadati che possono raggiungere applicazioni client usando protocolli basati su standard come HLS e DASH.


## <a name="21-rtmp-timed-metadata"></a>2,1 metadati programmati RTMP

Il protocollo [RTMP] consente l'invio di segnali di metadati temporizzati per diversi scenari, tra cui i metadati personalizzati e i segnali ad SCTE-35. 

I segnali pubblicitari (messaggi cue) vengono inviati come messaggi CUE [AMF0] incorporati all'interno del flusso [RTMP]. I messaggi di cue possono essere inviati a un certo punto prima dell'evento effettivo o del segnale di splicing di Active Directory [SCTE35]. Per supportare questo scenario, il timestamp di presentazione effettivo dell'evento viene inviato all'interno del messaggio cue. Per altre informazioni, vedere [AMF0].

I seguenti comandi [AMF0] sono supportati da servizi multimediali di Azure per l'inserimento RTMP:

- **onUserDataEvent** -usato per i metadati personalizzati o per i metadati temporizzati [ID3v2]
- **onAdCue** : usato principalmente per segnalare un'opportunità di selezione host nel flusso live. Sono supportate due forme della cue, una modalità semplice e una modalità "SCTE-35". 
- **onCuePoint** : supportato da determinati codificatori hardware locali, ad esempio il codificatore Elemental Live, per segnalare i messaggi [SCTE35]. 
  

La tabella seguente descrive il formato del payload del messaggio AMF che verrà inserito da servizi multimediali per le modalità "Simple" e [SCTE35] Message.

Il nome del messaggio [AMF0] può essere utilizzato per distinguere più flussi di eventi dello stesso tipo.  Per i messaggi [SCTE-35] e la modalità "Simple", il nome del messaggio AMF deve essere "onAdCue" come richiesto nella specifica [Adobe-debutto].  Tutti i campi non elencati di seguito verranno ignorati da servizi multimediali di Azure al momento dell'inserimento.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1-RTMP con metadati personalizzati con "onUserDataEvent"

Se si vuole fornire feed di metadati personalizzati dal codificatore upstream, dalla fotocamera IP, dal drone o dal dispositivo usando il protocollo RTMP, usare il tipo di comando "onUserDataEvent" [AMF0] data Message.

Il comando **"onUserDataEvent"** del messaggio di dati deve contenere un payload del messaggio con la definizione seguente che deve essere acquisito da servizi multimediali e incluso nel formato di file in banda, nonché nei manifesti per HLS, DASH e Smooth Streaming.
Si consiglia di inviare messaggi di metadati temporizzati non più spesso di una volta ogni 0,5 secondi (500 ms) o di problemi di stabilità con il flusso Live può verificarsi. Ogni messaggio può aggregare i metadati da più frame se è necessario fornire i metadati a livello di frame. Se si inviano flussi a bitrate multipli, è consigliabile fornire anche i metadati in un solo bitrate solo per ridurre la larghezza di banda ed evitare interferenze con l'elaborazione video/audio. 

Il payload per **"onUserDataEvent"** deve essere un messaggio di formato XML [MPEGDASH] EventStream. In questo modo è facile passare schemi definiti personalizzati che possono essere trasportati in payload ' EMSG ' in banda per il contenuto CMAF [MPEGCMAF] recapitato tramite protocolli HLS o DASH. Ogni messaggio del flusso di eventi DASH contiene un schemeIdUri che funziona come identificatore dello schema dei messaggi URN e definisce il payload del messaggio. Alcuni schemi come " https://aomedia.org/emsg/ID3 " per [ID3v2] o **urn: SCTE: scte35:2013: bin** per [SCTE-35] sono standardizzati dai consorzi di settore per l'interoperabilità. Qualsiasi provider di applicazioni può definire un proprio schema personalizzato usando un URL che controlla (di proprietà del dominio) e può fornire una specifica in corrispondenza di tale URL, se scelto. Se un lettore dispone di un gestore per lo schema definito, questo è l'unico componente che deve comprendere il payload e il protocollo.

Lo schema per il payload XML [MPEG-DASH] EventStream è definito come (Estratto da DASH ISO-IEC-23009-1-3rd Edition). Si noti che al momento è supportato un solo "EventType" per "EventStream". Se vengono forniti più eventi in **EventStream**, viene elaborato solo il primo elemento **evento** .

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Esempio di flusso di eventi XML con ID dello schema ID3 e payload di dati con codifica Base64.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Esempio di flusso di eventi con ID dello schema personalizzato e dati binari con codifica Base64  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Esempio di flusso di eventi con ID schema personalizzato e JSON personalizzato  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>URI ID schema supportati predefiniti
| URI ID schema                 | Description                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https: \/ /aomedia.org/EMSG/ID3 | Viene descritto come i metadati [ID3v2] possono essere trasportati come metadati temporizzati in un MP4 frammentato compatibile con CMAF [MPEGCMAF]. Per ulteriori informazioni, vedere i [metadati temporizzati in Common Media Application Format (CMAF)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Elaborazione di eventi e segnalazione di manifesti

Alla ricezione di un evento **"onUserDataEvent"** valido, servizi multimediali di Azure cercherà un payload XML valido corrispondente a EventStreamType (definito in [MPEGDASH]), analizzerà il payload XML e lo convertirà in una casella ' EMSG ' della versione 1 del frammento MP4 [MPEGCMAF] per l'archiviazione nell'archivio live e la trasmissione a Media Services Packager.   Il Packager rileverà la casella ' EMSG ' nel flusso live e:

- (a) "creare pacchetti dinamici" in segmenti TS per il recapito ai client HLS in conformità con la specifica dei metadati temporizzati HLS [HLS-TMD] o
- (b) passarlo per la distribuzione nei frammenti CMAF tramite HLS o DASH oppure 
- (c) convertirlo in un segnale di traccia di tipo sparse per il recapito tramite Smooth Streaming [MS-SSTR].

Oltre al formato "EMSG" in formato CMAF o pacchetti di Servizi terminal per HLS, i manifesti per DASH (MPD) e Smooth Streaming conterranno un riferimento ai flussi di eventi in banda (noto anche come Track Stream di tipo sparse in Smooth Streaming).

Singoli eventi o i relativi payload di dati non vengono restituiti direttamente nei manifesti HLS, DASH o Smooth. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Vincoli informativi e predefiniti aggiuntivi per gli eventi onUserDataEvent

- Se la scala cronologica non è impostata nell'elemento EventStream, per impostazione predefinita viene usata la scala cronologica RTMP da 1 kHz
- Il recapito di un messaggio onUserDataEvent è limitato a una volta ogni 500 ms max. Se gli eventi vengono inviati con maggiore frequenza, può influisca sulla larghezza di banda e sulla stabilità del feed live

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 la segnalazione di un annuncio RTMP con "onAdCue"

Servizi multimediali di Azure può restare in ascolto e rispondere a diversi tipi di messaggi [AMF0] che possono essere usati per segnalare diversi metadati sincronizzati in tempo reale nel flusso live.  La specifica [Adobe-debutto] definisce due tipi di cue denominati "Simple" e "SCTE-35". Per la modalità "semplice", servizi multimediali supporta un singolo messaggio di cue AMF denominato "onAdCue" che usa un payload che corrisponde alla tabella riportata di seguito definita per il segnale "modalità semplice".  

La sezione seguente illustra il payload "modalità semplice" di RTMP, che può essere usato per segnalare un segnale ad "spliceOut" di base che verrà trasferito al manifesto client per HLS, DASH e Microsoft Smooth Streaming. Questa operazione è molto utile per gli scenari in cui il cliente non dispone di un sistema di distribuzione o inserimento di segnalazioni ad SCTE-35 complesso e usa un codificatore locale di base per inviare il messaggio cue tramite un'API. In genere, il codificatore locale supporterà un'API basata su REST per attivare questo segnale, che verrà anche "splicing-condition" del flusso video inserendo un frame IDR nel video e iniziando un nuovo GOP.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 Indicazione di segnale RTMP ad con "onAdCue"-modalità semplice

| Nome campo | Tipo di campo | Necessaria? | Descrizioni                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | string     | Obbligatoria  | Messaggio di evento.  Deve essere "SpliceOut" per designare una giunzione modalità semplice.                                                                                                                                                                                                         |
| id         | string     | Obbligatoria  | Un identificatore univoco che descrive la giunzione o il segmento. Identifica questa istanza del messaggio                                                                                                                                                                                       |
| duration   | Numero     | Obbligatoria  | La durata della giunzione. Le unità sono espresse in secondi frazionari.                                                                                                                                                                                                                           |
| elapsed    | Numero     | Facoltativo  | Se il segnale dell'annuncio viene ripetuto per supportare l'ottimizzazione, questo campo deve indicare il tempo di presentazione trascorso dall'inizio della giunzione. Le unità sono espresse in secondi frazionari. Quando si usa la modalità semplice, questo valore non deve superare la durata originale della giunzione. |
| time       | Numero     | Obbligatoria  | Deve essere l'ora della giunzione, nell'ora di presentazione. Le unità sono espresse in secondi frazionari.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Esempio di output del manifesto MPEG DASH quando si usa la modalità semplice di Adobe RTMP

Vedere l'esempio [3.3.2.1 MPEG Dash. mpd EventStream con la modalità semplice di Adobe](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Vedere il [manifesto di esempio di 3.3.3.1 Dash con singolo periodo e la modalità semplice di Adobe ](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Esempio di output del manifesto HLS quando si usa la modalità semplice di Adobe RTMP

Vedere l'esempio [3.2.2 manifesto HLS con Adobe Simple Mode e EXT-X-cue Tag](#322-apple-hls-with-adobe-primetime-ext-x-cue)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 la segnalazione di segnale ad RTMP con la modalità "onAdCue"-SCTE-35

Quando si usa un flusso di lavoro di produzione broadcast più avanzato che richiede il passaggio del messaggio di payload SCTE-35 completo al manifesto HLS o DASH, è preferibile usare la specifica "SCTE-35 Mode" della specifica [Adobe-debutto].  Questa modalità supporta i segnali SCTE-35 in banda inviati direttamente in un codificatore Live locale che codifica quindi i segnali nel flusso RTMP usando la modalità "SCTE-35" specificata nella specifica [Adobe-debutto]. 

In genere i messaggi SCTE-35 possono essere visualizzati solo in input del flusso di trasporto MPEG-2 in un codificatore locale. Rivolgersi al produttore del codificatore per informazioni dettagliate su come configurare un'inserimento del flusso di trasporto che contiene SCTE-35 e abilitarlo per il pass-through a RTMP in modalità Adobe SCTE-35.

In questo scenario, il payload seguente deve essere inviato dal codificatore locale usando il tipo di messaggio **"onAdCue"** [AMF0].

| Nome campo | Tipo di campo | Necessaria? | Descrizioni                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| cue        | string     | Obbligatoria  | Messaggio di evento.  Per i messaggi [SCTE-35], è necessario che sia il splice_info_section binario con codifica Base64 [RFC4648] () in modo che i messaggi vengano inviati ai client HLS, Smooth e Dash.                                                                                                                                                                                                                               |
| type       | string     | Obbligatoria  | Un URN o URL che identifica lo schema del messaggio. Per i messaggi [SCTE-35], **deve** essere **"scte35"** affinché i messaggi vengano inviati ai client HLS, Smooth e Dash in conformità con [Adobe-debutto]. Facoltativamente, l'URN "urn: SCTE: scte35:2013: bin" può essere usato anche per segnalare un messaggio [SCTE-35].                                                                                                        |
| id         | string     | Obbligatoria  | Un identificatore univoco che descrive la giunzione o il segmento. Identifica l'istanza del messaggio.  I messaggi semanticamente equivalenti devono avere lo stesso valore.                                                                                                                                                                                                                                                       |
| duration   | Numero     | Obbligatoria  | La durata dell'evento o del segmento di giunzione, se conosciuto. Se è sconosciuto, il valore **deve** essere 0.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Numero     | Facoltativo  | Se il segnale dell'annuncio [SCTE-35] viene ripetuto per ottimizzarlo, questo campo deve indicare il tempo di presentazione trascorso dall'inizio della giunzione. Le unità sono espresse in secondi frazionari. In modalità [SCTE-35], questo valore può superare la durata originale specificata della giunzione o del segmento.                                                                                                                   |
| time       | Numero     | Obbligatoria  | L'ora di presentazione dell'evento o della giunzione annuncio.  L'ora e la durata della presentazione **devono** essere allineate con i punti di accesso al flusso (SAP) di tipo 1 o 2, come definito in [ISO-14496-12] Annex I. Per l'uscita HLS, l'ora e la durata **devono** essere allineate con i limiti del segmento. L'ora di presentazione e la durata di diversi messaggi di eventi all'interno dello stesso flusso di eventi non devono (MUST NOT) sovrapporsi. Le unità sono espresse in secondi frazionari. |

---

<!---
#### Example MPEG DASH .mpd manifest with SCTE-35 mode
See [Section 3.3.3.2 example DASH manifest with SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)
--->

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Esempio di un manifesto HLS. M3U8 con segnale in modalità SCTE-35
Vedere [la sezione 3.2.1.1 esempio di manifesto HLS con SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35)



## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 per la segnalazione di ad RTMP con "onCuePoint" per Elemental Live

Il codificatore locale Elemental Live supporta i marcatori di Active Directory nel segnale RTMP. Servizi multimediali di Azure supporta attualmente solo il tipo di marcatore ad "onCuePoint" per RTMP.  Questa impostazione può essere abilitata nelle impostazioni del gruppo Adobe RTMP nell'API o nelle impostazioni del codificatore multimediale per gli elementi, impostando "**ad_markers**" su "onCuePoint".  Per informazioni dettagliate, vedere la documentazione di Elemental Live. L'abilitazione di questa funzionalità nel gruppo RTMP passerà i segnali SCTE-35 agli output di Adobe RTMP per l'elaborazione da parte di servizi multimediali di Azure.

Il tipo di messaggio "onCuePoint" è definito in [Adobe-Flash-AS] e ha la struttura di payload seguente quando viene inviato dall'output RTMP di Elemental Live.


| Proprietà   | Descrizione                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | Il nome deve essere '**scte35**' per Elemental Live.                                                                                                                                                                              |
| time       | Tempo in secondi in cui si è verificato il punto di attacco nel file video durante la sequenza temporale                                                                                                                                           |
| type       | Il tipo di punto cue deve essere impostato su "**Event**".                                                                                                                                                                             |
| parametri | Matrice associativa di stringhe di coppie nome/valore contenenti le informazioni del messaggio SCTE-35, inclusi ID e durata. Questi valori vengono analizzati da servizi multimediali di Azure e inclusi nel tag di decorazione del manifesto. |


Quando si usa questa modalità del marcatore di Active Directory, l'output del manifesto HLS è simile alla modalità "Simple" di Adobe.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Esempio MPEG DASH MPD, Single period, Adobe Simple Mode Signals

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
```

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Playlist HLS di esempio, segnali in modalità semplice di Adobe con tag EXT-X-CUE (troncato "..." per brevità)

L'esempio seguente illustra l'output di Media Services Dynamic Packager per un flusso di inserimento RTMP usando i segnali della modalità "Simple" di Adobe e il tag legacy [Adobe-debutto] EXT-X-CUE.  

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...
```

### <a name="216-cancellation-and-updates"></a>annullamento e aggiornamenti di 2.1.6

I messaggi possono essere annullati o aggiornati inviando più messaggi con ora di presentazione e ID uguali. L'ora di presentazione e l'ID identificano in modo univoco l'evento e l'ultimo messaggio ricevuto per un'ora di presentazione specifica che soddisfa i vincoli preroll è il messaggio su cui viene eseguita un'operazione. L'evento aggiornato sostituisce eventuali messaggi ricevuti in precedenza. Il vincolo di preroll è 4 secondi. Sui messaggi ricevuti almeno quattro secondi prima dell'ora di presentazione verranno eseguite le operazioni.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Inserimento MP4 frammentato (Smooth Streaming)

Per i requisiti relativi all'inserimento di flussi live, vedere [MS-SSTR-inserimenti]. Le sezioni seguenti descrivono i dettagli relativi all'inserimento dei metadati di presentazione programmati.  I metadati della presentazione temporizzata vengono inseriti come una traccia di tipo sparse, definita sia nella casella del manifesto del server Live (vedere MS-SSTR) che nella casella film (' Moov ').  

Ogni frammento di tipo sparse è costituito da una casella di frammento di film (' Moof ') e da supporti Data Box (' mdat '), in cui la casella ' mdat ' è il messaggio binario.

Per ottenere un inserimento accurato dei frame degli annunci, il codificatore deve suddividere il frammento in corrispondenza dell'ora di presentazione in cui è necessario inserire la cue.  È necessario creare un nuovo frammento che inizia con un frame IDR appena creato oppure con i punti di accesso di flusso (SAP) di tipo 1 o 2, come definito in [ISO-14496-12] Annex I.
<!--- This allows the Azure Media Packager to properly generate an HLS manifest and a DASH multi-period manifest where the new Period begins at the frame-accurate splice conditioned presentation time. --->

### <a name="221-live-server-manifest-box"></a>2.2.1 Casella Live Server Manifest Box

La traccia di tipo sparse **deve** essere dichiarata nella casella manifesto del server Live con una **\<textstream\>** voce e **deve** avere gli attributi seguenti impostati:

| **Nome attributo** | **Tipo di campo** | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Numero         | Obbligatoria      | **Deve** essere "0", che indica una traccia con velocità in bit sconosciuta e variabile.                                                                                                                                                          |
| parentTrackName    | string         | Obbligatoria      | **Deve** corrispondere al nome della traccia padre alla quale i codici temporali della traccia di tipo sparse sono allineati per la scala cronologica. La traccia padre non può essere una traccia di tipo sparse.                                                                             |
| manifestOutput     | Boolean        | Obbligatoria      | **Deve** essere "true" per indicare che la traccia di tipo sparse verrà incorporata nel manifesto del client Smooth.                                                                                                                        |
| Subtype            | string         | Obbligatoria      | **Deve** essere il codice di quattro caratteri "data".                                                                                                                                                                                  |
| Schema             | string         | Obbligatoria      | **Deve** essere un URN o un URL che identifica lo schema del messaggio. Per i messaggi [SCTE-35], **deve** essere "urn: SCTE: scte35:2013: bin" affinché i messaggi vengano inviati ai client HLS, Smooth e Dash in conformità con [SCTE-35]. |
| trackName          | string         | Obbligatoria      | **Deve** corrispondere al nome della traccia di tipo sparse. Trackname può essere usato per distinguere più flussi di eventi con lo stesso schema. Ogni flusso di eventi univoco **deve** avere un nome di traccia univoco.                                |
| timescale          | Numero         | Facoltativo      | **Deve** corrispondere alla scala cronologica della traccia padre.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Casella Movie Box

La casella Movie (' Moov ') segue la casella del manifesto del server Live come parte dell'intestazione del flusso per una traccia di tipo sparse.

La casella ' Moov ' **deve** contenere una casella **TrackHeaderBox (' tkhd ')** come definito in [ISO-14496-12] con i vincoli seguenti:

| **Nome campo** | **Tipo di campo**          | **Obbligatorio?** | **Descrizione**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | Intero senza segno a 64 bit | Obbligatoria      | **Deve** essere 0, poiché la casella Track include zero campioni e la durata totale degli esempi nella casella Track è 0. |

---

La casella ' Moov ' **deve** contenere un **HandlerBox (' HDLR ')** come definito in [ISO-14496-12] con i vincoli seguenti:

| **Nome campo** | **Tipo di campo**          | **Obbligatorio?** | **Descrizione**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | Intero senza segno a 32 bit | Obbligatoria      | **Deve** essere ' meta '. |

---

La casella ' STSD ' **deve** contenere una casella MetaDataSampleEntry con un nome di codifica come definito in [ISO-14496-12].  Per i messaggi SCTE-35, ad esempio, il nome del codice **deve** essere ' SCTE '.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Caselle Movie Fragment Box e Media Data Box

I frammenti di traccia di tipo sparse sono costituiti da una casella di frammento di film (' Moof ') e da un Data Box di supporto (' mdat ').

> [!NOTE]
> Per ottenere un inserimento accurato dei frame degli annunci, il codificatore deve suddividere il frammento in corrispondenza dell'ora di presentazione in cui è necessario inserire la cue.  È necessario creare un nuovo frammento che inizia con un frame IDR appena creato oppure con i punti di accesso di flusso (SAP) di tipo 1 o 2, come definito in [ISO-14496-12] Annex I
> 

La casella casella moviefragmentbox (' Moof ') **deve** contenere una casella **TrackFragmentExtendedHeaderBox (' UUID ')** come definito in [MS-SSTR] con i campi seguenti:

| **Nome campo**         | **Tipo di campo**          | **Obbligatorio?** | **Descrizione**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | Intero senza segno a 64 bit | Obbligatoria      | **Deve** essere l'ora di arrivo dell'evento. Questo valore consente di allineare il messaggio con la traccia padre.           |
| fragment_duration      | Intero senza segno a 64 bit | Obbligatoria      | **Deve** corrispondere alla durata dell'evento. La durata può essere zero per indicare che la durata è sconosciuta. |

---


La casella MediaDataBox (' mdat ') **deve** avere il formato seguente:

| **Nome campo**          | **Tipo di campo**                   | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | Intero senza segno a 32 bit (uimsbf) | Obbligatoria      | Determina il formato del contenuto della casella ' mdat '. Le versioni non riconosciute verranno ignorate. Al momento, l'unica versione supportata è 1.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | Intero senza segno a 32 bit (uimsbf) | Obbligatoria      | Identifica l'istanza del messaggio. I messaggi semanticamente equivalenti devono avere lo stesso valore, vale a dire che è sufficiente l'elaborazione di una qualsiasi casella di messaggio evento con lo stesso ID.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | Intero senza segno a 32 bit (uimsbf) | Obbligatoria      | La somma del fragment_absolute_time, specificata in TrackFragmentExtendedHeaderBox, e il presentation_time_delta **deve** essere l'ora di presentazione dell'evento. L'ora e la durata della presentazione **devono** essere allineate con i punti di accesso al flusso (SAP) di tipo 1 o 2, come definito in [ISO-14496-12] Annex I. Per l'uscita HLS, l'ora e la durata **devono** essere allineate con i limiti del segmento. L'ora di presentazione e la durata dei diversi messaggi di evento nello stesso flusso di eventi non **devono** sovrapporsi. |
| message                 | matrice di byte                       | Obbligatoria      | Messaggio di evento. Per i messaggi [SCTE-35], il messaggio è il splice_info_section binario (). Per i messaggi [SCTE-35], **deve** essere il splice_info_section () affinché i messaggi vengano inviati ai client HLS, Smooth e Dash in conformità con [SCTE-35]. Per i messaggi [SCTE-35], il splice_info_section binario () è il payload della casella ' mdat ' e **non** è con codifica Base64.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Annullamento e aggiornamenti

I messaggi possono essere annullati o aggiornati inviando più messaggi con ora di presentazione e ID uguali.  Il tempo di presentazione e l'ID identificano in modo univoco l'evento. L'ultimo messaggio ricevuto per un'ora di presentazione specifica che soddisfa i vincoli preroll è il messaggio su cui viene eseguita un'operazione. Il messaggio aggiornato sostituisce eventuali messaggi ricevuti in precedenza.  Il vincolo di preroll è 4 secondi. Sui messaggi ricevuti almeno quattro secondi prima dell'ora di presentazione verranno eseguite le operazioni. 


## <a name="3-timed-metadata-delivery"></a>3 Recapito di metadati programmati

I dati del flusso di eventi sono opachi per Servizi multimediali. Servizi multimediali si limita a passare tre tipi di informazioni tra l'endpoint di inserimento e l'endpoint client. Le proprietà seguenti vengono recapitate al client, in conformità con [SCTE-35] e/o il protocollo di streaming del client:

1.  Schema: un URN o URL che identifica lo schema del messaggio.
2.  Ora di presentazione: l'ora di presentazione dell'evento sulla scala cronologica dei file multimediali.
3.  Durata: la durata dell'evento.
4.  ID: un identificatore univoco facoltativo per l'evento.
5.  Messaggio: i dati dell'evento.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3,1 manifesto Smooth Streaming Microsoft  

Per informazioni dettagliate su come formattare una traccia dei messaggi sparse, vedere Gestione delle tracce di tipo sparse [MS-SSTR]. Per i messaggi [SCTE35], Smooth Streaming restituirà il splice_info_section con codifica Base64 () in un frammento di tipo sparse.
StreamIndex **deve** avere un sottotipo di "data" e CustomAttributes **deve** contenere un attributo con Name = "schema" e value = "urn: SCTE: scte35:2013: bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Esempio di manifesto client Smooth che mostra la codifica Base64 [SCTE35] splice_info_section ()
```xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="32-apple-hls-manifest-decoration"></a>3,2 decorazione del manifesto HLS Apple

Servizi multimediali di Azure supporta i tag del manifesto HLS seguenti per la segnalazione di informazioni su ad disp durante un evento Live o su richiesta. 

<!--- EXT-X-DATERANGE as defined in Apple HLS [RFC8216] --->
- EXT-X-CUE come definito in [Adobe-debutto]
<!--- this mode is considered "legacy".  Customers should adopt the EXT-X-DATERANGE tag when possible. --->

L'output dei dati in ogni tag varia in base alla modalità del segnale di inserimento utilizzata. Ad esempio, l'inserimento RTMP con la modalità semplice di Adobe non contiene il payload con codifica Base64 SCTE-35 completo.

<!---
## 3.2.1 Apple HLS with EXT-X-DATERANGE (recommended)

The Apple HTTP Live Streaming [RFC8216] specification allows for signaling of [SCTE-35] messages. The messages are inserted into the segment playlist in an EXT-X-DATERANGE tag per [RFC8216] section titled "Mapping SCTE-35 into EXT-X-DATERANGE".  The client application layer can parse the M3U playlist and process M3U tags, or receive the events through the Apple player framework.  

The **RECOMMENDED** approach in Azure Media Services (version 3 API) is to follow [RFC8216] and use the EXT-X_DATERANGE tag for [SCTE35] ad avail decoration in the manifest.
--->


## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35"></a>3.2.1.1 esempio HLS manifesto. M3U8 che mostra la segnalazione EXT-X-CUE di SCTE-35

Nell'esempio seguente l'output del manifesto HLS di Media Services Dynamic Packager Mostra un tag EXT-X-CUE per [Adobe-debutto] in modalità SCTE35. 

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

```


<!---
THIS VERSION HAS THE HLSv8 DATERANGE Tags in it
~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~

--->

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue"></a>3.2.2 Apple HLS con Adobe debutto EXT-X-CUE

Servizi multimediali (API versione 2 e 3) supporta l'output del tag EXT-X-CUE, come definito in [Adobe-debutto] "SCTE-35 Mode". In questa modalità, servizi multimediali di Azure incorpora il codice Base64 [SCTE-35] splice_info_section () nel tag EXT-X-CUE.  

Il tag EXT-X-CUE "Legacy" è definito come indicato di seguito e può anche essere normativo a cui viene fatto riferimento nella specifica [Adobe-debutto]. Questo deve essere usato solo per la segnalazione SCTE35 legacy, laddove necessario; in caso contrario, il tag consigliato viene definito in [RFC8216] come EXT-X-DATERANGE. 

| **Nome attributo** | **Tipo**                      | **Obbligatorio?**                             | **Descrizione**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CUE                | stringa tra virgolette                 | Obbligatoria                                  | Messaggio codificato come stringa con codifica Base64 come descritto in [RFC4648]. Per i messaggi [SCTE-35], si tratta del splice_info_section con codifica Base64 ().                                                                                                                                      |
| TYPE               | stringa tra virgolette                 | Obbligatoria                                  | Un URN o URL che identifica lo schema del messaggio. Per i messaggi [SCTE-35], il tipo accetta il valore speciale "scte35".                                                                                                                                                                          |
| ID                 | stringa tra virgolette                 | Obbligatoria                                  | Un identificatore univoco dell'evento. Se al momento dell'inserimento del messaggio non viene specificato l'ID, Servizi multimediali di Azure genererà un ID univoco.                                                                                                                                              |
| DURATION           | numero a virgola mobile decimale | Obbligatoria                                  | Durata dell'evento. Se è sconosciuto, il valore **deve** essere 0. Le unità sono espresse in secondi frazionari.                                                                                                                                                                                           |
| ELAPSED            | numero a virgola mobile decimale | Facoltativo, ma necessari per la finestra temporale scorrevole | Quando il segnale viene ripetuto per supportare una finestra di presentazione scorrevole, questo campo **deve** corrispondere alla quantità di tempo di presentazione trascorso dall'inizio dell'evento. Le unità sono espresse in secondi frazionari. Questo valore può superare la durata originale specificata della giunzione o del segmento. |
| TIME               | numero a virgola mobile decimale | Obbligatoria                                  | L'ora di presentazione dell'evento. Le unità sono espresse in secondi frazionari.                                                                                                                                                                                                                        |

Il livello dell'applicazione del lettore HLS userà l'attributo TYPE per identificare il formato del messaggio, decodificare il messaggio, applicare le conversioni temporali necessarie ed elaborare l'evento.  Gli eventi sono sincronizzati temporalmente nella playlist del segmento della traccia padre, in base al timestamp dell'evento.  Essi vengono inserite prima del segmento più vicino (tag #EXTINF).


### <a name="323-hls-m3u8-manifest-example-using-adobe-primetime-ext-x-cue"></a>3.2.3 esempio di manifesto HLS. M3U8 con Adobe debutto EXT-X-CUE

L'esempio seguente illustra la decorazione del manifesto HLS usando il tag di Adobe serata EXT-X-CUE.  Il parametro "CUE" contiene solo le proprietà TYPE e Duration, che indica che si tratta di un'origine RTMP che usa la segnalazione in modalità "Simple" di Adobe.  
<!---If this was a SCTE-35 mode signal, the tag would include the base64 encoded binary SCTE-35 payload as seen in the [3.2.1.1 example](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).
--->


```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

```

### <a name="324-hls-message-handling-for-adobe-primetime-ext-x-cue"></a>3.2.4 gestione dei messaggi HLS per Adobe debutto EXT-X-CUE

Gli eventi vengono segnalati nella playlist del segmento di ogni traccia video e audio. La posizione del tag EXT-X-CUE **deve** sempre trovarsi immediatamente prima del primo segmento HLS (per l'imposizione o l'avvio del segmento) o subito dopo l'ultimo segmento HLS (per la giunzione nell'estremità o segmento) a cui si riferiscono gli attributi relativi a tempo e durata, come richiesto da [Adobe-debutto].

Quando è abilitata una finestra di presentazione scorrevole, il tag EXT-X-CUE **deve** essere ripetuto abbastanza spesso che la giunzione o il segmento è sempre completamente descritto nella playlist del segmento e l'attributo trascorso **deve** essere usato per indicare l'intervallo di tempo in cui la giunzione o il segmento è stato attivo, come richiesto da [Adobe-debutto].

Se una finestra temporale scorrevole di presentazione è abilitata, i tag EXT-X-CUE vengono rimossi dalla playlist del segmento quando il tempo file multimediali a cui fanno riferimento non rientra nella finestra temporale scorrevole di presentazione.

## <a name="33-dash-manifest-decoration-mpd"></a>Decorazione del manifesto DASH 3,3 (MPD)

[MPEGDASH] fornisce tre modi per segnalare gli eventi:

1.  Eventi segnalati nel EventStream MPD
2.  Eventi segnalati in banda tramite la finestra di messaggio evento (' EMSG ')
3.  Una combinazione di 1 e 2

Gli eventi segnalati nei EventStream MPD sono utili per lo streaming VOD, perché i client hanno accesso a tutti gli eventi immediatamente dopo il download di MPD. È utile anche per la segnalazione SSAI, in cui il fornitore downstream SSAI deve analizzare i segnali dal manifesto MPD e inserire il contenuto di Active Directory in modo dinamico.  La soluzione in banda (' EMSG ') è utile per lo streaming live in cui i client non devono scaricare nuovamente MPD oppure non si verifica alcuna manipolazione del manifesto SSAI tra il client e l'origine. 

Il comportamento predefinito di servizi multimediali di Azure per DASH consiste nel segnalare sia nei EventStream MPD che in banda usando la finestra di messaggio di evento (' EMSG ').

I messaggi cue inseriti su [RTMP] o [MS-SSTR-ingeri] vengono mappati in eventi DASH, usando le caselle ' EMSG ' in banda e/o in-MPD EventStreams. 

La segnalazione SCTE-35 in banda per DASH segue la definizione e i requisiti definiti in [SCTE-214-3] e anche nella sezione [DASH-IF-IOP] 13.12.2 (' eventi SCTE35'). 

Per il trasporto in banda [SCTE-35], nella finestra di messaggio dell'evento (' EMSG ') viene usato schemeId = "urn: SCTE: scte35:2013: bin". Per la decorazione del manifesto MPD, EventStream schemeId USA "urn: SCTE: scte35:2014: XML + bin".  Questo formato è una rappresentazione XML dell'evento che include un output binario con codifica Base64 del messaggio SCTE-35 completo che è arrivato al momento dell'inserimento. 

Le definizioni dei riferimenti normativi del trasporto dei messaggi CUE [SCTE-35] in DASH sono disponibili in [SCTE-214-1] sec 6.7.4 (MPD) e [SCTE-214-3] sec 7.3.2 (trasporto di messaggi cue SCTE 35).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 (EventStream)-segnalazione di MPEG DASH (MPD)

L'effetto del manifesto (MPD) degli eventi verrà segnalato in MPD usando l'elemento EventStream, che viene visualizzato all'interno dell'elemento period. Il schemeId usato è "urn: SCTE: scte35:2014: XML + bin".


> [!NOTE]
> Per motivi di brevità [SCTE-35] consente l'uso della sezione con codifica Base64 nell'elemento Signal. Binary (anziché nell'elemento Signal. SpliceInfoSection) come alternativa al ritorno a capo di un messaggio cue completamente analizzato.
> Servizi multimediali di Azure usa questo approccio "XML + bin" per la segnalazione nel manifesto MPD.
> Questo è anche il metodo consigliato usato in [DASH-IF-IOP]: vedere la sezione intitolata ["flussi di eventi di inserimento ad" del Dash se la linea guida di IOP](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 


L'elemento EventStream ha gli attributi seguenti:

| **Nome attributo** | **Tipo**                | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | string                  | Obbligatorio      | Identifica lo schema del messaggio. Lo schema è impostato sul valore dell'attributo Schema nella casella Live Server Manifest. Il valore **deve** essere un URN o un URL che identifica lo schema del messaggio. Il schemeId di output supportato dovrebbe essere "urn: SCTE: scte35:2014: XML + bin" per [SCTE-214-1] sec 6.7.4 (MPD), perché il servizio supporta solo "XML + bin" per brevità in MPD. |
| Valore              | string                  | Facoltativo      | Un valore di stringa aggiuntivo usato dai proprietari dello schema per personalizzare la semantica del messaggio. Per distinguere più flussi di eventi con lo stesso schema, il valore **deve** essere impostato sul nome del flusso di eventi (Trackname per [MS-SSTR-inserimento] o sul nome del messaggio AMF per l'inserimento di [RTMP]).                                                                         |
| Scala cronologica          | Intero senza segno a 32 bit | Obbligatoria      | La scala cronologica, espressa in cicli al secondo.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 flussi di eventi di esempio per MPEG DASH

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>esempio di 3.3.2.1 MPEG DASH. mpd segnalazione di flussi RTMP con Adobe Simple Mode

Nell'esempio seguente viene illustrato un estratto EventStream da Media Services Dynamic Packager per un flusso RTMP con la segnalazione in modalità "Simple" di Adobe.

```xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
```


#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>esempio di 3.3.2.2 MPEG DASH. mpd segnalazione di un flusso RTMP con la modalità Adobe SCTE-35

Nell'esempio seguente viene illustrato un estratto EventStream da Media Services Dynamic Packager per un flusso RTMP con la segnalazione in modalità Adobe SCTE-35.

Esempio di elemento EventStream con la segnalazione dello stile XML + bin per [SCTE-214-1]

```xml

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
```



> [!IMPORTANT]
> Si noti che presentationTime è l'ora di presentazione dell'evento [SCTE-35] convertito in rispetto all'ora di inizio del periodo, non all'ora di arrivo del messaggio.
> [MPEGDASH] definisce Event@presentationTime come "specifica l'ora di presentazione dell'evento relativo all'inizio del periodo.
> Il valore dell'ora di presentazione in secondi è la divisione del valore di questo attributo e il valore dell' EventStream@timescale attributo.
> Se non è presente, il valore dell'ora di presentazione è 0.


#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 esempio di formato MPEG DASH manifest (MPD) con un solo punto, EventStream, con i segnali in modalità semplice di Adobe

L'esempio seguente illustra l'output di Media Services Dynamic Packager per un flusso RTMP di origine usando il metodo di segnalazione ad di Adobe "Simple" mode. L'output è un singolo manifesto del periodo che mostra un EventStream usando l'URI schemeId impostato su "urn: com: Adobe: dpi: Simple: 2015" e la proprietà Value è impostata su "simplesignal".
Ogni segnale semplice viene fornito in un elemento Event con le @presentationTime @duration proprietà, e @id popolate in base ai segnali semplici in arrivo.

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

```

<!---
#### 3.3.3.2 Example MPEG DASH manifest (MPD) with multi-period, EventStream, using Adobe SCTE35 mode signaling

The following example shows the output from the Media Services dynamic packager for a source RTMP stream using the Adobe SCTE35 mode signaling.
In this case, the output manifest is a multi-period DASH .mpd with an EventStream element, and @schemeIdUri property set to "urn:scte:scte35:2014:xml+bin" and a @value property set to "scte35". Each Event element in the EventStream contains the full base64 encoded binary SCTE35 signal 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

--->

### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>Segnalazione della finestra di messaggio dell'evento 3.3.4 MPEG DASH in banda

Un flusso di eventi in banda richiede che MPD abbia un elemento InbandEventStream a livello del set di adattamento.  Questo elemento ha un attributo schemeIdUri obbligatorio e un attributo di scala cronologica facoltativo, che vengono visualizzati anche nella finestra di messaggio di evento (' EMSG ').  Non **devono** essere presenti finestre di messaggio di evento con identificatori di schema che non sono definiti in mpd.

Per il trasporto in banda [SCTE-35], i segnali **devono** usare schemeId = "urn: SCTE: scte35:2013: bin".
Le definizioni normative del trasporto dei messaggi [SCTE-35] in banda sono definite in [SCTE-214-3] sec 7.3.2 (trasporto di messaggi cue SCTE 35).

I dettagli seguenti descrivono i valori specifici che il client deve aspettarsi in ' EMSG ' in conformità con [SCTE-214-3]:

| **Nome campo**          | **Tipo di campo**          | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | string                  | Obbligatorio      | Identifica lo schema del messaggio. Lo schema è impostato sul valore dell'attributo Schema nella casella Live Server Manifest. Il valore **deve** essere un URN che identifica lo schema del messaggio. Per i messaggi [SCTE-35], **deve** essere "urn: SCTE: scte35:2013: bin" in conformità con [SCTE-214-3]          |
| valore                   | string                  | Obbligatorio      | Un valore di stringa aggiuntivo usato dai proprietari dello schema per personalizzare la semantica del messaggio. Per poter distinguere più flussi di eventi con lo stesso schema, il valore verrà impostato sul nome del flusso di eventi (trackName per l'inserimento Smooth o nome del messaggio AMF per l' inserimento RTMP). |
| Scala cronologica               | Intero senza segno a 32 bit | Obbligatoria      | La scala cronologica, espressa in cicli al secondo, dei campi Times e Duration all'interno della casella "EMSG".                                                                                                                                                                                                            |
| Presentation_time_delta | Intero senza segno a 32 bit | Obbligatoria      | Delta del tempo della presentazione multimediale dell'evento è l'ora di presentazione meno recente in questo segmento. L'ora e la durata della presentazione **devono** essere allineate con i punti di accesso al flusso (SAP) di tipo 1 o 2, come definito in [ISO-14496-12] Annex I.                                  |
| event_duration          | Intero senza segno a 32 bit | Obbligatoria      | La durata dell'evento o 0xFFFFFFFF per indicare una durata sconosciuta.                                                                                                                                                                                                                              |
| ID                      | Intero senza segno a 32 bit | Obbligatoria      | Identifica l'istanza del messaggio. I messaggi semanticamente equivalenti devono avere lo stesso valore. Se al momento dell'inserimento del messaggio non viene specificato l'ID, Servizi multimediali di Azure genererà un ID univoco.                                                                                        |
| Message_data            | matrice di byte              | Obbligatoria      | Messaggio di evento. Per i messaggi [SCTE-35], i dati del messaggio sono i splice_info_section binari () in conformità con [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Esempio di entità InBandEvenStream per la modalità semplice di Adobe
```xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
```

### <a name="335-dash-message-handling"></a>Gestione dei messaggi 3.3.5 DASH

Gli eventi vengono segnalati in banda, all'interno della casella "EMSG", per le tracce video e audio.  La segnalazione si verifica per tutte le richieste di segmenti per cui presentation_time_delta è 15 secondi o inferiore. 

Se una finestra temporale scorrevole di presentazione è abilitata, i messaggi eventi vengono rimossi da MPD quando la somma di ora e durata del messaggio evento è inferiore alla durata dei dati dei file multimediali nel manifesto.  In altre parole, i messaggi eventi vengono rimossi dal manifesto quando il tempo file multimediali a cui fanno riferimento non rientra nella finestra temporale scorrevole di presentazione.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 informazioni aggiuntive sull'implementazione per i fornitori del codificatore

Le linee guida seguenti sono problemi comuni che possono influisca sull'implementazione di questa specifica da parte del fornitore del codificatore.  Le linee guida riportate di seguito sono state raccolte in base ai commenti e suggerimenti dei partner reali per semplificare l'implementazione di questa specifica per altri utenti. 

[SCTE-35] i messaggi vengono inseriti in formato binario usando lo schema **"urn: SCTE: scte35:2013: bin"** per [MS-SSTR-ingerite] e il tipo **"scte35"** per l'inserimento di [RTMP]. Per semplificare la conversione dell'intervallo [SCTE-35], che si basa sui timestamp ora di presentazione (PTS) del flusso di trasporto MPEG-2, il mapping tra PTS (pts_time + pts_adjustment of the splice_time()) e la scala cronologica dei file multimediali viene fornito dall'ora di presentazione dell'evento (il campo fragment_absolute_time per l'inserimento Smooth e il campo ora per l'inserimento RTMP). Il mapping è necessario perché il valore PTS a 33 bit viene aggiornato ogni 26,5 ore circa.

Per Smooth Streaming inserimento [MS-SSTR-inserimenti] è **necessario** che il data box multimediale (' mdat ') contenga il **splice_info_section ()** definito in [SCTE-35]. 

Per l'inserimento RTMP, l'attributo CUE del messaggio AMF viene impostato sul splice_info_section con codifica Base64 **()** definito in [SCTE-35].  


Quando i messaggi hanno il formato descritto in precedenza, vengono inviati ai client HLS, Smooth e DASH come definito sopra.  

Quando si esegue il test dell'implementazione con la piattaforma servizi multimediali di Azure, iniziare a eseguire il test con un Live "pass-through" prima di passare a test in un live di codifica.

---

## <a name="change-history"></a>Cronologia delle modifiche

| Data     | Modifiche                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Il supporto per l'inserimento RTMP è stato modificato, è stato aggiunto RTMP "onCuePoint" per Elemental Live                                            |
| 08/22/19 | Aggiornamento per l'aggiunta di OnUserDataEvent a RTMP per i metadati personalizzati                                                          |
| 1/08/20  | Correzione dell'errore in modalità RTMP semplice e RTMP SCTE35. Modificato da "onCuePoint" a "onAdCue". Tabella in modalità semplice aggiornata. |
| 08/4/20  | Rimosso il supporto per il tag DATERANGE in modo che corrisponda all'implementazione nel servizio di produzione.    |

## <a name="next-steps"></a>Passaggi successivi
Visualizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
