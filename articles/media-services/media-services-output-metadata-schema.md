---
title: Schema dei metadati di output dei Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento fornisce una panoramica dello schema di output dei Servizi multimediali di Azure.
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.openlocfilehash: c8792535eeeb71e7233c42bd9ea2a446a1c4d43c
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="output-metadata"></a>Metadati di output
## <a name="overview"></a>Panoramica
Un processo di codifica è associato uno (o più) asset di input in cui si desidera eseguire alcune attività di codifica. Ad esempio, codificare un file MP4 in set di velocità in bit adattivi MP4 H.264; creare un'anteprima; creare le sovrimpressioni. Al termine di un'attività, viene generato un asset di output.  L'asset di output contiene video, audio, anteprime e così via. L'asset di output include anche un file contenente i metadati dell'asset di output. Il nome del file XML dei metadati ha il seguente formato: &lt;nome_file_origine&gt;_manifest.xml (ad esempio BigBuckBunny_manifest.xml).  

Se si desidera esaminare il file di metadati, è possibile creare un localizzatore **SAS** e scaricare il file nel computer locale.  

Questo articolo descrive gli elementi e i tipi di schema XML su cui si basato i metadati di output (&lt;nome_file_origine&gt;_manifest.xml). Per informazioni sul file contenente i metadati sull'asset di input, vedere [Input Metadata](media-services-input-metadata-schema.md) (Metadati di input).  

> [!NOTE]
> Il codice schema completo e un esempio di codice XML sono disponibili alla fine di questo articolo.  
>
>

## <a name="AssetFiles "></a> Elemento radice AssetFiles
Raccolta di elementi AssetFile per il processo di codifica.  

### <a name="child-elements"></a>Elementi figlio
| Nome | Descrizione |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs="0" maxOccurs="1" |Un [elemento AssetFile](media-services-output-metadata-schema.md) incluso nella raccolta AssetFiles. |

## <a name="AssetFile "></a> Elemento AssetFile
Consultare l'[esempio di codice XML](media-services-output-metadata-schema.md#xml) disponibile.  

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Nome**<br/><br/> Obbligatorio |**xs:string** |Il nome dell'asset di file multimediale. |
| **Dimensione**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:long** |Dimensioni del file di asset in byte. |
| **Duration**<br/><br/> Obbligatorio |**xs:duration** |Durata della riproduzione del contenuto. |

### <a name="child-elements"></a>Elementi figlio
| Nome | Descrizione |
| --- | --- |
| **Sources** |Raccolta di file multimediali di input/origine elaborata per produrre questo AssetFile. Per altre informazioni, vedere [Elemento di origine](media-services-output-metadata-schema.md). |
| **VideoTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Ogni elemento AssetFile fisico può contenere da zero a più tracce video con interfoliazione in un formato contenitore appropriato. Per altre informazioni, vedere [Elemento VideoTracks](media-services-output-metadata-schema.md). |
| **AudioTrack**<br/><br/> minOccurs="0" maxOccurs="1" |Ogni elemento AssetFile fisico può contenere da zero a più tracce audio con interfoliazione in un formato contenitore appropriato. Questa è la raccolta di tutte queste tracce audio. Per altre informazioni, vedere [Elemento AudioTracks](media-services-output-metadata-schema.md). |

## <a name="Sources "></a> Elemento Sources
Raccolta di file multimediali di input/origine elaborata per produrre questo AssetFile.  

Consultare l'[esempio di codice XML](media-services-output-metadata-schema.md#xml) disponibile.  

### <a name="child-elements"></a>Elementi figlio
| Nome | Descrizione |
| --- | --- |
| **Origine**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Un file di input/origine usato durante la generazione di questo asset. Per altre informazioni, vedere [Elemento di origine](media-services-output-metadata-schema.md). |

## <a name="Source "></a> Elemento di origine
Un file di input/origine usato durante la generazione di questo asset.  

