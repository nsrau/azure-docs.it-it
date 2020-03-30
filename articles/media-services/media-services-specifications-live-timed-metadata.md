---
title: Azure Media Services - Signaling Timed Metadata in Live Streaming
description: Questa specifica descrive i metodi per segnalare i metadati temporiti durante l'inserimento e lo streaming in Servizi multimediali di Azure.This specification outlines methods for signaling timed metadata when ingesting and streaming to Azure Media Services. Ciò include il supporto per i segnali di metadati tempornoti generici (ID3), nonché la segnalazione SCTE-35 per l'inserimento di annunci e la segnalazione della condizione di giunzione.
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
ms.openlocfilehash: 551fb0cb9f3745a62d5d84f2c4878bbbbe5ad9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137323"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Segnalazione dei metadati programmati in streaming live 

Ultimo aggiornamento: 2019-08-22

### <a name="conformance-notation"></a>Nota di conformità

Le parole chiave "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY" e "OPTIONAL" in questo documento vanno interpretate come descritto nella specifica RFC 2119

## <a name="1-introduction"></a>1. Introduzione 

Al fine di segnalare l'inserimento di annunci pubblicitari o eventi di metadati personalizzati su un lettore client, le emittenti spesso fanno uso di metadati temporalcuni incorporati nel video. Per abilitare questi scenari, Servizi multimediali fornisce il supporto per il trasporto di metadati temporpensati dal punto di inserimento del canale di streaming live all'applicazione client.
Questa specifica descrive diverse modalità supportate da Servizi multimediali per i metadati temporizzazione all'interno dei segnali di streaming live.

1. [SCTE-35] segnalazione conforme agli standard delineati da [SCTE-35], [SCTE-214-1], [SCTE-214-3] e [RFC8216]

2. Segnalazione [SCTE-35] conforme alla specifica legacy [Adobe-Primetime] per la segnalazione degli annunci RTMP.
   
3. Una modalità di segnalazione dei metadati temporizzazione generica, per i messaggi che **NON** [SCTE-35] e che possono contenere [ID3v2] o altri schemi personalizzati definiti dallo sviluppatore dell'applicazione.

## <a name="11-terms-used"></a>1.1 Termini utilizzati

| Termine                | Definizione                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Interruzione annuncio            | Una posizione o un punto nel tempo in cui uno o più annunci possono essere pianificati per la pubblicazione; come l'opportunità di avvalenza e di collocamento.                                                                                                                     |
| Servizio di decisione annuncio | servizio esterno che decide quali annunci e durate verranno mostrati all'utente. I servizi vengono in genere forniti da un partner e non sono nell'ambito di questo documento.                                                                    |
| segnale                 | Indicazione del tempo e dei parametri dell'interruzione pubblicitaria imminente. Tieni presente che i segnali possono indicare un passaggio in sospeso a un'interruzione dell'annuncio, in attesa di passare all'annuncio successivo all'interno di un'interruzione pubblicitaria e passare da un'interruzione pubblicitaria al contenuto principale.           |
| Packager            | "Streaming Endpoint" di Servizi multimediali di Azure offre funzionalità di creazione di pacchetti dinamici per DASH e HLS e viene definito "Packager" nel settore multimediale.                                                                              |
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

## <a name="12-normative-references"></a>1.2 Riferimenti Normativi

I seguenti documenti contengono disposizioni che, per riferimento nel presente testo, costituiscono disposizioni del presente documento. Tutti i documenti sono soggetti a revisione da parte degli organismi di normalizzazione e i lettori sono invitati a indagare sulla possibilità di applicare le edizioni più recenti dei documenti elencati di seguito. Ai lettori viene inoltre ricordato che le edizioni più recenti dei documenti a cui si fa riferimento potrebbero non essere compatibili con questa versione della specifica dei metadati temporizzati per Servizi multimediali di Azure.Readers are also reminded that newer editions of the referenced documents might not be compatible with this version of the timed metadata specification for Azure Media Services.


