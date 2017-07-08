---
title: Schema dei metadati di input dei Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento fornisce una panoramica dello schema di input dei Servizi multimediali di Azure.
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: d72848e2-4b65-4c84-94bc-e2a90a6e7f47
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 21cbbb10065df9ae9c63b775a6526ea9c4f92136
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="input-metadata"></a>Metadati di input
Un processo di codifica è associato uno (o più) asset di input in cui si desidera eseguire alcune attività di codifica.  Al termine di un'attività, viene generato un asset di output.  L'asset di output contiene video, audio, anteprime, manifest e così via. L'asset di output include anche un file contenente i metadati dell'asset di input. Il nome del file XML dei metadati ha il seguente formato: &lt;asset_id&gt;_metadata.xml (ad esempio, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), dove &lt;asset_id&gt; è il valore AssetId dell'asset di input.  

Se si desidera esaminare il file di metadati, è possibile creare un localizzatore **SAS** e scaricare il file nel computer locale. È possibile trovare un esempio su come creare un localizzatore SAS e scaricare un file tramite le [estensioni dell'SDK .NET di Servizi multimediali](media-services-dotnet-get-started.md).  

In questo argomento vengono descritti gli elementi e i tipi di schema XML su cui si basato i metadati di input (&lt;asset_id&gt;_metadata.xml).  Per informazioni sul file contenente i metadati sull'asset di output, vedere [Output Metadata](media-services-output-metadata-schema.md) (Metadati di output).  

> [!NOTE]
> Il [codice schema](media-services-input-metadata-schema.md#code) completo e un [esempio di codice XML](media-services-input-metadata-schema.md#xml) sono disponibili alla fine di questo argomento.  
> 
> 

## <a name="AssetFiles"></a> Elemento radice AssetFile
Contiene una raccolta di [elementi AssetFile](media-services-input-metadata-schema.md#AssetFile) per il processo di codifica.  

Vedere un esempio di codice XML alla fine di questo argomento: [esempio XML](media-services-input-metadata-schema.md#xml).  

| Nome | Descrizione |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs="1" maxOccurs="unbounded" |Un singolo elemento figlio. Per altre informazioni, vedere [Elemento AssetFile](media-services-input-metadata-schema.md#AssetFile). |

## <a name="AssetFile"></a> Elemento AssetFile
 Contiene gli attributi e gli elementi che descrivono un file di asset.  

 Vedere un esempio di codice XML alla fine di questo argomento: [esempio XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Nome**<br /><br /> Obbligatorio |**xs:string** |Nome del file di asset |
| **Dimensione**<br /><br /> Obbligatorio |**xs:long** |Dimensioni del file di asset in byte. |
| **Duration**<br /><br /> Obbligatorio |**xs:duration** |Durata della riproduzione del contenuto. Esempio: Duration="PT25M37.757S". |
| **NumberOfStreams**<br /><br /> Obbligatorio |**xs:int** |Numero di flussi nel file di asset. |
| **FormatNames**<br /><br /> Obbligatorio |**xs:string** |Nomi del formato. |
| **FormatVerboseNames**<br /><br /> Obbligatorio |**xs:string** |Nomi dettagliati del formato. |
| **StartTime** |**xs:duration** |Ora di inizio del contenuto. Esempio: StartTime="PT2.669S". |
| **OverallBitRate** |**xs:int** |Media della velocità in bit del file di asset, espressa in kbps. |

> [!NOTE]
> I seguenti 4 elementi figlio devono comparire in una sequenza.  
> 
> 

### <a name="child-elements"></a>Elementi figlio
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Programs**<br /><br /> minOccurs="0" | |Raccolta di tutti gli [Elementi Programs](media-services-input-metadata-schema.md#Programs) quando il file di asset è in formato MPEG-TS. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Ogni file di asset fisico può contenere da zero a più tracce video con interfoliazione in un formato contenitore appropriato. Questo elemento contiene una raccolta di tutti gli [elementi VideoTrack](media-services-input-metadata-schema.md#VideoTracks) che fanno parte del file di asset. |
| **AudioTrack**<br /><br /> minOccurs="0" | |Ogni file di asset fisico può contenere da zero a più tracce audio con interfoliazione in un formato contenitore appropriato. Questo elemento contiene una raccolta di tutti gli [elementi AudioTrack](media-services-input-metadata-schema.md#AudioTracks) che fanno parte del file di asset. |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Metadati del file di asset rappresentati come stringhe chiave-valore. ad esempio:<br /><br /> **&lt;Metadata key="language" value="eng" /&gt;** |

## <a name="TrackType"></a> TrackType
Vedere un esempio di codice XML alla fine di questo argomento: [esempio XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Id**<br /><br /> Obbligatorio |**xs:int** |Indice in base zero della traccia audio o video.<br /><br /> Non corrisponde necessariamente al TrackID usato in un file MP4. |
| **Codec** |**xs:string** |Stringa del codec della traccia video. |
| **CodecLongName** |**xs:string** |Nome lungo del codec della traccia audio o video. |
| **TimeBase**<br /><br /> Obbligatorio |**xs:string** |Tempo base. Esempio: TimeBase="1/48000" |
| **NumberOfFrames** |**xs:int** |Numero di frame (presenti per le tracce video). |
| **StartTime** |**xs:duration** |Ora di inizio della traccia. Esempio: StartTime="PT2.669S" |
| **Duration** |**xs:duration** |Durata della traccia. Esempio: Duration="PTSampleFormat M37.757S". |

> [!NOTE]
> I seguenti 2 elementi figlio devono comparire in una sequenza.  
> 
> 

### <a name="child-elements"></a>Elementi figlio
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Disposition**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Contiene informazioni di presentazione (ad esempio, se una determinata traccia audio è per utenti con problemi di vista). |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Stringhe chiave-valore generiche che possono essere usate per contenere una varietà di informazioni. Esempio, key="language" e value="eng". |

## <a name="AudioTrackType"></a> AudioTrackType (eredita da TrackType)
 **AudioTrackType** è un tipo globale complesso che eredita da [TrackType](media-services-input-metadata-schema.md#TrackType).  

 Il tipo rappresenta una specifica traccia audio nel file di asset.  

 Vedere un esempio di codice XML alla fine di questo argomento: [esempio XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **SampleFormat** |**xs:string** |Formato del campione. |
| **ChannelLayout** |**xs:string** |Layout del canale. |
| **Channels**<br /><br /> Obbligatorio |**xs:int** |Numero di canali audio (da 0 in su). |
| **SamplingRate**<br /><br /> Obbligatorio |**xs:int** |Frequenza di campionamento dell'audio in campioni/sec o Hz. |
| **Bitrate** |**xs:int** |Velocità media in bit audio in bit al secondo, calcolata in base al file di asset. Viene contato solo il payload del flusso elementare, mentre l'overhead di creazione dei pacchetti è escluso dal conteggio. |
| **BitsPerSample** |**xs:int** |Tipo di bit per campione per il formato wFormatTag. |

## <a name="VideoTrackType"></a> VideoTrackType (eredita da TrackType)
**AudioTrackType** è un tipo globale complesso che eredita da [TrackType](media-services-input-metadata-schema.md#TrackType).  

Il tipo rappresenta una specifica traccia video nel file di asset.  

Vedere un esempio di codice XML alla fine di questo argomento: [esempio XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **FourCC**<br /><br /> Obbligatorio |**xs:string** |Codice FourCC del codec video. |
| **Profilo** |**xs:string** |Profilo della traccia video. |
| **Level** |**xs:string** |Livello della traccia video. |
| **PixelFormat** |**xs:string** |Formato pixel della traccia video. |
| **Width**<br /><br /> Obbligatorio |**xs:int** |Larghezza del video codificata in pixel. |
| **Height**<br /><br /> Obbligatorio |**xs:int** |Altezza del video codificata in pixel. |
| **DisplayAspectRatioNumerator**<br /><br /> Obbligatorio |**xs:double** |Numeratore delle proporzioni della visualizzazione video. |
| **DisplayAspectRatioDenominator**<br /><br /> Obbligatorio |**xs:double** |Denominatore delle proporzioni della visualizzazione video. |
| **DisplayAspectRatioDenominator**<br /><br /> Obbligatorio |**xs:double** |Numeratore delle proporzioni del campione video. |
| **SampleAspectRatioNumerator** |**xs:double** |Numeratore delle proporzioni del campione video. |
| **SampleAspectRatioNumerator** |**xs:double** |Denominatore delle proporzioni del campione video. |
| **FrameRate**<br /><br /> Obbligatorio |**xs: decimal** |Frequenza dei frame misurata in formato .3F. |
| **Bitrate** |**xs:int** |Velocità media in bit video in kilobit al secondo, calcolata in base al file di asset. Viene contato solo il payload del flusso elementare, mentre l'overhead di creazione dei pacchetti è escluso. |
| **MaxGOPBitrate** |**xs:int** |Velocità media in bit Max GOP per la traccia video, in kilobit al secondo. |
| **HasBFrames** |**xs:int** |Numero di traccia video dei fotogrammi B. |

## <a name="MetadataType"></a> MetadataType
**MetadataType** è un tipo globale complesso che descrive i metadati di un file di asset come stringhe chiave-valore. Esempio, key="language" e value="eng".  

Vedere un esempio di codice XML alla fine di questo argomento: [esempio XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **key**<br /><br /> Obbligatorio |**xs:string** |La chiave nella coppia chiave-valore. |
| **value**<br /><br /> Obbligatorio |**xs:string** |Il valore nella coppia chiave-valore. |

## <a name="ProgramType"></a> ProgramType
**ProgramType** è un tipo globale complesso che descrive un programma.  

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **ProgramId**<br /><br /> Obbligatorio |**xs:int** |ID programma |
| **NumberOfPrograms**<br /><br /> Obbligatorio |**xs:int** |Numero di programmi. |
| **PmtPid**<br /><br /> Obbligatorio |**xs:int** |Tabelle di mappa dei programmi (PMT) che contengono informazioni sui programmi.  Per altre informazioni, vedere [PMT](http://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Obbligatorio |**xs:int** |Usato dal decodificatore. Per altre informazioni, vedere [PCR](http://en.wikipedia.org/wiki/MPEG_transport_stream#PCR). |
| **StartPTS** |**xs: long** |Timestamp di avvio della presentazione. |
| **EndPTS** |**xs: long** |Timestamp di fine della presentazione. |

## <a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** è un tipo globale complesso che descrive il flusso.  

Vedere un esempio di codice XML alla fine di questo argomento: [esempio XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Default**<br /><br /> Obbligatorio |**xs:int** |Impostare questo attributo su 1 per indicare che si tratta della presentazione predefinita. |
| **Dub**<br /><br /> Obbligatorio |**xs:int** |Impostare questo attributo su 1 per indicare che si tratta della presentazione doppiata. |
| **Original**<br /><br /> Obbligatorio |**xs:int** |Impostare questo attributo su 1 per indicare che si tratta della presentazione originale. |
| **Comment**<br /><br /> Obbligatorio |**xs:int** |Impostare questo attributo su 1 per indicare che questa traccia contiene commenti. |
| **Lyrics**<br /><br /> Obbligatorio |**xs:int** |Impostare questo attributo su 1 per indicare che questa traccia contiene testi. |
| **Karaoke**<br /><br /> Obbligatorio |**xs:int** |Impostare questo attributo su 1 per indicare che si tratta della traccia karaoke (musica di sottofondo, senza cantato). |
| **Forced**<br /><br /> Obbligatorio |**xs:int** |Impostare questo attributo su 1 per indicare che si tratta della presentazione forzata. |
| **HearingImpaired**<br /><br /> Obbligatorio |**xs:int** |Impostare questo attributo su 1 per indicare che questa traccia è destinata agli utenti con problemi di udito. |
| **VisualImpaired**<br /><br /> Obbligatorio |**xs:int** |Impostare questo attributo su 1 per indicare che questa traccia è destinata agli utenti con problemi di vista. |
| **CleanEffects**<br /><br /> Obbligatorio |**xs:int** |Impostare questo attributo su 1 per indicare che questa traccia contiene effetti clean. |
| **AttachedPic**<br /><br /> Obbligatorio |**xs:int** |Impostare questo attributo su 1 per indicare che questa traccia contiene foto. |

## <a name="Programs"></a> Elemento Programs
Elemento wrapper contenente più elementi **Program**.  

### <a name="child-elements"></a>Elementi figlio
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Per i file di asset in formato MPEG-TS, contiene informazioni sui programmi nel file di asset. |

## <a name="VideoTracks"></a> Elemento VideoTracks
 Elemento wrapper contenente più elementi **VideoTrack**.  

 Vedere un esempio di codice XML alla fine di questo argomento: [esempio XML](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Elementi figlio
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (eredita da TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Contiene informazioni sulle tracce video presenti nel file di asset. |

## <a name="AudioTracks"></a> Elemento AudioTracks
 Elemento wrapper contenente più elementi **AudioTrack**.  

 Vedere un esempio di codice XML alla fine di questo argomento: [Esempio XML](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Elementi figlio
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (eredita da TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Contiene informazioni sulle tracce audio presenti nel file di asset. |

## <a name="code"></a> Codice schema
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               elementFormDefault="qualified">  

      <xs:complexType name="MetadataType">  
        <xs:attribute name="key"   type="xs:string" use="required"/>  
        <xs:attribute name="value" type="xs:string" use="required"/>  
      </xs:complexType>  

      <xs:complexType name="ProgramType">  
        <xs:attribute name="ProgramId" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Program Id</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Number of programs</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PmtPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pmt pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PcrPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pcr pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="StartPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>start pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="EndPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>end pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="StreamDispositionType">  
        <xs:attribute name="Default"          type="xs:int" use="required" />  
        <xs:attribute name="Dub"              type="xs:int" use="required" />  
        <xs:attribute name="Original"         type="xs:int" use="required" />  
        <xs:attribute name="Comment"          type="xs:int" use="required" />  
        <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
        <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
        <xs:attribute name="Forced"           type="xs:int" use="required" />  
        <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
        <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
        <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
        <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
      </xs:complexType>  

      <xs:complexType name="TrackType" abstract="true">  
        <xs:sequence>  
          <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
          <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
        </xs:sequence>  
        <xs:attribute name="Id" use="required">  
          <xs:annotation>  
            <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Codec" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec string</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="CodecLongName" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec long name</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="TimeBase"  type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfFrames">  
          <xs:annotation>  
            <xs:documentation>number of frames</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="StartTime" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Duration" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="VideoTrackType">  
        <xs:annotation>  
          <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="FourCC" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>video codec FourCC code</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Profile" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>profile</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Level" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>level</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="PixelFormat" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>Video track's pixel format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Width" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video width in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Height" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video height in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioNumerator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioDenominator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="FrameRate" use="required">  
              <xs:annotation>  
                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:decimal">  
                  <xs:minInclusive value="0"/>  
                  <xs:fractionDigits value="3"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="MaxGOPBitrate">  
              <xs:annotation>  
                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="HasBFrames" type="xs:int">  
              <xs:annotation>  
                <xs:documentation>video track number of B frames</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:complexType name="AudioTrackType">  
        <xs:annotation>  
          <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="SampleFormat"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>sample format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="ChannelLayout"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>channel layout</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Channels" use="required">  
              <xs:annotation>  
                <xs:documentation>number of audio channels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SamplingRate" use="required">  
              <xs:annotation>  
                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="BitsPerSample">  
              <xs:annotation>  
                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Programs" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" type="xs:duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
                  <xs:annotation>  
                    <xs:documentation>number of streams in asset file</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatNames" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format verbose names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="StartTime" type="xs:duration">  
                  <xs:annotation>  
                    <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="OverallBitRate">  
                  <xs:annotation>  
                    <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:int">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  


## <a name="xml"></a> Esempio XML
Di seguito è riportato un esempio di file di metadati di input.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
      <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
        <VideoTracks>  
          <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
          </VideoTrack>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
          </AudioTrack>  
        </AudioTracks>  
        <Metadata key="major_brand" value="mp42" />  
        <Metadata key="minor_version" value="0" />  
        <Metadata key="compatible_brands" value="mp42mp41" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
        <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