Consultare l'[esempio di codice XML](media-services-output-metadata-schema.md#xml) disponibile.  

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Nome**<br/><br/> Obbligatorio |**xs:string** |Nome di file di origine di input. |

## <a name="VideoTracks "></a> Elemento VideoTracks
Ogni elemento AssetFile fisico può contenere da zero a più tracce video con interfoliazione in un formato contenitore appropriato. L'elemento **VideoTracks** rappresenta una raccolta di tutte le tracce video.  

Consultare l'[esempio di codice XML](media-services-output-metadata-schema.md#xml) disponibile.  

### <a name="child-elements"></a>Elementi figlio
| Nome | Descrizione |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Una specifica traccia video nell'elemento AssetFile padre. Per altre informazioni, vedere [Elemento VideoTracks](media-services-output-metadata-schema.md#VideoTrack). |

## <a name="VideoTrack"></a> Elemento VideoTrack
Una specifica traccia video nell'elemento AssetFile padre.  

Consultare l'[esempio di codice XML](media-services-output-metadata-schema.md#xml) disponibile.  

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:int** |Indice in base zero della traccia video. **Nota:** questo **ID** non corrisponde necessariamente al TrackID usato in un file MP4. |
| **FourCC**<br/><br/> Obbligatorio |**xs:string** |Codice FourCC del codec video. |
| **Profilo** |**xs:string** |Profilo H264 (applicabile solo al codec H264). |
| **Level** |**xs:string** |Livello H264 (applicabile solo al codec H264). |
| **Width**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:int** |Larghezza del video codificata in pixel. |
| **Height**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:int** |Altezza del video codificata in pixel. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:double** |Numeratore delle proporzioni della visualizzazione video. |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:double** |Denominatore delle proporzioni della visualizzazione video. |
| **Framerate**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs: decimal** |Frequenza dei frame misurata in formato .3F. |
| **TargetFramerate**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs: decimal** |Frequenza dei frame preimpostata misurata in formato .3F. |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:int** |Velocità media in bit video in kilobit al secondo, calcolata in base all'elemento AssetFile. Conta solo il payload del flusso elementare e non include il sovraccarico dovuto alla creazione di pacchetti. |
| **TargetBitrate**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:int** |Velocità media in bit mirata per la traccia video, come da set di impostazioni di codifica, in kilobit al secondo. |
| **MaxGOPBitrate**<br/><br/> minInclusive ="0" |**xs:int** |Velocità media in bit Max GOP per la traccia video, in kilobit al secondo. |

## <a name="AudioTracks "></a> Elemento AudioTrack
Ogni elemento AssetFile fisico può contenere da zero a più tracce audio con interfoliazione in un formato contenitore appropriato. L'elemento **AudioTracks** rappresenta una raccolta di tutte le tracce audio.  

Consultare l'[esempio di codice XML](media-services-output-metadata-schema.md#xml) disponibile.  

### <a name="child-elements"></a>Elementi figlio
| Nome | Descrizione |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Una specifica traccia audio nell'elemento AssetFile padre. Per altre informazioni, vedere [Elemento AudioTrack](media-services-output-metadata-schema.md). |

## <a name="AudioTrack "></a> Elemento AudioTrack
Una specifica traccia audio nell'elemento AssetFile padre.  

Consultare l'[esempio di codice XML](media-services-output-metadata-schema.md#xml) disponibile.  

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:int** |Indice in base zero della traccia audio. **Nota:** non corrisponde necessariamente al TrackID usato in un file MP4. |
| **Codec** |**xs:string** |Stringa del codec della traccia audio. |
| **EncoderVersion** |**xs:string** |Stringa facoltativa della versione del codificatore obbligatoria per EAC3. |
| **Channels**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:int** |Numero dei canali audio. |
| **SamplingRate**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:int** |Frequenza di campionamento dell'audio in campioni/sec o Hz. |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:int** |Velocità media in bit audio in bit al secondo, calcolata in base all'elemento AssetFile. Conta solo il payload del flusso elementare e non include il sovraccarico dovuto alla creazione di pacchetti. |
| **BitsPerSample**<br/><br/> minInclusive ="0"<br/><br/> Obbligatorio |**xs:int** |Tipo di bit per campione per il formato wFormatTag. |

