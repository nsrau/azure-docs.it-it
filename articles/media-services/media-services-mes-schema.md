---
title: Schema di Media Encoder Standard | Microsoft Docs
description: Questo argomento fornisce una panoramica dello schema di Media Encoder Standard.
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 4c060062-8ef2-41d9-834e-e81e8eafcf2e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: f8e6e1da776d680d48737ecb5ac7b9319901f121
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017


---
# <a name="media-encoder-standard-schema"></a>Schema di Media Encoder Standard
In questo argomento vengono descritti alcuni elementi e tipi di schema XML su cui si basa il [set di impostazioni di Media Encoder Standard](media-services-mes-presets-overview.md). L'argomento spiega gli elementi e i valori possibili per ognuno. Lo schema completo verrà pubblicato in un secondo momento.  

## <a name="Preset"></a> Set di impostazioni (elemento radice)
Definisce un set di impostazioni per la codifica.  

### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Encoding** |[Encoding](media-services-mes-schema.md#Encoding) |Elemento radice, indica che le origini dell'input devono essere codificate. |
| **Outputs** |[Outputs](media-services-mes-schema.md#Output) |Raccolta dei file dell'output desiderato. |

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Versione**<br/><br/> Obbligatorio |**xs: decimal** |Versione predefinita. Valgono le limitazioni seguenti: xs:fractionDigits value="1" e xs:minInclusive value="1" Ad esempio, **version="1.0"**. |

## <a name="Encoding"></a> Encoding
Contiene una sequenza degli elementi seguenti.  

### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Impostazioni per la codifica video H.264. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Impostazioni per la codifica audio AAC. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Impostazioni per le immagini .bmp. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Impostazioni per le immagini .png. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Impostazioni per le immagini .jpg. |

## <a name="H264Video"></a> H264Video
### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:boolean** |Al momento è supportata solo la codifica in un passaggio. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:time** |Determina la spaziatura fissa tra i frame IDR in unità di secondi. Chiamata anche la durata GOP. Vedere **SceneChangeDetection** (sotto) per controllare se il codificatore può deviare da questo valore. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> default="false" |**xs:boolean** |Se impostato su true, il codificatore tenta di rilevare i cambi scena nel video e inserisce un frame IDR. |
| **Complexity**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**xs:string** |Consente di controllare il compromesso tra qualità video e velocità di codifica. Può corrispondere a uno dei valori seguenti: **Speed**, **Balanced** o **Quality**.<br/><br/> Valore predefinito: **Balanced** |
| **SyncMode**<br/><br/> minOccurs="0" | |La funzionalità sarà disponibile in una delle prossime versioni. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Raccolta di livelli video di output. |

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Condition** |**xs:string** | Quando l'input non ha video, è consigliabile forzare il codificatore per inserire una traccia video monocromatica. A tale scopo, usare Condition="InsertBlackIfNoVideoBottomLayerOnly" (per inserire un video solo alla più bassa velocità in bit) o Condition="InsertBlackIfNoVideo" (per inserire un video a tutte le velocità in bit di output). Per altre informazioni, vedere [questo](media-services-advanced-encoding-with-mes.md#a-idnovideoainsert-a-video-track-when-input-has-no-video) argomento.|

## <a name="H264Layers"></a> H264Layers

Per impostazione predefinita, se si invia al codificatore un input che contiene solo audio e nessun video, l'asset di output conterrà file di soli dati audio. Alcuni lettori non possono gestire flussi di output di questo tipo. È possibile usare l'impostazione dell'attributo **InsertBlackIfNoVideo** di H264Video per forzare l'aggiunta di una traccia video all'output da parte del codificatore. Per altre informazioni, vedere [questo](media-services-advanced-encoding-with-mes.md#a-idnovideoainsert-a-video-track-when-input-has-no-video) argomento.
              
### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Raccolta di livelli H264. |

## <a name="H264Layer"></a> H264Layer
> [!NOTE]
> I limiti video sono basati sui valori descritti nella tabella [Livelli H264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels).  
> 
> 

### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Profilo**<br/><br/> minOccurs="0"<br/><br/> default="Auto" |**xs:string** |Può corrispondere a uno dei valori seguenti **xs:string**: **Auto**, **Baseline**, **Main**, **High**. |
| **Level**<br/><br/> minOccurs="0"<br/><br/> default="Auto" |**xs:string** | |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |La velocità in bit usata per questo livello video, espressa in kbps. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs:int** |La velocità in bit massima usata per questo livello video, espressa in kbps. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs:time** |Lunghezza del buffer video. |
| **Width**<br/><br/> minOccurs="0" |**xs:int** |Larghezza del frame video di output, espressa in pixel.<br/><br/> Al momento è necessario specificare entrambi i valori di larghezza e altezza, che devono essere numeri pari. |
| **Height**<br/><br/> minOccurs="0" |**xs:int** |Altezza del frame video di output, espressa in pixel.<br/><br/> Al momento è necessario specificare entrambi i valori di larghezza e altezza, che devono essere numeri pari.|
| **BFrames**<br/><br/> minOccurs="0" |**xs:int** |Numero di fotogrammi B tra frame di riferimento. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> default=”3” |**xs:int** |Numero di frame di riferimento in un GOP. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> default="Cabac" |**xs:string** |Può corrispondere a uno dei valori seguenti: **Cabac** e **Cavlc**. |
| **FrameRate**<br/><br/> minOccurs="0" |numero razionale |Determina la frequenza dei fotogrammi del video di output. Usare il valore predefinito "0/1" per consentire al codificatore di usare la stessa frequenza dei fotogrammi del video di input. I valori consentiti sono in genere le normali frequenze di fotogrammi video, come illustrato di seguito. Tuttavia, è consentito qualunque numero razionale. Ad esempio 1/1 corrisponderebbe a 1 fps e sarebbe valido.<br/><br/> - 12/1 (12 fps)<br/><br/> - 15/1 (15 fps)<br/><br/> - 24/1 (24 fps)<br/><br/> - 24.000/1.001 (23,976 fps)<br/><br/> - 25/1 (25 fps)<br/><br/>  - 30/1 (30 fps)<br/><br/> - 30.000/1.001 (29,97 fps) <br/> <br/>**NOTA** se si sta creando un set di impostazioni personalizzato per la codifica a bitrate multipli, tutti i livelli del set di impostazioni **devono** usare lo stesso valore di FrameRate.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs:boolean** |Copiare da Azure Media Encoder |
| **Slices**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |Stabilisce il numero di sezioni in cui è suddiviso un frame. È consigliabile usare il valore predefinito. |

## <a name="AACAudio"></a> AACAudio
 Contiene una sequenza degli elementi e dei gruppi seguenti.  

 Per altre informazioni su AAC, vedere [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Profilo**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs:string** |Può corrispondere a uno dei valori seguenti: **AACLC**, **HEAACV1**, or **HEAACV2**. |

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Condition** |**xs:string** |Per forzare la generazione di un asset contenente una traccia audio silenziosa da parte del codificatore quando l'input è privo di audio, specificare il valore "InsertSilenceIfNoAudio".<br/><br/> Per impostazione predefinita, se si invia al codificatore un input che contiene solo video e nessun audio, l'asset di output contiene file di soli dati video. Alcuni lettori non possono gestire flussi di output di questo tipo. In tal caso, è possibile usare questa impostazione per forzare l'aggiunta di una traccia audio silenziosa all'output da parte del codificatore. |

### <a name="groups"></a>Gruppi
| riferimento | Descrizione |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Vedere la descrizione di [AudioGroup](media-services-mes-schema.md#AudioGroup) per conoscere il numero di canali, la frequenza di campionamento e la velocità in bit appropriati che è possibile impostare per ogni profilo. |

## <a name="AudioGroup"></a> AudioGroup
Per altre informazioni sui valori validi per ogni profilo, vedere la tabella "Dettagli sui codec audio" qui di seguito.  

### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Channels**<br/><br/> minOccurs="0" |**xs:int** |Numero dei canali audio codificati. Le opzioni valide sono: 1, 2, 5, 6, 8.<br/><br/> Valore predefinito: 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs:int** |Frequenza di campionamento audio, espressa in Hz. |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |Velocità in bit usata per la codifica audio, specificata in kbps. |

### <a name="audio-codec-details"></a>Dettagli sui codec audio
Codec audio|Dettagli  
-----------------|---  
**AACLC**|1:<br/><br/> - 11025 : 8 &lt;= bitrate &lt; 16<br/><br/> - 12000 : 8 &lt;= bitrate &lt; 16<br/><br/> - 16000 : 8 &lt;= bitrate &lt;32<br/><br/>- 22050 : 24 &lt;= bitrate &lt; 32<br/><br/> - 24000 : 24 &lt;= bitrate &lt; 32<br/><br/> - 32000 : 32 &lt;= bitrate &lt;= 192<br/><br/> - 44100 : 56 &lt;= bitrate &lt;= 288<br/><br/> - 48000 : 56 &lt;= bitrate &lt;= 288<br/><br/> - 88200 : 128 &lt;= bitrate &lt;= 288<br/><br/> - 96000 : 128 &lt;= bitrate &lt;= 288<br/><br/> 2:<br/><br/> - 11025 : 16 &lt;= bitrate &lt; 24<br/><br/> - 12000 : 16 &lt;= bitrate &lt; 24<br/><br/> - 16000 : 16 &lt;= bitrate &lt; 40<br/><br/> - 22050 : 32 &lt;= bitrate &lt; 40<br/><br/> - 24000 : 32 &lt;= bitrate &lt; 40<br/><br/> - 32000 : 40 &lt;= bitrate &lt;= 384<br/><br/> - 44100 : 96 &lt;= bitrate &lt;= 576<br/><br/> - 48000 : 96 &lt;= bitrate &lt;= 576<br/><br/> - 88200 : 256 &lt;= bitrate &lt;= 576<br/><br/> - 96000 : 256 &lt;= bitrate &lt;= 576<br/><br/> 5/6:<br/><br/> - 32000 : 160 &lt;= bitrate &lt;= 896<br/><br/> - 44100 : 240 &lt;= bitrate &lt;= 1024<br/><br/> - 48000 : 240 &lt;= bitrate &lt;= 1024<br/><br/> - 88200 : 640 &lt;= bitrate &lt;= 1024<br/><br/> - 96000 : 640 &lt;= bitrate &lt;= 1024<br/><br/> 8:<br/><br/> - 32000 : 224 &lt;= bitrate &lt;= 1024<br/><br/> - 44100 : 384 &lt;= bitrate &lt;= 1024<br/><br/> - 48000 : 384 &lt;= bitrate &lt;= 1024<br/><br/> - 88200 : 896 &lt;= bitrate &lt;= 1024<br/><br/> - 96000 : 896 &lt;= bitrate &lt;= 1024  
**HEAACV1**|1:<br/><br/> - 22050 : bitrate = 8<br/><br/> - 24000 : 8 &lt;= bitrate &lt;= 10<br/><br/> - 32000 : 12 &lt;= bitrate &lt;= 64<br/><br/> - 44100 : 20 &lt;= bitrate &lt;= 64<br/><br/> - 48000 : 20 &lt;= bitrate &lt;= 64<br/><br/> - 88200 : bitrate = 64<br/><br/> 2:<br/><br/> - 32000 : 16 &lt;= bitrate &lt;= 128<br/><br/> - 44100 : 16 &lt;= bitrate &lt;= 128<br/><br/> - 48000 : 16 &lt;= bitrate &lt;= 128<br/><br/> - 88200 : 96 &lt;= bitrate &lt;= 128<br/><br/> - 96000 : 96 &lt;= bitrate &lt;= 128<br/><br/> 5/6:<br/><br/> - 32000 : 64 &lt;= bitrate &lt;= 320<br/><br/> - 44100 : 64 &lt;= bitrate &lt;= 320<br/><br/> - 48000 : 64 &lt;= bitrate &lt;= 320<br/><br/> - 88200 : 256 &lt;= bitrate &lt;= 320<br/><br/> - 96000 : 256 &lt;= bitrate &lt;= 320<br/><br/> 8:<br/><br/> - 32000 : 96 &lt;= bitrate &lt;= 448<br/><br/> - 44100 : 96 &lt;= bitrate &lt;= 448<br/><br/> - 48000 : 96 &lt;= bitrate &lt;= 448<br/><br/> - 88200 : 384 &lt;= bitrate &lt;= 448<br/><br/> - 96000 : 384 &lt;= bitrate &lt;= 448  
**HEAACV2**|2:<br/><br/> - 22050 : 8 &lt;= bitrate &lt;= 10<br/><br/> - 24000 : 8 &lt;= bitrate &lt;= 10<br/><br/> - 32000 : 12 &lt;= bitrate &lt;= 64<br/><br/> - 44100 : 20 &lt;= bitrate &lt;= 64<br/><br/> - 48000 : 20 &lt;= bitrate &lt;= 64<br/><br/> - 88200 : 64 &lt;= bitrate &lt;= 64  
  

## <a name="Clip"></a> Clip
### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **StartTime** |**xs:duration** |Specifica l'ora di inizio di una presentazione. Il valore di StartTime deve corrispondere ai timestamp assoluti del video di input. Ad esempio, se il primo fotogramma del video di input ha un timestamp di 12:00:10.000, il valore di StartTime deve essere di almeno 12:00:10.000 o superiore. |
| **Duration** |**xs:duration** |Specifica la durata di una presentazione (ad esempio, l'aspetto di una sovrimpressione nel video). |

## <a name="Output"></a> Output
### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **FileName** |**xs:string** |Nome del file di output.<br/><br/> È possibile usare le macro descritte nella tabella seguente per creare i nomi dei file di output. Ad esempio:<br/><br/> **"Outputs": [      {       "FileName": "{Basename}*{Resolution}*{Bitrate}.mp4",       "Format": {         "Type": "MP4Format"       }     }   ]** |

### <a name="macros"></a>Macro
| Macro | Descrizione |
| --- | --- |
| **{Basename}** |Se si esegue la codifica VoD, {Basename} corrisponde ai primi 32 caratteri della proprietà AssetFile.Name del file primario dell'asset di input.<br/><br/> Se l'asset di input è un archivio live, {Basename} è derivato dagli attributi trackName nel manifesto server. Se si invia un processo subclip usando TopBitrate, come in "<VideoStream\>TopBitrate</VideoStream\>", e il file di output contiene video, {Basename} corrisponde ai primi 32 caratteri dell'attributo trackName del livello video con il valore di velocità in bit più alto.<br/><br/> Se invece si invia un processo subclip usando tutte le velocità in bit dell'input, come in "<VideoStream\>*</VideoStream\>", e il file di output contiene video, {Basename} corrisponde ai primi 32 caratteri dell'attributo trackName del livello video corrispondente. |
| **{Codec}** |Esegue il mapping a "H264" per i video e "AAC" per l'audio. |
| **{Bitrate}** |Velocità in bit video di destinazione se il file di output contiene video e audio oppure velocità in bit audio di destinazione se il file di output contiene solo l'audio. Il valore usato è la velocità in bit espressa in kbps. |
| **{Channel}** |Numero di canali audio se il file contiene audio. |
| **{Width}** |Larghezza del video nel file di output, espressa in pixel, se il file contiene video. |
| **{Height}** |Altezza del video nel file di output, espressa in pixel, se il file contiene video. |
| **{Extension}** |Eredita dalla proprietà "Type" per il file di output. Il nome del file di output avrà una delle estensioni seguenti: "mp4", "ts", "jpg", "png" o "bmp". |
| **{Index}** |Obbligatorio per l'anteprima. Deve essere presente solo una volta. |

## <a name="Video"></a> Video (il tipo complesso eredita da Codec)
### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Inizia** |**xs:string** | |
| **Step** |**xs:string** | |
| **Range** |**xs:string** | |
| **PreserveResolutionAfterRotation** |**xs:boolean** |Per informazioni dettagliate, vedere la sezione seguente: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
È consigliabile usare il flag PreserveResolutionAfterRotation insieme ai valori di risoluzione espressi in termini di percentuale (Width="100%", Height="100%").  

Per impostazione predefinita, le impostazioni di risoluzione della codifica (Width, Height) nel set di impostazioni di Media Encoder Standard (MES) sono destinate ai video con zero gradi di rotazione. Ad esempio, se il video di input è 1.280x720 con zero gradi di rotazione, i set di impostazioni assicurano che l'output avrà la stessa risoluzione. Vedere la figura seguente.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Tuttavia, ciò significa che se il video di input è stato acquisito con rotazione diversa da zero (ad esempio nel caso di un tablet o uno smartphone tenuto verticalmente), per impostazione predefinita MES applicherà al video di input le impostazioni di risoluzione della codifica (Width, Height), compensando quindi la rotazione. Vedere l'esempio nell'immagine qui di seguito. Il set di impostazioni usa Width = "100%", Height = "100%", facendo sì che MES interpreti la necessità di un output di 1.280 pixel di larghezza e 720 pixel di altezza. Una volta ruotato il video, l'immagine viene ridotta alle dimensioni della finestra, creando colonne a sinistra e a destra.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Se il codice precedente non corrisponde al comportamento desiderato, è possibile usare il flag PreserveResolutionAfterRotation e impostarlo su "true" (il valore predefinito è "false"). Pertanto se il set di impostazioni ha come valori Width = "100%", Height = "100%" e PreserveResolutionAfterRotation è impostato su "true", un video di input da 1.280 pixel di larghezza e 720 pixel di altezza con 90 gradi di rotazione produrrà un output con zero gradi di rotazione, ma 720 pixel di larghezza e 1.280 pixel di altezza. Vedere la figura seguente.  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="FormatGroup"></a> FormatGroup (gruppo)
### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Elemento
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="PngLayer"></a> PngLayer
### <a name="element"></a>Elemento
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Elemento
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |
| **Quality**<br/><br/> minOccurs="0" |**xs:int** |I valori validi sono 1(worst)-100(best) |

### <a name="attributes"></a>Attributi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a> BmpImage (il tipo complesso eredita da Video)
### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png layers |

## <a name="JpgImage"></a> JpgImage (il tipo complesso eredita da Video)
### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png layers |

## <a name="PngImage"></a> PngImage (il tipo complesso eredita da Video)
### <a name="elements"></a>Elementi
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png layers |

## <a name="examples"></a>esempi
Per osservare esempi di set di impostazioni XML compilati in base a questo schema, vedere [Task Presets for MES (Media Encoder Standard)](media-services-mes-presets-overview.md) (Set di impostazioni delle attività di MES (Media Encoder Standard)).

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