| Standard          | Definizione                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Primetime Digital Program Insertion Signaling Specifica 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [Guida di riferimento al linguaggio ACTIONScript FLASH](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["Formato messaggio di azione AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | Linee guida per l'interoperabilità del forum di SETTORE DASH v 4.2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Metadati tempordati per lo streaming live HTTP -[https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Metadati temporpati nel formato CMAF (Common Media Application Format)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 Tag versione 2.4.0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: Parte 12 Formato di file multimediale di base ISO, FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Tecnologia dell'informazione -- Streaming adattivo dinamico su HTTP (DASH) - Parte 1: Descrizione della presentazione multimediale e formati di segmento. maggio 2014. Pubblicato. URL: https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Tecnologia delle informazioni -- Formato di applicazione multimediale (MPEG-A) -- Parte 19: Common media application format (CMAF) per supporti segmentati. gennaio 2018. Pubblicato. URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Tecnologia delle informazioni -- tecnologie di sistemi MPEG -- Parte 7: Crittografia comune nei file di formato dei file multimediali di base ISO. febbraio 2016. Pubblicato. URL: https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 15 maggio 2014](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-Ingest]  | [Specifica di inserimento dinamico MP4 di Servizi multimediali di AzureAzure Media Services Fragmented MP4 Live Inest Specification](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)                                                      |
| [RFC8216]         | R. Pantos, Ed.; W. Maggio. HTTP Live Streaming. nell'agosto 2017. Si tratta di un messaggio informativo. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | Codifiche dei dati Base16, Base32 e Base64[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| [RTMP]            | ["Protocollo di messaggistica in tempo reale di Adobe", 21 dicembre 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35: 2019 - Messaggio Digital Program Insertion Cueing per Cavo -https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH per servizi via cavo basati su IP Parte 1: Vincoli ed estensioni MPD                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH per servizi via cavo basati su IP Parte 3: Profilo DASH/FF                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – Interfaccia di pianificazione e notifica degli eventi                                                                                                                                                  |
| [SCTE-250]        | API di gestione di eventi e segnali (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. Inserimento metadati tempor>

Servizi multimediali di Azure supporta i metadati in tempo reale per entrambi i protocolli [RTMP] e Smooth Streaming [MS-SSTR-Ingest]. I metadati in tempo reale possono essere usati per definire eventi personalizzati, con schemi personalizzati personalizzati personalizzati (JSON, binario, XML), nonché formati definiti dal settore come ID3 o SCTE-35 per la segnalazione degli annunci in un flusso di trasmissione. 

Questo articolo fornisce i dettagli su come inviare segnali di metadati temporpensati personalizzati usando i protocolli di inserimento supportati di Servizi multimediali di Azure.This article provides the details for how to send custom timed metadata signals using the supported ingest protocols of Azure Media Services. L'articolo spiega anche come i manifesti per HLS, DASH e Smooth Streaming sono decorati con i segnali di metadati temporizzati, nonché come viene trasportato in banda quando il contenuto viene distribuito utilizzando segmenti CMAF (frammenti MP4) o Transport Stream (TS) per HLS. 

Gli scenari comuni dei casi d'uso per i metadati a tempo includono:Common use case scenarios for timed metadata include:

 - Segnali pubblicitari SCTE-35 per attivare le interruzioni pubblicitarie in un evento live o in una trasmissione lineare
 - Metadati ID3 personalizzati che possono attivare eventi in un'applicazione client (browser, iOS o Android)Custom ID3 metadata that can trigger events at a client application (browser, iOS, or Android)
 - Metadati JSON, binari o XML definiti personalizzati per attivare eventi in un'applicazione clientCustom defined JSON, Binary, or XML metadata to trigger events at a client application
 - Telemetria da un codificatore live, telecamera IP o drone
 - Eventi da una telecamera IP come Motion, face detection, ecc.
 - Informazioni sulla posizione geografica da una action camera, un drone o un dispositivo in movimento
 - Testi delle canzoni
 - Confini del programma su un feed live lineare
 - Immagini o metadati aumentati da visualizzare in un feed live
 - Punteggi sportivi o informazioni sull'orologio
 - Pacchetti pubblicitari interattivi da visualizzare insieme al video nel browser
 - Quiz o sondaggi
  
Gli eventi live di Servizi multimediali di Azure e Packager sono in grado di ricevere questi segnali di metadati temporerai e convertirli in un flusso di metadati in grado di raggiungere le applicazioni client usando protocolli basati su standard come HLS e DASH.


## <a name="21-rtmp-timed-metadata"></a>2.1 Metadati tempordati RTMP

Il protocollo [RTMP] consente l'invio di segnali di metadati temporpensati per vari scenari, inclusi i metadati personalizzati e i segnali di annuncio SCTE-35. 

I segnali pubblicitari (messaggi cue) vengono inviati come messaggi [AMF0] incorporati nel flusso [RTMP]. I messaggi cue possono essere inviati qualche tempo prima che l'evento effettivo o il segnale di giunzione annuncio [SCTE35] debba verificarsi. Per supportare questo scenario, il timestamp di presentazione effettivo dell'evento viene inviato all'interno del messaggio cue. Per altre informazioni, vedere [AMF0].

I comandi [AMF0] seguenti sono supportati dall'inserimento di Servizi multimediali di Azure per RTMP:

- **onUserDataEvent** - utilizzato per metadati personalizzati o metadati temporgiati [ID3v2]
- **onAdCue** - utilizzato principalmente per segnalare un'opportunità di posizionamento pubblicitario nel live streaming. Sono supportate due forme di segnale, una modalità semplice e una modalità "SCTE-35". 
- **onCuePoint** - supportato da alcuni codificatori hardware locali, come il codificatore Elemental Live, per segnalare i messaggi [SCTE35]. 
  

Nella tabella seguente viene descritto il formato del payload del messaggio AMF che Servizi multimediali invierà per entrambe le modalità messaggio "semplice" e [SCTE35].

Il nome del messaggio [AMF0] può essere utilizzato per differenziare più flussi di eventi dello stesso tipo.  Per entrambi i messaggi [SCTE-35] e la modalità "semplice", il nome del messaggio AMF DEVE essere "onAdCue" come richiesto nella specifica [Adobe-Primetime].  Tutti i campi non elencati di seguito devono essere ignorati da Servizi multimediali di Azure all'inserimento.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP con metadati personalizzati tramite "onUserDataEvent"

Se si desidera fornire feed di metadati personalizzati dal codificatore upstream, dalla fotocamera IP, dal drone o dal dispositivo utilizzando il protocollo RTMP, utilizzare il tipo di comando del messaggio di dati "onUserDataEvent" [AMF0].

Il comando del messaggio di dati **"onUserDataEvent"** deve contenere un payload del messaggio con la seguente definizione per essere acquisita da Servizi multimediali e inserita nel formato di file in banda, nonché i manifesti per HLS, DASH e Smooth Streaming.
Si consiglia di inviare messaggi di metadati temporizzati non più frequenti di una volta ogni 0,5 secondi (500 ms) o possono verificarsi problemi di stabilità con il flusso live. Ogni messaggio può aggregare i metadati da più frame se è necessario fornire metadati a livello di frame. Se si inviano flussi a bitrate multiplo, è consigliabile fornire anche i metadati su un singolo bitrate solo per ridurre la larghezza di banda ed evitare interferenze con l'elaborazione video/audio. 

Il payload per **"onUserDataEvent"** deve essere un messaggio in formato XML EventStream [MPEGDASH]. In questo modo è più semplice passare schemi definiti personalizzati che possono essere trasportati in payload 'emsg' in banda per il contenuto CMAF [MPEGCMAF] che viene distribuito tramite i protocolli HLS o DASH. Ogni messaggio dash Event Stream contiene un schemeIdUri che funziona come un identificatore dello schema di messaggio URN e definisce il payload del messaggio. Alcuni schemi comehttps://aomedia.org/emsg/ID3" " per [ID3v2], o **urn:scte:scte35:2013:bin** per [SCTE-35] sono standardizzati dai consorzi del settore per l'interoperabilità. Qualsiasi provider di applicazioni può definire il proprio schema personalizzato utilizzando un URL che controlla (dominio di proprietà) e può fornire una specifica a tale URL, se lo desidera. Se un giocatore ha un gestore per lo schema definito, allora questo è l'unico componente che deve comprendere il payload e il protocollo.

Lo schema per il payload XML EventStream di [MPEG-DASH] è definito come (estratto da DASH ISO-IEC-23009-1-3rd Edition). Si noti che in questo momento è supportato un solo "EventType" per "EventStream". Solo il primo elemento **Event** verrà elaborato se in **EventStream**vengono forniti più eventi .

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


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Esempio di flusso di eventi XML con ID schema ID3 e payload di dati con codifica base64.Example XML Event Stream with ID ID schema ID3 and base64-encoded data payload.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Flusso di eventi di esempio con ID schema personalizzato e dati binari con codifica base64Example Event Stream with custom schema ID and base64-encoded binary data  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Flusso di eventi di esempio con ID schema personalizzato e JSON personalizzatoExample Event Stream with custom schema ID and custom JSON  
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

### <a name="built-in-supported-scheme-id-uris"></a>URI ID schema supportati incorporati
| URI ID schema                 | Descrizione                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https:\//aomedia.org/emsg/ID3 | Viene descritto come i metadati [ID3v2] possono essere trasportati come metadati temporizzati in un MP4 frammentato [MPEGCMAF] compatibile con CMAF. Per altre informazioni, vedere [metadati temporpati nel formato CMAF (Common Media Application Format)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Elaborazione degli eventi e segnalazione dei manifesti

Alla ricezione di un evento **"onUserDataEvent"** valido, Servizi multimediali di Azure cercherà un payload XML valido corrispondente a EventStreamType (definito in [MPEGDASH] ), analizzerà il payload XML e lo convertirà in una casella 'emsg' versione 1 del frammento MP4 per l'archiviazione nell'archivio live e la trasmissione a Media Services Packager.   Packager rileverà la casella 'emsg' nel live streaming e:

- a) "imballare dinamicamente" nei segmenti di TS per la consegna ai client HLS in conformità con la specifica dei metadati a tempo possibile HLS [HLS-TMD], oppure
- b) passarlo per la consegna in frammenti CMAF tramite HLS o DASH, o 
- (c) convertirlo in un segnale di traccia sparse per la consegna tramite Smooth Streaming [MS-SSTR].

Oltre ai pacchetti CMAF o TS PES in formato "emsg" in banda per HLS, i manifesti per DASH (MPD) e Smooth Streaming conterranno un riferimento ai flussi di eventi in banda (noti anche come traccia di flusso sparse in Smooth Streaming). 

I singoli eventi o i relativi payload di dati NON vengono restituiti direttamente nei manifesti HLS, DASH o Smooth. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Ulteriori vincoli informativi e valori predefiniti per gli eventi onUserDataEventAdditional informational constraints and defaults for onUserDataEvent events

- Se la scala cronologica non è impostata nell'elemento EventStream, per impostazione predefinita viene utilizzata la scala cronologica RTMP 1 kHz
- Il recapito di un messaggio onUserDataEvent è limitato a una volta ogni 500 ms al massimo. Se si inviano eventi più frequentemente, ciò può influire sulla larghezza di banda e sulla stabilità del feed live

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 Segnalazione di segnale di fronte rtMP con "onAdCue"

Servizi multimediali di Azure può ascoltare e rispondere a diversi tipi di messaggio [AMF0] che possono essere usati per segnalare vari metadati sincronizzati in tempo reale nel live streaming.  La specifica [Adobe-Primetime] definisce due tipi di cue denominati modalità "semplice" e "SCTE-35". Per la modalità "semplice", Servizi multimediali supporta un singolo messaggio di cue AMF denominato "onAdCue" utilizzando un payload che corrisponde alla tabella seguente definita per il segnale "Simple Mode".  

La sezione seguente mostra il payload RTMP "simple" mode, che può essere utilizzato per segnalare un segnale di annuncio "spliceOut" di base che verrà trasportato attraverso il manifesto del client per HLS, DASH e Microsoft Smooth Streaming. Ciò è molto utile per gli scenari in cui il cliente non dispone di un complesso sistema di distribuzione o inserimento degli annunci basato su SCTE-35 e utilizza un codificatore locale di base per inviare il messaggio cue tramite un'API. In genere il codificatore locale supporterà un'API basata su REST per attivare questo segnale, che anche "splice-condition" il flusso video inserendo un fotogramma IDR nel video e avviando un nuovo GOP.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 Segnalazione di cue RTMP con "onAdCue" - Modalità semplice

| Nome campo | Tipo di campo | Obbligatorio? | Descrizioni                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | string     | Obbligatoria  | Messaggio di evento.  Deve essere "SpliceOut" per designare una giunzione modalità semplice.                                                                                                                                                                                                         |
| id         | string     | Obbligatoria  | Un identificatore univoco che descrive la giunzione o il segmento. Identifica questa istanza del messaggio                                                                                                                                                                                       |
| duration   | Number     | Obbligatoria  | La durata della giunzione. Le unità sono espresse in secondi frazionari.                                                                                                                                                                                                                           |
| elapsed    | Number     | Facoltativo  | Se il segnale dell'annuncio viene ripetuto per supportare l'ottimizzazione, questo campo deve indicare il tempo di presentazione trascorso dall'inizio della giunzione. Le unità sono espresse in secondi frazionari. Quando si usa la modalità semplice, questo valore non deve superare la durata originale della giunzione. |
| time       | Number     | Obbligatoria  | Deve essere l'ora della giunzione, nell'ora di presentazione. Le unità sono espresse in secondi frazionari.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Esempio di output del manifesto MPEG DASH quando si utilizza la modalità semplice Adobe RTMP

Consultate l'esempio [3.3.2.1 MPEG DASH .mpd EventStream utilizzando la modalità semplice Adobe](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Consultate il manifesto DASH di esempio [3.3.3.1 con singolo punto e la modalità semplice di Adobe](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Esempio di output del manifesto HLS quando si utilizza la modalità semplice Adobe RTMP

Consultate l'esempio [3.2.2 Manifesto HLS con la modalità semplice di Adobe e il tag EXT-X-CUE](#322-apple-hls-with-adobe-primetime-ext-x-cue-legacy)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 Segnalazione di cue RTMP con "onAdCue" - Modalità SCTE-35

Quando si lavora con un flusso di lavoro di produzione broadcast più avanzato che richiede il messaggio di payload SCTE-35 completo da portare attraverso il manifesto HLS o DASH, è meglio usare la "modalità SCTE-35" della specifica [Adobe-Primetime].  Questa modalità supporta i segnali SCTE-35 inviati direttamente in un codificatore live locale, che quindi codifica i segnali nel flusso RTMP utilizzando la "modalità SCTE-35" specificata nella specifica [Adobe-Primetime]. 

In genere i messaggi SCTE-35 possono essere visualizzati solo negli input del flusso di trasporto (TS) MPEG-2 in un codificatore locale. Per informazioni dettagliate su come configurare un'ingestione del flusso di trasporto contenente SCTE-35 e abilitarlo per il pass-through a RTMP in modalità Adobe SCTE-35, rivolgersi al produttore del codificatore.

In questo scenario, il payload seguente DEVE essere inviato dal codificatore locale utilizzando il tipo di messaggio **"onAdCue"** [AMF0].

| Nome campo | Tipo di campo | Obbligatorio? | Descrizioni                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| cue        | string     | Obbligatoria  | Messaggio di evento.  Per i messaggi [SCTE-35], deve essere il file binario splice_info_section() con codifica base64 per l'invio dei messaggi ai client HLS, Smooth ed Dash con codifica base64.                                                                                                                                                                                                                               |
| type       | string     | Obbligatoria  | Un URN o URL che identifica lo schema del messaggio. Per i messaggi [SCTE-35], questo **dovrebbe** essere **"scte35"** per consentire l'invio di messaggi ai client HLS, Smooth ed Dash, in conformità con [Adobe-Primetime]. Facoltativamente, l'URN "urn:scte:scte35:2013:bin" può essere utilizzato anche per segnalare un messaggio [SCTE-35].                                                                                                        |
| id         | string     | Obbligatoria  | Un identificatore univoco che descrive la giunzione o il segmento. Identifica l'istanza del messaggio.  I messaggi semanticamente equivalenti devono avere lo stesso valore.                                                                                                                                                                                                                                                       |
| duration   | Number     | Obbligatoria  | La durata dell'evento o del segmento di giunzione, se conosciuto. Se sconosciuto, il valore **DOVREBBE** essere 0.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Number     | Facoltativo  | Se il segnale dell'annuncio [SCTE-35] viene ripetuto per ottimizzarlo, questo campo deve indicare il tempo di presentazione trascorso dall'inizio della giunzione. Le unità sono espresse in secondi frazionari. In modalità [SCTE-35], questo valore può superare la durata originale specificata della giunzione o del segmento.                                                                                                                   |
| time       | Number     | Obbligatoria  | L'ora di presentazione dell'evento o della giunzione annuncio.  Il tempo di presentazione e la durata **dovrebbero** essere allineati con i punti di accesso al flusso (SAP) di tipo 1 o 2, come definito nell'allegato IsO-14496-12] I. Per HLS in uscita, tempo e durata **SHOULD** allineati ai limiti del segmento. L'ora di presentazione e la durata di diversi messaggi di eventi all'interno dello stesso flusso di eventi non devono (MUST NOT) sovrapporsi. Le unità sono espresse in secondi frazionari. |

---

#### <a name="example-mpeg-dash-mpd-manifest-with-scte-35-mode"></a>Esempio di manifesto MPEG DASH .mpd con modalità SCTE-35
Vedere [sezione 3.3.3.2 Manifesto DASH di esempio con SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Esempio di manifesto HLS .m3u8 con segnale in modalità SCTE-35
Vedere [sezione 3.2.1.1 manifesto HLS di esempio con SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)

## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 Segnalazione di annunci RTMP con "onCuePoint" per Elemental Live

Il codificatore locale Elemental Live supporta i marcatori degli annunci nel segnale RTMP. Servizi multimediali di Azure attualmente supporta solo il tipo di indicatore di annunci "onCuePoint" per RTMP.  Questa opzione può essere abilitata nelle impostazioni del gruppo Adobe RTMP nelle impostazioni o api del codificatore Elemental Media Live impostando "**ad_markers**" su "onCuePoint".  Si prega di fare riferimento alla documentazione Elementale Live per i dettagli. L'abilitazione di questa funzionalità nel gruppo RTMP comporta il passaggio dei segnali SCTE-35 agli output di Adobe RTMP che devono essere elaborati da Servizi multimediali di Azure.

Il tipo di messaggio "onCuePoint" è definito in [Adobe-Flash-AS] e ha la seguente struttura di payload quando viene inviato dall'output Elemental Live RTMP.


| Proprietà   | Descrizione                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | Il nome SHOULD be '**scte35**' by Elemental Live.                                                                                                                                                                              |
| time       | Il tempo in secondi in cui si è verificato il cue point nel file video durante la timeline                                                                                                                                           |
| type       | Il tipo di cue point SHOULD deve essere impostato su "**event**".                                                                                                                                                                             |
| parametri | Matrice associativa di stringhe di coppie nome/valore contenente le informazioni del messaggio SCTE-35, inclusi ID e durata. Questi valori vengono analizzati da Servizi multimediali di Azure e inclusi nel tag di decorazione del manifesto. |


Quando viene utilizzata questa modalità di marcatore annuncio, l'output del manifesto HLS è simile alla modalità "Semplice" di Adobe.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Esempio MPEG DASH MPD, singolo periodo, segnali in modalità Adobe Simple

~~~ xml
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
~~~

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Esempio di playlist HLS, segnali di modalità Adobe Simple che utilizzano il tag EXT-X-CUE (troncerato "..." per brevità)

L'esempio seguente mostra l'output del packager dinamico di Servizi multimediali per un flusso di inserimento RTMP utilizzando i segnali in modalità "semplice" di Adobe e il tag LEGACY [Adobe-Primetime] EXT-X-CUE.  

~~~
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

~~~

### <a name="216-cancellation-and-updates"></a>2.1.6 Cancellazione e aggiornamenti

I messaggi possono essere annullati o aggiornati inviando più messaggi con ora di presentazione e ID uguali. L'ora di presentazione e l'ID identificano in modo univoco l'evento e l'ultimo messaggio ricevuto per un'ora di presentazione specifica che soddisfa i vincoli preroll è il messaggio su cui viene eseguita un'operazione. L'evento aggiornato sostituisce eventuali messaggi ricevuti in precedenza. Il vincolo di preroll è 4 secondi. Sui messaggi ricevuti almeno quattro secondi prima dell'ora di presentazione verranno eseguite le operazioni.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Inserimento MP4 frammentato (Smooth Streaming)

Fare riferimento a [MS-SSTR-Ingest] per i requisiti per l'inserimento in live streaming. Le sezioni seguenti descrivono i dettagli relativi all'inserimento dei metadati di presentazione programmati.  I metadati della presentazione a tempo vengono ingeriti come traccia sparse, definita sia nella finestra manifesto del server dal vivo (vedere MS-SSTR) che nella finestra del filmato ('moov').  

Ogni frammento sparse è costituito da una finestra di frammento di film ('moof') e Media Data Box ('mdat'), dove la casella 'mdat' è il messaggio binario.

Per ottenere l'inserimento accurato degli annunci, il codificatore DEVE dividere il frammento nel momento in cui è necessario inserire il segnale.  Deve essere creato un nuovo frammento che inizia con un frame IDR appena creato o Stream Access Points (SAP) di tipo 1 o 2, come definito in [ISO-14496-12] Allegato I. Ciò consente a Azure Media Packager di generare correttamente un manifesto HLS e un manifesto multiperiodo DASH in cui il nuovo periodo inizia al tempo di presentazione condizionato con giunzione accurata al frame.

### <a name="221-live-server-manifest-box"></a>2.2.1 Casella Live Server Manifest Box

La traccia sparse **DEVE** essere dichiarata nella casella Manifesto Live Server con una ** \<\> ** voce textstream e **DEVE** avere i seguenti attributi impostati:

| **Nome attributo** | **Tipo di campo** | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Number         | Obbligatoria      | **DEVE** essere "0", che indica una traccia con bitrate variabile sconosciuto.                                                                                                                                                          |
| parentTrackName    | string         | Obbligatoria      | **DEVE** essere il nome della traccia padre, a cui sono allineati i codici temporali di tipo sparse. La traccia padre non può essere una traccia di tipo sparse.                                                                             |
| manifestOutput     | Boolean        | Obbligatoria      | **DEVE** essere "true", per indicare che la traccia sparse verrà incorporata nel manifesto client Smooth.                                                                                                                        |
| Subtype            | string         | Obbligatoria      | **DEVE** essere il codice a quattro caratteri "DATA".                                                                                                                                                                                  |
| Schema             | string         | Obbligatoria      | **DEVE** essere un URN o un URL che identifica lo schema dei messaggi. Per i messaggi [SCTE-35], **questo DEVE** essere "urn:scte:scte35:2013:bin" per consentire l'invio di messaggi ai client HLS, Smooth ed Dash in conformità con [SCTE-35]. |
| trackName          | string         | Obbligatoria      | **DEVE** essere il nome della traccia sparse. Il trackName può essere utilizzato per differenziare più flussi di eventi con lo stesso schema. Ogni flusso di eventi univoco **DEVE** avere un nome di traccia univoco.                                |
| timescale          | Number         | Facoltativo      | **DEVE** essere la scala cronologica della traccia padre.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Casella Movie Box

La finestra del film ('moov') segue la finestra manifesto del server Live come parte dell'intestazione del flusso per una traccia sparse.

La casella 'moov' **deve** contenere una casella **TrackHeaderBox ('tkhd')** come definito in [ISO-14496-12] con i seguenti vincoli:

| **Nome campo** | **Tipo di campo**          | **Obbligatorio?** | **Descrizione**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | Intero senza segno a 64 bit | Obbligatoria      | **DOVREBBE** essere 0, poiché la scatola delle tracce ha zero campioni e la durata totale dei campioni nella scatola delle tracce è 0. |

---

La casella 'moov' **SHOULD** contiene un **HandlerBox ('hdlr')** come definito in [ISO-14496-12] con i seguenti vincoli:

| **Nome campo** | **Tipo di campo**          | **Obbligatorio?** | **Descrizione**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | Intero senza segno a 32 bit | Obbligatoria      | **DOVREBBE** essere 'meta'. |

---

La casella 'stsd' **deve** contenere una casella MetaDataSampleEntry con un nome di codifica come definito in [ISO-14496-12].  Ad esempio, per i messaggi SCTE-35 il nome di codifica **SHOULD** essere 'scte'.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Caselle Movie Fragment Box e Media Data Box

I frammenti di tracce sparse sono costituiti da una finestra di frammentazione del film ('moof') e da Media Data Box ('mdat').

> [!NOTE]
> Per ottenere l'inserimento accurato degli annunci, il codificatore DEVE dividere il frammento nel momento in cui è necessario inserire il segnale.  Deve essere creato un nuovo frammento che inizia con un frame IDR appena creato o Stream Access Points (SAP) di tipo 1 o 2, come definito in [ISO-14496-12] Allegato I
> 

La casella MovieFragmentBox ('moof') **DEVE** contenere una casella **TrackFragmentExtendedHeaderBox ('uuid')** come definito in [MS-SSTR] con i seguenti campi:

| **Nome campo**         | **Tipo di campo**          | **Obbligatorio?** | **Descrizione**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | Intero senza segno a 64 bit | Obbligatoria      | **DEVE** essere l'ora di arrivo dell'evento. Questo valore consente di allineare il messaggio con la traccia padre.           |
| fragment_duration      | Intero senza segno a 64 bit | Obbligatoria      | **DEVE** essere la durata dell'evento. La durata può essere zero per indicare che la durata è sconosciuta. |

---


La casella MediaDataBox ('mdat') **DEVE** avere il seguente formato:

| **Nome campo**          | **Tipo di campo**                   | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | Intero senza segno a 32 bit (uimsbf) | Obbligatoria      | Determina il formato del contenuto della casella 'mdat'. Le versioni non riconosciute verranno ignorate. Al momento, l'unica versione supportata è 1.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | Intero senza segno a 32 bit (uimsbf) | Obbligatoria      | Identifica l'istanza del messaggio. I messaggi semanticamente equivalenti devono avere lo stesso valore, vale a dire che è sufficiente l'elaborazione di una qualsiasi casella di messaggio evento con lo stesso ID.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | Intero senza segno a 32 bit (uimsbf) | Obbligatoria      | La somma dei fragment_absolute_time, specificata in TrackFragmentExtendedHeaderBox, e il presentation_time_delta **DEVE** essere l'ora di presentazione dell'evento. Il tempo di presentazione e la durata **dovrebbero** essere allineati con i punti di accesso al flusso (SAP) di tipo 1 o 2, come definito nell'allegato IsO-14496-12] I. Per HLS in uscita, tempo e durata **SHOULD** allineati ai limiti del segmento. Il tempo di presentazione e la durata dei diversi messaggi di evento all'interno dello stesso flusso di eventi non **DEVONO** sovrapporsi. |
| message                 | matrice di byte                       | Obbligatoria      | Messaggio di evento. Per i messaggi [SCTE-35], il messaggio è il splice_info_section(). Per i messaggi [SCTE-35], questa **DEVE** essere la splice_info_section() per consentire l'invio di messaggi ai client HLS, Smooth ed Dash conformi a [SCTE-35]. Per i messaggi [SCTE-35], il file binario splice_info_section() è il payload della casella 'mdat' e **NON** è codificato in base64.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Annullamento e aggiornamenti

I messaggi possono essere annullati o aggiornati inviando più messaggi con ora di presentazione e ID uguali.  Il tempo di presentazione e l'ID identificano in modo univoco l'evento. L'ultimo messaggio ricevuto per un'ora di presentazione specifica che soddisfa i vincoli preroll è il messaggio su cui viene eseguita un'operazione. Il messaggio aggiornato sostituisce eventuali messaggi ricevuti in precedenza.  Il vincolo di preroll è 4 secondi. Sui messaggi ricevuti almeno quattro secondi prima dell'ora di presentazione verranno eseguite le operazioni. 


## <a name="3-timed-metadata-delivery"></a>3 Recapito di metadati programmati

I dati del flusso di eventi sono opachi per Servizi multimediali. Servizi multimediali si limita a passare tre tipi di informazioni tra l'endpoint di inserimento e l'endpoint client. Le seguenti proprietà vengono consegnate al client, in conformità con [SCTE-35] e/o con il protocollo di streaming del client:

1.  Schema: un URN o URL che identifica lo schema del messaggio.
2.  Ora di presentazione: l'ora di presentazione dell'evento sulla scala cronologica dei file multimediali.
3.  Durata: la durata dell'evento.
4.  ID: un identificatore univoco facoltativo per l'evento.
5.  Messaggio: i dati dell'evento.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Manifesto Microsoft Smooth Streaming  

Fare riferimento alla gestione della traccia sparse [MS-SSTR] per informazioni dettagliate su come formattare una traccia dei messaggi sparse. Per i messaggi [SCTE35], Smooth Streaming emetterà il splice_info_section() con codifica base64 in un frammento sparse.
StreamIndex **DEVE** avere un sottotipo di "DATA" e gli CustomAttributes **DEVONO** contenere un attributo con Nome "Schema" e Value "urn:scte35:2013:bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Esempio di manifesto del client Smooth che mostra la codifica Base64 [SCTE35] splice_info_section()
~~~ xml
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
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Decorazione del manifesto Apple HLS

Servizi multimediali di Azure supporta i tag manifesto HLS seguenti per la segnalazione delle informazioni relative all'accesso esterno durante un evento live o su richiesta. 

- EXT-X-DATERANGE come definito in Apple HLS [RFC8216]
- EXT-X-CUE come definito in [Adobe-Primetime] - questa modalità è considerata "legacy". I clienti devono adottare il tag EXT-X-DATERANGE quando possibile.

L'output dei dati per ogni tag varia in base alla modalità del segnale di inserimento utilizzata. Ad esempio, l'inserimento RTMP con la modalità Adobe Simple non contiene il payload completo con codifica Base64 SCTE-35.

## <a name="321-apple-hls-with-ext-x-daterange-recommended"></a>3.2.1 Apple HLS con EXT-X-DATERANGE (consigliato)

La specifica Apple HTTP Live Streaming [RFC8216] consente la segnalazione dei messaggi [SCTE-35]. I messaggi vengono inseriti nella playlist dei segmenti in un tag EXT-X-DATERANGE per ogni sezione [RFC8216] intitolata "Mapping SCTE-35 into EXT-X-DATERANGE".  Il livello dell'applicazione client può analizzare la playlist M3U ed elaborare i tag M3U o ricevere gli eventi tramite il framework Apple Player.  

L'approccio **RECOMMENDED** in Servizi multimediali di Azure (API versione 3) consiste nel seguire [RFC8216] e usare il tag EXT-X_DATERANGE per la decorazione di annunci [SCTE35] nel manifesto.

## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35"></a>3.2.1.1 Manifesto HLS di esempio .m3u8 con segnalazione EXT-X-DATERANGE di SCTE-35

Nell'esempio seguente l'output del manifesto HLS dal packager dinamico di Servizi multimediali mostra l'uso del tag EXT-X-DATERANGE di [RFC8216] che segnala gli eventi SCTE-35 nel flusso. Inoltre, questo flusso contiene il tag EXT-X-CUE "legacy" per [Adobe-Primetime].

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


## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS con Adobe Primetime EXT-X-CUE (legacy)

È inoltre disponibile un'implementazione "legacy" fornita in Servizi multimediali di Azure (API versione 2 e 3) che usa il tag EXT-X-CUE come definito in [Adobe-Primetime] "SCTE-35 Mode". In questa modalità, Servizi multimediali di Azure incorporerà la codifica base64 [SCTE-35] splice_info_section() nel tag EXT-X-CUE.  

Il tag EXT-X-CUE "legacy" è definito come di seguito e può anche essere referenziato con normative nella specifica [Adobe-Primetime]. Deve essere utilizzato solo per la segnalazione SCTE35 legacy dove necessario, altrimenti il tag consigliato è definito in [RFC8216] come EXT-X-DATERANGE. 

| **Nome attributo** | **Tipo**                      | **Obbligatorio?**                             | **Descrizione**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CUE                | stringa tra virgolette                 | Obbligatoria                                  | Il messaggio codificato come stringa con codifica base64 come descritto in [RFC4648]. Per i messaggi [SCTE-35], si tratta del splice_info_section().                                                                                                                                      |
| TYPE               | stringa tra virgolette                 | Obbligatoria                                  | Un URN o URL che identifica lo schema del messaggio. Per i messaggi [SCTE-35], il tipo accetta il valore speciale "scte35".                                                                                                                                                                          |
| ID                 | stringa tra virgolette                 | Obbligatoria                                  | Un identificatore univoco dell'evento. Se al momento dell'inserimento del messaggio non viene specificato l'ID, Servizi multimediali di Azure genererà un ID univoco.                                                                                                                                              |
| DURATION           | numero a virgola mobile decimale | Obbligatoria                                  | Durata dell'evento. Se sconosciuto, il valore **DOVREBBE** essere 0. Le unità sono espresse in secondi frazionari.                                                                                                                                                                                           |
| ELAPSED            | numero a virgola mobile decimale | Facoltativo, ma necessari per la finestra temporale scorrevole | Quando il segnale viene ripetuto per supportare una finestra di presentazione scorrevole, questo campo **DEVE** essere la quantità di tempo di presentazione trascorso dall'inizio dell'evento. Le unità sono espresse in secondi frazionari. Questo valore può superare la durata originale specificata della giunzione o del segmento. |
| TIME               | numero a virgola mobile decimale | Obbligatoria                                  | L'ora di presentazione dell'evento. Le unità sono espresse in secondi frazionari.                                                                                                                                                                                                                        |


Il livello dell'applicazione del lettore HLS userà l'attributo TYPE per identificare il formato del messaggio, decodificare il messaggio, applicare le conversioni temporali necessarie ed elaborare l'evento.  Gli eventi sono sincronizzati temporalmente nella playlist del segmento della traccia padre, in base al timestamp dell'evento.  Essi vengono inserite prima del segmento più vicino (tag #EXTINF).

### <a name="323-hls-m3u8-manifest-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 Esempio di manifesto HLS .m3u8 utilizzando "Legacy" Adobe Primetime EXT-X-CUE

L'esempio seguente mostra la decorazione del manifesto HLS usando il tag Adobe Primetime EXT-X-CUE.  Il parametro "CUE" contiene solo le proprietà TYPE e Duration, il che significa che si trattava di un'origine RTMP che utilizzava la modalità "semplice" di segnalazione Adobe.  Se si trattasse di un segnale in modalità SCTE-35, il tag includerebbe il payload binario SCTE-35 codificato in base64 come illustrato [nell'esempio 3.2.1.1](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).

~~~
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

~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 Gestione dei messaggi HLS per "Legacy" Adobe Primetime EXT-X-CUE

Gli eventi vengono segnalati nella playlist del segmento di ogni traccia video e audio. La posizione del tag EXT-X-CUE **DEVE** essere sempre immediatamente prima del primo segmento HLS (per la giunzione o l'inizio del segmento) o immediatamente dopo l'ultimo segmento HLS (per giunzione in o fine segmento) a cui i suoi attributi TIME e DURATION si riferiscono, come richiesto da [Adobe-Primetime].

Quando una finestra di presentazione scorrevole è abilitata, il tag EXT-X-CUE **DEVE** essere ripetuto abbastanza spesso che la giunzione o il segmento è sempre completamente descritto nella playlist del segmento e l'attributo ELAPSED **DEVE** essere utilizzato per indicare la quantità di tempo in cui la giunzione o il segmento è stato attivo, come richiesto da [Adobe-Primetime].

Se una finestra temporale scorrevole di presentazione è abilitata, i tag EXT-X-CUE vengono rimossi dalla playlist del segmento quando il tempo file multimediali a cui fanno riferimento non rientra nella finestra temporale scorrevole di presentazione.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 Decorazione del manifesto DASH (MPD)

[MPEGDASH] fornisce tre modi per segnalare gli eventi:

1.  Eventi segnalati in MPD EventStreamEvents signaled in the MPD EventStream
2.  Eventi segnalati in banda utilizzando la finestra di messaggio dell'evento ('emsg')
3.  Una combinazione di 1 e 2

Gli eventi segnalati in MPD EventStream sono utili per lo streaming VOD perché i client hanno accesso a tutti gli eventi, immediatamente quando viene scaricato MPD. È utile anche per la segnalazione SSAI, in cui il fornitore SSAI a valle deve analizzare i segnali da un manifesto MPD per più periodi e inserire contenuti dell'annuncio in modo dinamico.  La soluzione in-band ('emsg')è utile per lo streaming live in cui i client non devono scaricare nuovamente il MPD o non è presente alcuna manipolazione del manifesto SSAI tra il client e l'origine. 

Il comportamento predefinito di Servizi multimediali di Azure per DASH consiste nel segnalare sia in MPD EventStream che in banda usando la finestra di messaggio dell'evento ('emsg').

I messaggi cue ingeriti su [RTMP] o [MS-SSTR-Ingest] vengono mappati in eventi DASH, utilizzando caselle 'emsg' in banda e/o EventStream in-MPD. 

La segnalazione SCTE-35 in banda per DASH segue la definizione e i requisiti definiti in [SCTE-214-3] e anche nella sezione [DASH-IF-IOP] 13.12.2 ('SCTE35 Events'). 

Per il carrello in banda [SCTE-35], la finestra di messaggio dell'evento ('emsg') utilizza lo schemaId " urn:scte:scte35:2013:bin". Per la decorazione del manifesto MPD, eventStream schemeId usa "urn:scte:scte35:2014:xml-bin".  Questo formato è una rappresentazione XML dell'evento che include un output binario con codifica base64 del messaggio SCTE-35 completo arrivato all'inserimento. 

Le definizioni di riferimento normativo dei messaggi cue [SCTE-35] in DASH sono disponibili in [SCTE-214-1] sec 6.7.4 (MPD) e [SCTE-214-3] sec 7.3.2 (Carriage of SCTE 35 cue messages).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG DASH (MPD) EventStream Segnalazione

La decorazione manifesto (MPD) degli eventi verrà segnalata in MPD utilizzando l'elemento EventStream, che viene visualizzato all'interno dell'elemento Period. Lo schemaId utilizzato è "urn:scte:scte35:2014:xml-bin".

> [!NOTE]
> Ai fini della brevità [SCTE-35] consente l'utilizzo della sezione con codifica base64 nell'elemento Signal.Binary (anziché l'elemento Signal.SpliceInfoSection) in alternativa al carrello di un messaggio cue completamente analizzato.
> Servizi multimediali di Azure usa questo approccio "xml" per la segnalazione nel manifesto MPD.
> Questo è anche il metodo consigliato utilizzato nella [DASH-IF-IOP] - vedere la sezione intitolata "Flussi di eventi di [inserimento ad" della linea guida DASH IF IOP](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

L'elemento EventStream ha gli attributi seguenti:

| **Nome attributo** | **Tipo**                | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | string                  | Obbligatoria      | Identifica lo schema del messaggio. Lo schema è impostato sul valore dell'attributo Schema nella casella Live Server Manifest. Il valore **DEVE** essere un URN o un URL che identifica lo schema del messaggio; Lo schemadi output supportato deve essere "urn:scte:scte35:2014:xml-bin" per [SCTE-214-1] sec 6.7.4 (MPD), poiché il servizio supporta solo "xml-bin" in questo momento per brevità nel MPD. |
| value              | string                  | Facoltativo      | Un valore di stringa aggiuntivo usato dai proprietari dello schema per personalizzare la semantica del messaggio. Per differenziare più flussi di eventi con lo stesso schema, il valore **DEVE** essere impostato sul nome del flusso di eventi (trackName per [MS-SSTR-Ingest] o nome del messaggio AMF per l'inserimento di [RTMP]).                                                                         |
| Scala cronologica          | Intero senza segno a 32 bit | Obbligatoria      | La scala cronologica, in zecche al secondo.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 Flussi di eventi di esempio per MPEG DASH

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 Esempio di MPEG DASH .mpd manifest segnalazione dello streaming RTMP utilizzando la modalità semplice di Adobe

L'esempio seguente mostra un estratto EventStream dal packager dinamico di Servizi multimediali per un flusso RTMP utilizzando la segnalazione della modalità "semplice" di Adobe.

~~~ xml
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
~~~

#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 Esempio di segnalazione del manifesto mPEG DASH .mpd di un flusso RTMP con la modalità Adobe SCTE-35

L'esempio seguente mostra un estratto EventStream dal packager dinamico di Servizi multimediali per un flusso RTMP utilizzando la segnalazione della modalità Adobe SCTE-35.

~~~ xml
<!-- Example EventStream element using xml+bin style signaling per [SCTE-214-1] -->

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
~~~

> [!IMPORTANT]
> Si noti che presentationTime è l'ora di presentazione dell'evento [SCTE-35] tradotto in modo che sia relativo all'ora di inizio del periodo, non all'ora di arrivo del messaggio.
> [MPEGDASH] definisce Event@presentationTime il come "Specifica il tempo di presentazione dell'evento rispetto all'inizio del Periodo.
> Il valore del tempo di presentazione in secondi è la divisione EventStream@timescale del valore di questo attributo e del valore dell'attributo.
> Se non è presente, il valore dell'ora di presentazione è 0.

#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 Manifesto MPEG DASH di esempio (MPD) con un singolo punto, EventStream, utilizzando i segnali in modalità semplice di Adobe

L'esempio seguente mostra l'output del packager dinamico di Servizi multimediali per un flusso RTMP di origine utilizzando il metodo di segnale di annuncio in modalità "semplice" di Adobe. L'output è un manifesto a periodo singolo che mostra un EventStream usando l'URI schemeId impostato su "urn:com:adobe:dpi:simple:2015" e la proprietà value impostata su "simplesignal".
Ogni segnale semplice viene fornito in @presentationTime @durationun @id elemento Event con le proprietà , e popolate in base ai segnali semplici in ingresso.

~~~ xml
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

~~~

#### <a name="3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling"></a>3.3.3.2 Manifesto MPEG DASH di esempio (MPD) con più periodi, EventStream, con segnalazione in modalità Adobe SCTE35

L'esempio seguente mostra l'output del packager dinamico di Servizi multimediali per un flusso RTMP di origine utilizzando la segnalazione della modalità Adobe SCTE35.
In questo caso, il manifesto di output è un DASH con @schemeIdUri più periodi con un elemento EventStream e proprietà impostata su "urn:scte35:2014:xml-bin" e una @value proprietà impostata su "scte35". Ogni elemento Event in EventStream contiene il segnale SCTE35 binario codificato in base64 completo 

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
                    
                    <!--> ... aduio segments truncated for sample brevity </-->

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
### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG DASH In-band Segnale della finestra di messaggio degli eventi

Un flusso di eventi in banda richiede che MPD abbia un elemento InbandEventStream a livello del set di adattamento.  Questo elemento dispone di un attributo schemeIdUri obbligatorio e di un attributo facoltativo della scala cronologica, che vengono visualizzati anche nella finestra di messaggio dell'evento ('emsg').  Finestre di messaggio di evento con identificatori di schema non definiti in MPD **SHOULD** non essere presenti.

Per il carrello in banda [SCTE-35], i segnali **DEVONO** utilizzare lo schemaId "urn:scte:scte35:2013:bin".
Le definizioni normative del carrello dei messaggi in banda [SCTE-35] sono definite in [SCTE-214-3] sec 7.3.2 (Carriage of SCTE 35 cue messages).

I dettagli seguenti illustrano i valori specifici che il cliente dovrebbe aspettarsi in 'emsg' in conformità con [SCTE-214-3]:

| **Nome campo**          | **Tipo di campo**          | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | string                  | Obbligatoria      | Identifica lo schema del messaggio. Lo schema è impostato sul valore dell'attributo Schema nella casella Live Server Manifest. Il valore **DEVE** essere un URN che identifica lo schema dei messaggi. Per i messaggi [SCTE-35], **questo DEVE** essere "urn:scte:scte35:2013:bin" in conformità con [SCTE-214-3]          |
| valore                   | string                  | Obbligatoria      | Un valore di stringa aggiuntivo usato dai proprietari dello schema per personalizzare la semantica del messaggio. Per poter distinguere più flussi di eventi con lo stesso schema, il valore verrà impostato sul nome del flusso di eventi (trackName per l'inserimento Smooth o nome del messaggio AMF per l' inserimento RTMP). |
| Scala cronologica               | Intero senza segno a 32 bit | Obbligatoria      | La scala cronologica, in segni di graduazione al secondo, dei campi time e duration all'interno della casella 'emsg'.                                                                                                                                                                                                            |
| Presentation_time_delta | Intero senza segno a 32 bit | Obbligatoria      | Delta del tempo della presentazione multimediale dell'evento è l'ora di presentazione meno recente in questo segmento. Il tempo di presentazione e la durata **dovrebbero** essere allineati con i punti di accesso al flusso (SAP) di tipo 1 o 2, come definito nell'allegato IsO-14496-12] I.                                  |
| event_duration          | Intero senza segno a 32 bit | Obbligatoria      | La durata dell'evento o 0xFFFFFFFF per indicare una durata sconosciuta.                                                                                                                                                                                                                              |
| ID                      | Intero senza segno a 32 bit | Obbligatoria      | Identifica l'istanza del messaggio. I messaggi semanticamente equivalenti devono avere lo stesso valore. Se al momento dell'inserimento del messaggio non viene specificato l'ID, Servizi multimediali di Azure genererà un ID univoco.                                                                                        |
| Message_data            | matrice di byte              | Obbligatoria      | Messaggio di evento. Per i messaggi [SCTE-35], i dati del messaggio sono il file binario splice_info_section() in conformità con [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Esempio di entità InBandEvenStream per la modalità Adobe Simple
~~~ xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
~~~

### <a name="335-dash-message-handling"></a>3.3.5 Gestione dei messaggi DASH

Gli eventi vengono segnalati in banda, all'interno della casella 'emsg', sia per le tracce video che audio.  La segnalazione si verifica per tutte le richieste di segmenti per cui presentation_time_delta è 15 secondi o inferiore. 

Se una finestra temporale scorrevole di presentazione è abilitata, i messaggi eventi vengono rimossi da MPD quando la somma di ora e durata del messaggio evento è inferiore alla durata dei dati dei file multimediali nel manifesto.  In altre parole, i messaggi eventi vengono rimossi dal manifesto quando il tempo file multimediali a cui fanno riferimento non rientra nella finestra temporale scorrevole di presentazione.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 Indicazioni sull'implementazione dell'inserimento per i fornitori di codificatori

Le linee guida seguenti sono problemi comuni che possono influire sull'implementazione di questa specifica da parte di un fornitore di codificatori.  Le linee guida riportate di seguito sono state raccolte in base al feedback dei partner del mondo reale per semplificare l'implementazione di questa specifica per altri utenti. 

I messaggi [SCTE-35] vengono ingeriti in formato binario utilizzando lo schema **"urn:scte:scte35:2013:bin"** per [MS-SSTR-Ingest] e il tipo **"scte35"** per [RTMP] ingest. Per semplificare la conversione dell'intervallo [SCTE-35], che si basa sui timestamp ora di presentazione (PTS) del flusso di trasporto MPEG-2, il mapping tra PTS (pts_time + pts_adjustment of the splice_time()) e la scala cronologica dei file multimediali viene fornito dall'ora di presentazione dell'evento (il campo fragment_absolute_time per l'inserimento Smooth e il campo ora per l'inserimento RTMP). Il mapping è necessario perché il valore PTS a 33 bit viene aggiornato ogni 26,5 ore circa.

L'inserimento Smooth Streaming [MS-SSTR-Ingest] richiede che Media Data Box ('mdat') **contenga** il **splice_info_section()** definito in [SCTE-35]. 

Per l'inserimento RTMP, l'attributo cue del messaggio AMF è impostato sul **splice_info_section()** con codifica base64 definito in [SCTE-35].  

Quando i messaggi hanno il formato descritto in precedenza, vengono inviati ai client HLS, Smooth e DASH come definito in precedenza.  

Quando si esegue il test dell'implementazione con la piattaforma Servizi multimediali di Azure, iniziare a eseguire il test con un evento LiveEvent "pass-through", prima di passare al test in un evento LiveEvent di codifica.

---

## <a name="change-history"></a>Cronologia modifiche

| Data     | Modifiche                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Rerivisto inserimento RTMP per il supporto SCTE35, aggiunto RTMP "onCuePoint" per Elemental Live                                  |
| 08/22/19 | Aggiornato per aggiungere OnUserDataEvent a RTMP per i metadati personalizzati                                                          |
| 1/08/20  | Corretto l'errore sulla modalità RTMP Simple e RTMP SCTE35. Modificato da "onCuePoint" a "onAdCue". Tabella in modalità semplice aggiornata. |

## <a name="next-steps"></a>Passaggi successivi
Visualizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