### <a name="child-elements"></a>Elementi figlio
| Nome | Descrizione |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs="0" maxOccurs="1" |Parametri di risultato della misurazione della sonorità. Per altre informazioni, vedere [Elemento LoudnessMeteringResultParameters](media-services-output-metadata-schema.md). |

## <a name="LoudnessMeteringResultParameters "></a> Elemento LoudnessMeteringResultParameters
Parametri di risultato della misurazione della sonorità.  

Consultare l'[esempio di codice XML](media-services-output-metadata-schema.md#xml) disponibile.  

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs:string** |Versione del kit **Dolby** per lo sviluppo della misurazione professionale della sonorità (DPLM). |
| **DialogNormalization**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> Obbligatorio |**xs:int** |DialogNormalization generato tramite DPLM, obbligatorio quando è impostato LoudnessMetering. |
| **IntegratedLoudness**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> Obbligatorio |**xs:float** |Sonorità integrata |
| **IntegratedLoudnessUnit**<br/><br/> Obbligatorio |**xs:string** |Unità integrata della sonorità. |
| **IntegratedLoudnessGatingMethod**<br/><br/> Obbligatorio |**xs:string** |Identificatore limite. |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive ="0" maxInclusive="100" |**xs:float** |Contenuto vocale presente nel programma, in percentuale. |
| **SamplePeak**<br/><br/> Obbligatorio |**xs:float** |Valore di campionamento assoluto di picco per canale, dall'ultimo ripristino o dall'ultima cancellazione.  Le unità sono espresse in dBFS. |
| **SamplePeakUnit**<br/><br/> fixed="dBFS"<br/><br/> Obbligatorio |**xs:anySimpleType** |Unità di picco di esempio. |
| **TruePeak**<br/><br/> Obbligatorio |**xs:float** |Valore massimo di picco effettivo per canale, come da ITU-R BS.1770-2, dall'ultimo ripristino o dall'ultima cancellazione. Le unità sono espresse in dBFS. |
| **TruePeakUnit**<br/><br/> fixed="dBTP"<br/><br/> Obbligatorio |**xs:anySimpleType** |Unità di picco effettivo. |

## <a name="schema-code"></a>Codice schema
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               elementFormDefault="qualified">  
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
                  <xs:element name="Sources">  
                    <xs:annotation>  
                      <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:attribute name="Name" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>input source file name</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
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
                            <xs:attribute name="FourCC" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>video codec FourCC code</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Profile" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Level" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
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
                            <xs:attribute name="Framerate" use="required">  
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
                            <xs:attribute name="TargetFramerate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:decimal">  
                                  <xs:minInclusive value="0"/>  
                                  <xs:fractionDigits value="3"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="TargetBitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
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
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:sequence>  
                              <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                                <xs:annotation>  
                                  <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                                </xs:annotation>  
                                <xs:complexType>  
                                  <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="DialogNormalization" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:int">  
                                        <xs:minInclusive value="-31"/>  
                                        <xs:maxInclusive value="-1"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudness" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation>Integrated loudness</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="-70"/>  
                                        <xs:maxInclusive value="10"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Gating identifier</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                    <xs:annotation>  
                                      <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="0"/>  
                                        <xs:maxInclusive value="100"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                                  </xs:attribute>  
                                </xs:complexType>  
                              </xs:element>  
                            </xs:sequence>  
                            <xs:attribute name="Id" use="required">  
                              <xs:annotation>  
                                <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Codec" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>audio track codec string</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="EncoderVersion" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
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
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="BitsPerSample" use="required">  
                              <xs:annotation>  
                                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
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
                <xs:attribute name="Duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:duration"/>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  



## <a name="xml"></a> Esempio XML

L'XML seguente è un esempio di file di metadati di output.  

    <AssetFiles xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema"   
                xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
      <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
