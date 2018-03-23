---
title: Servizi multimediali di Azure - Rettifica del protocollo Smooth Streaming (MS-SSTR) per HEVC | Microsoft Docs
description: Questa specifica descrive il protocollo e il formato per lo streaming live basato sul formato MP4 frammentato con HEVC in Servizi multimediali di Azure. Si tratta di una rettifica alla documentazione del protocollo Smooth Streaming (MS-SSTR) per includere il supporto dello streaming e dell'inserimento HEVC. In questo articolo vengono specificate solo le modifiche necessarie per usare HEVC, tranne nei casi in cui è riportato "(Nessuna modifica)" per indicare che il testo è stato copiato solo per maggiore chiarezza.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: johndeu; cenkd
ms.openlocfilehash: 5a8cdf4187c1b751e0c61e5c750646c5819b5c2b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="smooth-streaming-protocol-ms-sstr-ammendment-for-hevc"></a>Rettifica del protocollo Smooth Streaming (MS-SSTR) per HEVC

## <a name="1-introduction"></a>1 Introduzione 

Questo articolo fornisce le modifiche dettagliate da applicare alla specifica del protocollo Smooth Streaming [MS-SSTR] per abilitare lo Smooth Streaming di video con codifica HEVC. In questa specifica vengono illustrate solo le modifiche necessarie per usare il codec video HEVC. L'articolo segue lo stesso schema di numerazione della specifica [MS-SSTR]. I titoli vuoti presenti nell'articolo vengono forniti per far orientare il lettore all'interno della specifica [MS-SSTR].  "(Nessuna modifica)" indica che il testo è stato copiato solo per maggiore chiarezza.

L'articolo include i requisiti di implementazione tecnica per la segnalazione del codec video HEVC in un manifesto Smooth Streaming e sono stati aggiornati i riferimenti normativi per fare riferimento agli standard MPEG correnti che includono HEVC, Common Encryption di HEVC, e sono stati aggiornati i nomi dei riquadri per ISO Base Media File Format in modo da essere coerenti con le ultime specifiche. 

La specifica del protocollo Smooth Streaming di riferimento [MS-SSTR] descrive il formato di trasmissione usato per offrire contenuto multimediale live e on-demand come audio e video nei modi seguenti: da un codificatore a un server Web, da un server a un altro server e da un server a un client HTTP.
L'utilizzo di una distribuzione della struttura dei dati basata su MPEG-4 ([[MPEG-4-RA])](http://go.microsoft.com/fwlink/?LinkId=327787) tramite HTTP consente il passaggio senza interruzioni quasi in tempo reale tra diversi livelli di qualità di contenuto multimediale compresso. Il risultato è un'esperienza di riproduzione costante per l'utente finale del client HTTP, anche se le condizioni di rete e rendering video cambiano per il computer client o il dispositivo.

## <a name="11-glossary"></a>1.1 Glossario 

I termini seguenti sono definiti in *[MS-GLOS]*:

>   **identificatore univoco globale (GUID) identificatore univoco universale (UUID)**

I termini seguenti sono specifici di questo documento:

>  **ora di composizione:** ora in cui un campione viene presentato al client, come definito in [[ISO/IEC-14496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

>   **CENC**: Common Encryption, come definito in [ISO/IEC 23001-7] Second Edition.

>   **tempo di decodifica:** tempo necessario per la decodifica di un campione nel client, come definito in [[ISO/IEChttp://go.microsoft.com/fwlink/?LinkId=18369514496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

**frammento:** unità di **contenuto multimediale** scaricabile in modo indipendente che include uno o più **campioni**.

>   **HEVC:** High Efficiency Video Coding, come definito in [ISO/IEC 23008-2]

>   **manifesto:** metadati sulla **presentazione** che consentono a un client di effettuare richieste di **contenuto multimediale**. **contenuto multimediale:** dati audio, video e di testo compressi usati dal client per riprodurre una **presentazione**. **formato multimediale:** formato ben definito per la rappresentazione di audio o video come un **campione** compresso.

>   **presentazione:** set di tutti i **flussi** e metadati correlati necessari per riprodurre un singolo filmato. **richiesta:** messaggio HTTP inviato dal client al server, come definito in [[RFC2616].](http://go.microsoft.com/fwlink/?LinkId=90372) **risposta:** messaggio HTTP inviato dal server al client, come definito in [[RFC2616].](http://go.microsoft.com/fwlink/?LinkId=90372)

>   **campione:** l'unità fondamentale più piccola (ad esempio un fotogramma) in cui viene archiviato ed elaborato il **contenuto multimediale**.

>   **MAY, SHOULD, MUST, SHOULD NOT, MUST NOT:** questi termini (tutti in maiuscolo) vengono usati come descritto in [[RFC2119].](http://go.microsoft.com/fwlink/?LinkId=90317) Tutte le istruzioni di comportamento facoltativo usano MAY, SHOULD, o SHOULD NOT.

## <a name="12-references"></a>1.2 Riferimenti 
-----------

>   I riferimenti alla documentazione Microsoft Open Specifications non includono l'anno di pubblicazione perché i collegamenti sono alla versione più recente dei documenti, che vengono aggiornati di frequente. I riferimenti ad altri documenti includono l'anno di pubblicazione quando è disponibile.

 ### <a name="121-normative-references"></a>1.2.1 Riferimenti normativi 

>  [MS-SSTR] Smooth Streaming Protocol *v20140502* [http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/[MS-SSTR].pdf](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

>   [ISO/IEC 14496-12] International Organization for Standardization, "Information technology -- Coding of audio-visual objects -- Part 12: ISO Base Media File Format", ISO/IEC 14496-12:2014, Edition 4, Plus Corrigendum 1, Amendments 1 & 2.
>   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>

>   [ISO/IEC 14496-15] International Organization for Standardization, "Information technology -- Coding of audio-visual objects -- Part 15: Carriage of NAL unit structured video in the ISO Base Media File Format", ISO 14496-15:2015, Edition 3.
>   <http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>

>   [ISO/IEC 23008-2] Information technology -- High efficiency coding and media   delivery in heterogeneous environments -- Part 2: High efficiency video   coding: 2013 or newest edition   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>

>   [ISO/IEC 23001-7] Information technology — MPEG systems technologies — Part   7: Common encryption in ISO base media file format files, CENC Edition   2:2015 <http://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>

>   [RFC-6381] IETF RFC-6381, “The 'Codecs' and 'Profiles' Parameters for   "Bucket" Media Types” <http://tools.ietf.org/html/rfc6381>

>   [MPEG4-RA] The MP4 Registration Authority, "MP4REG", [http://www.mp4ra.org   ](http://go.microsoft.com/fwlink/?LinkId=327787)

>   [RFC2119] Bradner, S., "Key words for use in RFCs to Indicate Requirement   Levels", BCP 14, RFC 2119, March 1997,   [http://www.rfc-editor.org/rfc/rfc2119.txt   ](http://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 Riferimenti informativi 

>   [MS-GLOS] Microsoft Corporation, "*Windows Protocols Master Glossary*."

>   [RFC3548] Josefsson, S., Ed., "The Base16, Base32, and Base64 Data   Encodings", RFC 3548, July 2003, [http://www.ietf.org/rfc/rfc3548.txt   ](http://go.microsoft.com/fwlink/?LinkId=90432)

>   [RFC5234] Crocker, D., Ed., and Overell, P., "Augmented BNF for Syntax   Specifications: ABNF", STD 68, RFC 5234, January 2008,   [http://www.rfc-editor.org/rfc/rfc5234.txt   ](http://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 Panoramica 
---------

>   Di seguito sono riportate solo le modifiche apportate alla specifica di Smooth Streaming necessarie per l'uso di HEVC. Per orientarsi nella specifica di Smooth Streaming [MS-SSTR] di riferimento vengono elencate le intestazioni delle sezioni non modificate.

## <a name="14-relationship-to-other-protocols"></a>1.4 Relazione con altri protocolli 
--------------------------------

## <a name="15-prerequisitespreconditions"></a>1.5 Prerequisiti/Precondizioni 
----------------------------

## <a name="16-applicability-statement"></a>1.6 Dichiarazione di applicabilità 
------------------------

## <a name="17-versioning-and-capability-negotiation"></a>1.7 Controllo delle versioni e negoziazione dalla capacità 
--------------------------------------

## <a name="18-vendor-extensible-fields"></a>1.8 Campi estendibili dal fornitore 
-------------------------

>   Per identificare i flussi che adottano il formato video HEVC, è necessario usare il metodo seguente:

>   * **Codici descrittivi personalizzati per formati multimediali:** questa funzionalità viene fornita dal campo **FourCC**, come specificato nella sezione *2.2.2.5*.
>   I responsabili dell'implementazione possono garantire che le estensioni non siano in conflitto registrando i codici delle estensioni con MPEG-4-RA, come specificato in [[ISO/IEC-14496-12] ](http://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 Assegnazioni degli standard 
----------------------

# <a name="2-messages"></a>2 Messaggi 

## <a name="21-transport"></a>2.1 Trasporto 
----------

## <a name="22-message-syntax"></a>2.2 Sintassi dei messaggi 
---------------

### <a name="221-manifest-request"></a>2.2.1 Richiesta manifesto 

### <a name="222-manifest-response"></a>2.2.2 Risposta manifesto 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (variabile):** versione secondaria del messaggio di risposta manifesto. Deve essere impostato su 2. (Nessuna modifica)

>   **TimeScale (variabile):** scala cronologica dell'attributo Duration, specificato come numero di incrementi in un secondo. Il valore predefinito è
>   10000000. (Nessuna modifica)

>   Il valore consigliato è 90000 per rappresentare la durata esatta di fotogrammi e frammenti video contenenti video con frame rate frazionari (ad esempio, 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

ProtectionElement deve essere presente quando CENC (Common Encryption) viene applicato ai flussi video o audio. I flussi con crittografia HEVC devono essere conformi a Common Encryption Second Edition [ISO/IEC 23001-7]. Solo i dati di sezione in unità VCL NAL devono essere crittografati.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (variabile):** scala cronologica per i valori di durata e tempo in questo flusso, specificati come numero di incrementi in un secondo. Per i flussi HEVC è consigliabile un valore di 90000. Per i flussi audio è consigliabile un valore corrispondente alla frequenza di campionamento della forma d'onda (ad esempio, 44100 o 48000).

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (variabile):** codice di quattro caratteri che identifica il formato multimediale usato per ogni campione. L'intervallo di valori seguente è riservato con questi significati semantici:

>  * "hev1": i campioni video per questa traccia usano video HEVC, con il formato descrizione campione 'hev1' specificato in [ISO/IEC-14496-15].

>   **CodecPrivateData (variabile):** dati che indicano parametri specifici del formato multimediale e comuni a tutti i campioni nella traccia, rappresentati come stringa di byte con codifica esadecimale. Il formato e il significato semantico delle sequenza di byte varia in base al valore del campo **FourCC** come indicato di seguito:

>   * Quando TrackElement descrive un video HEVC, il campo **FourCC** deve essere uguale a **"hev1"** e;

>   Il campo **CodecPrivateData** deve contenere una rappresentazione di stringa con codifica esadecimale della sequenza di byte seguente, specificata in ABNF [[RFC5234]:](http://go.microsoft.com/fwlink/?LinkId=123096) (nessuna modifica da MS-SSTR)

>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField

>   * SPSField contiene SPS (Sequence Parameter Set).

>   * PPSField contiene PPS (Slice Parameter Set).

>   Nota: VPS (Video Parameter Set) non è incluso in CodecPrivateData, ma deve essere contenuto nell'intestazione dei file archiviati nel riquadro 'hvcC'. I sistemi che usano il protocollo Smooth Streaming devono segnalare i parametri aggiuntivi di decodifica (ad esempio, HEVC Tier) con l'attributo personalizzato "codecs".

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   Il campo **SmoothStreamingMedia's MajorVersion** deve essere impostato su 2 e il campo **MinorVersion** campo deve essere impostato su 2. (Nessuna modifica)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 Richiesta frammento 

>   **Nota**: il formato multimediale predefinito richiesto per **MinorVersion** 2 e 'hev1' è il marchio 'iso8', ISO Base Media File Format, specificato in [ISO/IEC 14496-12] ISO Base Media File Format Fourth Edition e [ISO/IEC 23001-7] Common Encryption Second Edition.

### <a name="224-fragment-response"></a>2.2.4 Risposta frammento 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** è deprecato e la relativa funzione è stata sostituita da Track Fragment Decode Time Box ('tfdt') specificato in [ISO/IEC 14496-12] sezione 8.8.12.

>   **Nota**: un client può calcolare la durata di un frammento sommando le durate dei campioni elencate in Track Run Box ('trun') o moltiplicando il numero di campioni per la durata predefinita del campione. BaseMediaDecodeTime 'tfdt' sommato alla durata del frammento è uguale al parametro URL time per il frammento successivo.

>   Producer Reference Time Box ('prft') deve essere inserito prima di Movie Fragment Box ('moof') in base alle esigenze, per indicare l'ora UTC corrispondente a Track Fragment Decode Time del primo campione a cui fa riferimento Movie Fragment Box, come specificato in [ISO/IEC 14496-12] sezione 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** è deprecato e la relativa funzione è stata sostituita da Track Fragment Decode Time Box ('tfdt') specificato in [ISO/IEC 14496-12] sezione 8.8.12.

>   **Nota**: un client può calcolare la durata di un frammento sommando le durate dei campioni elencate in Track Run Box ('trun') o moltiplicando il numero di campioni per la durata predefinita del campione. BaseMediaDecodeTime 'tfdt' sommato alla durata del frammento è uguale al parametro URL time per il frammento successivo. Gli indirizzi lookahead sono deprecati perché ritardano lo streaming live.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** e i campi correlati incapsulano i valori predefiniti per i metadati campione presenti nel frammento. La sintassi del campo **TfhdBox** è un subset limitato della sintassi di Track Fragment Header Box definita in [[ISO/IEC-14496-12]](http://go.microsoft.com/fwlink/?LinkId=183695) sezione 8.8.7.

>   **BaseDataOffset (8 byte):** offset, in byte, dall'inizio del campo **MdatBox** al campo campione nel campo **MdatBox**. Per segnalare questa limitazione, è necessario impostare il flag default-base-is-moof (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** e i campi correlati incapsulano i valori per i metadati campione del frammento richiesto. La sintassi di **TrunBox** è un subset limitato di Version 1 Track Fragment Run Box definito in [[ISO/IEC-14496-](http://go.microsoft.com/fwlink/?LinkId=183695)*12]* sezione 8.8.8.

>   **SampleCompositionTimeOffset (4 byte):** offset dell'ora di composizione di ogni campione regolato in modo che l'ora di presentazione del primo campione presentato nel frammento sia uguale all'ora di decodifica del primo campione decodificato. Devono essere usati offset di composizione campioni video negativi,

>   come definito in [[ISO/IEC-14496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

>   Nota: in questo modo si evita un errore di sincronizzazione video causato dal ritardo audio uguale al ritardo di rimozione massimo del buffer delle immagini decodificate e gestisce i tempi di presentazione tra i frammenti alternativi che possono avere ritardi di rimozione diversi.

>   La sintassi dei campi definiti in questa sezione, specificata in ABNF [[RFC5234]](http://go.microsoft.com/fwlink/?LinkId=123096) rimane invariata, ad eccezione di quanto segue:

>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Campi comuni di risposta frammento 

### <a name="225-sparse-stream-pointer"></a>2.2.5 Puntatore di flusso di tipo sparse 

### <a name="226-fragment-not-yet-available"></a>2.2.6 Frammento non ancora disponibile 

### <a name="227-live-ingest"></a>2.2.7 Inserimento live 

#### <a name="2271-filetype"></a>2.2.7.1 FileType 

>   **FileType (variabile):** specifica il sottotipo e l'utilizzo previsto del file MPEG-4 ([[MPEG-4-RA])](http://go.microsoft.com/fwlink/?LinkId=327787) e degli attributi di alto livello.

>   **MajorBrand (variabile):** marchio principale del file multimediale. Deve essere impostato su "isml".

>   **MinorVersion (variabile):** versione secondaria del file multimediale. Deve essere impostato su 1.

>   **CompatibleBrands (variabile):** specifica i marchi supportati di MPEG-4.
>   Deve includere "ccff" e "iso8".

>   La sintassi dei campi definiti in questa sezione, specificata in ABNF [[RFC5234]](http://go.microsoft.com/fwlink/?LinkId=123096) è la seguente:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Nota**: i marchi di compatibilità 'ccff' e 'iso8' indicano che i frammenti sono conformi a "Common Container File Format", Common Encryption [ISO/IEC 23001 7] e ISO Base Media File Format Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 Frammento 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Intestazione estesa del frammento di traccia 

### <a name="228-server-to-server-ingest"></a>2.2.8 Inserimento da server a server 

# <a name="3-protocol-details"></a>3 Dettagli del protocollo 


## <a name="31-client-details"></a>3.1 Dettagli del client 

### <a name="311-abstract-data-model"></a>3.1.1 Modello di dati astratto 

#### <a name="3111-presentation-description"></a>3.1.1.1 Presentation Description 

>   L'elemento dati Presentation Description incapsula tutti i metadati per la presentazione.

>   Presentation Metadata: set di metadati comuni a tutti i flussi nella presentazione. Presentation Metadata comprende i campi seguenti, specificati nella sezione *2.2.2.1*:

>   * **MajorVersion**
>   * **MinorVersion**
>   * **TimeScale**
>   * **Duration**
>   * **IsLive**
>   * **LookaheadCount**
>   * **DVRWindowLength**

>   Le presentazioni con i flussi HEVC devono contenere le impostazioni seguenti:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Nota: riquadri deprecati)

>   Le presentazioni devono contenere anche l'impostazione seguente:

    TimeScale = 90000

>   Stream Collection: raccolta di elementi dati Stream Description, come specificato nella sezione *3.1.1.1.2*.

>   Protection Description: raccolta di elementi dati Protection System Metadata Description, come specificato nella sezione *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Protection System Metadata Description 

>   L'elemento dati Protection System Metadata Description incapsula i metadati specifici di un singolo sistema di protezione contenuto. (Nessuna modifica)

>   Protection Header Description: metadati di protezione contenuto che si riferiscono a un singolo sistema di protezione contenuto. Protection Header Description comprende i campi seguenti, specificati nella sezione *2.2.2.2*:

>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Stream Description 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Track Description 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Custom Attribute Description 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 Fragment Reference Description 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Track-Specific Fragment Reference Description 

#### <a name="3112-fragment-description"></a>3.1.1.2 Fragment Description 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Sample Description 

### <a name="312-timers"></a>3.1.2 Timer 

### <a name="313-initialization"></a>3.1.3 Inizializzazione 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Eventi attivati di livello più alto 

#### <a name="3141-open-presentation"></a>3.1.4.1 Open Presentation 

#### <a name="3142-get-fragment"></a>3.1.4.2 Get Fragment 

#### <a name="3143-close-presentation"></a>3.1.4.3 Close Presentation 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 Elaborazione eventi e regole di sequenziazione 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 Richiesta manifesto e risposta manifesto 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 Richiesta frammento e risposta frammento

## <a name="32-server-details"></a>3.2 Dettagli del server

## <a name="33-live-encoder-details"></a>3.3 Dettagli del codificatore live 

# <a name="4-protocol-examples"></a>4 Esempi di protocollo 

# <a name="5-security"></a>5 Sicurezza 

## <a name="51-security-considerations-for-implementers"></a>5.1 Considerazioni sulla sicurezza per i responsabili dell'implementazione 
-----------------------------------------

>   Se il contenuto trasportato mediante questo protocollo ha un elevato valore commerciale, è consigliabile usare un sistema di protezione contenuto per evitare l'uso non autorizzato del contenuto. È possibile usare **ProtectionElement** per trasportare i metadati correlati all'uso di un sistema di protezione contenuto. Il contenuto audio e video protetto deve essere crittografato come specificato da MPEG Common Encryption Second Edition: 2015 [ISO/IEC 23001-7].

>   **Nota**: per i video HEVC vengono crittografati solo i dati di sezione in VCL NAL. Le intestazioni di sezione e altri NAL sono accessibili alle applicazioni di presentazione prima della decrittografia. In un percorso video sicuro le informazioni crittografate non sono disponibili per le applicazioni di presentazione.

# <a name="52-index-of-security-parameters"></a>5.2 Indice dei parametri di sicurezza 
-----------------------------


| **Parametro di sicurezza**  | **Sezione**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Riquadri di Common Encryption | *[ISO/IEC 23001-7]* |

# <a name="53-common-encryption-boxes"></a>5.3 Riquadri di Common Encryption
-----------------------

I riquadri seguenti possono essere presenti nelle risposte frammento quando viene applicato Common Encryption e sono specificati in [ISO/IEC 23001-7] o [ISO/IEC 14496-12]:

1.  Protection System Specific Header Box ('pssh')

2.  Sample Encryption Box ('senc')

3.  Sample Auxiliary Information Offset Box ('saio')

4.  Sample Auxiliary Information Size Box ('saiz')

5.  Sample Group Description Box ('sgpd')

6.  Sample to Group Box ('sbgp')

-----------------------

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
