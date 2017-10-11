---
title: "Eseguire attività di codifica avanzata personalizzando i set di impostazioni di Media Encoder Standard | Documentazione Microsoft"
description: "Questo argomento illustra come eseguire la codifica avanzata personalizzando i set di impostazioni delle attività di Media Encoder Standard."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: juliako
ms.openlocfilehash: 8de3bdd45261c84a0e1bb90f1c58863ad740dd5a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Eseguire attività di codifica avanzata personalizzando i set di impostazioni di Media Encoder Standard 

## <a name="overview"></a>Panoramica

Questo argomento descrive come personalizzare i set d impostazioni di Media Encoder Standard. L'argomento [Codifica avanzata con Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md) mostra come usare .NET per creare un'attività di codifica e un processo per l'esecuzione di tale attività. Dopo aver personalizzato un set di impostazioni, fornire tali impostazioni all'attività di codifica. 

>[!NOTE]
>Se si usa un set di impostazioni XML, assicurarsi di mantenere l'ordine degli elementi, come illustrato negli esempi XML seguenti (KeyFrameInterval, ad esempio, deve precedere SceneChangeDetection).
>

In questo argomento vengono illustrati i set di impostazioni personalizzati che eseguono le attività di codifica seguenti.

## <a name="support-for-relative-sizes"></a>Supporto per le dimensioni relative

Quando si generano anteprime, non è sempre necessario specificare la larghezza e l'altezza dell'output in pixel. È possibile specificare questi valori sotto forma di percentuali nell'intervallo [1%, …, 100%].

### <a name="json-preset"></a>Set di impostazioni JSON
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>Set di impostazioni XML
    <Width>100%</Width>
    <Height>100%</Height>

## <a id="thumbnails"></a>Generare anteprime

Questa sezione illustra come personalizzare un set di impostazioni che genera anteprime. Il set di impostazioni definito di seguito contiene informazioni su come codificare il file, nonché le informazioni necessarie per generare le anteprime. È possibile usare uno dei set di impostazioni per Media Encoder Standard documentati in [questa](media-services-mes-presets-overview.md) sezione e aggiungere il codice che genera le anteprime.  

> [!NOTE]
> L'impostazione **SceneChangeDetection** nell'impostazione predefinita seguente può essere impostata a true solo in caso di codifica in un video a velocità in bit singola. In caso di codifica in video a bitrate multipli e impostazione di **SceneChangeDetection** su true, il codificatore restituisce un errore.  
>
>

Per informazioni sullo schema, vedere [questo](media-services-mes-schema.md) argomento.

Assicurarsi di esaminare la sezione [Considerazioni](#considerations) .

### <a id="json"></a>Set di impostazioni JSON
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"

            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a id="xml"></a>Set di impostazioni XML
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

### <a name="considerations"></a>Considerazioni

Si applicano le considerazioni seguenti:

* L'utilizzo di timestamp espliciti per Inizio/Passaggio/Intervallo presuppone che l'origine dell'input duri almeno 1 minuto.
* Gli elementi Jpg/Png/BmpImage hanno gli attributi inizio, passaggio e intervallo della stringa, che possono essere interpretati come:

  * Se sono numeri interi non negativi, numero di frame, ad esempio "Start": "120",
  * Relativi alla durata di origine se espressi con il suffisso %, ad esempio "Start": "15%", OR
  * Timestamp se espresso come HH:MM:SS come formato, ad esempio "Start": "00:01:00"

    È possibile combinare e associare le notazioni a piacimento.

    Inoltre, Inizio supporta anche una Macro speciale: {Best}, che tenta di determinare il primo fotogramma "interessante" della NOTA contenuto: (Passaggio e Intervallo vengono ignorati quando Inizio è impostato su {Best})
  * Impostazioni predefinite: Start: {Best}
* Il formato di output deve essere specificato in modo esplicito per ogni formato immagine: Jpg/Png/BmpFormat. Quando è presente, MES collega JpgVideo a JpgFormat e così via. OutputFormat presenta una nuova Macro specifica di codec di immagine : {Index}, che deve essere presente (una volta e una sola volta) per i formati immagine.

## <a id="trim_video"></a>Tagliare un video (ritaglio)
Questa sezione descrive la modifica di set di impostazioni del codificatore per tagliare o ritagliare il video di input quando l'input è un file in formato intermedio o su richiesta. Il codificatore può anche essere usato per tagliare o ritagliare un asset acquisito o archiviato da un flusso in tempo reale. Per i relativi dettagli, vedere [questo blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Per tagliare i video, è possibile eseguire uno dei set di impostazioni di Media Encoder Standard documentati in [questa](media-services-mes-presets-overview.md) sezione e modificare l'elemento **Sources** (come illustrato di seguito). Il valore di StartTime deve corrispondere ai timestamp assoluti del video di input. Ad esempio, se il primo fotogramma del video di input ha un timestamp di 12:00:10.000, il valore di StartTime deve essere di almeno 12:00:10.000 o superiore. Nell'esempio seguente, si presuppone che il video di input abbia un timestamp iniziale pari a zero. **Sources** deve essere posizionato all'inizio del set di impostazioni.

### <a id="json"></a>Set di impostazioni JSON
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="xml-preset"></a>Set di impostazioni XML
Per tagliare i video, è possibile eseguire un’impostazione predefinita MES documentata [qui](media-services-mes-presets-overview.md) e modificare l'elemento **Sources** (come illustrato di seguito).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

## <a id="overlay"></a>Creare una sovrimpressione

Il Media Encoder Standard consente di sovrapporre un'immagine a un video esistente. Attualmente, sono supportati i seguenti formati: png, jpg, gif e bmp. Il set di impostazioni definito di seguito è un esempio di base di una sovrimpressione video.

Oltre a definire un file del set di impostazioni, è anche necessario indicare a Servizi multimediali quale file dell'asset corrisponde all'immagine da sovrapporre e quale file contiene il video di origine sul quale sovrapporre l'immagine. Il file video deve essere il file **primario** .

Se si usa .NET, aggiungere le due funzioni seguenti all'esempio .NET definito in [questo](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) argomento. La funzione **UploadMediaFilesFromFolder** carica i file, ad esempio BigBuckBunny.mp4 e Image001.png, da una cartella e imposta il file con estensione mp4 come file primario dell'asset. La funzione **EncodeWithOverlay** usa il file di set di impostazioni personalizzato passato alla funzione stessa, ad esempio il set di impostazioni seguente, per creare l'attività di codifica.


    static public IAsset UploadMediaFilesFromFolder(string folderPath)
    {
        IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
    
        foreach (var af in asset.AssetFiles)
        {
            // The following code assumes 
            // you have an input folder with one MP4 and one overlay image file.
            if (af.Name.Contains(".mp4"))
                af.IsPrimary = true;
            else
                af.IsPrimary = false;
    
            af.Update();
        }
    
        return asset;
    }

    static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("Media Encoder Standard Job");
        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(customPresetFileName);

        // Create a task
        ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input assets to be encoded.
        // This asset contains a source file and an overlay file.
        task.InputAssets.Add(assetSource);

        // Add an output asset to contain the results of the job. 
        task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }


> [!NOTE]
> Limitazioni correnti:
>
> L'impostazione di opacità della sovrimpressione non è supportata.
>
> Il file video di origine e il file dell'immagine sovrapposta devono essere nello stesso asset e il file video deve essere impostato come file primario nell'asset.
>
>

### <a name="json-preset"></a>Set di impostazioni JSON
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a name="xml-preset"></a>Set di impostazioni XML
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


## <a id="silent_audio"></a>Inserire una traccia audio silenziosa quando l'input è privo di audio
Per impostazione predefinita, se si invia al codificatore un input che contiene solo video e nessun audio, l'asset di output contiene file di soli dati video. Alcuni lettori non possono gestire flussi di output di questo tipo. In tal caso, è possibile usare questa impostazione per forzare l'aggiunta di una traccia audio silenziosa all'output da parte del codificatore.

Per forzare la generazione di un asset contenente una traccia audio silenziosa da parte del codificatore quando l'input è privo di audio, specificare il valore "InsertSilenceIfNoAudio".

È possibile usare uno dei set di impostazioni di Media Encoder Standard documentati in [questa](media-services-mes-presets-overview.md) sezione e apportare la modifica seguente:

### <a name="json-preset"></a>Set di impostazioni JSON
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>Set di impostazioni XML
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a id="deinterlacing"></a>Disabilitare il deinterlacciamento automatico
I clienti non devono eseguire alcuna operazione se desiderano che il contenuto interlacciato sia automaticamente deinterlacciato. Quando il deinterlacciamento automatico è attivato (impostazione predefinita) il MES rileva automaticamente i fotogrammi interlacciati e deinterlaccia solo i fotogrammi contrassegnati come interlacciati.

È possibile disattivare il deinterlacciamento automatico. Questa opzione non è consigliata.

### <a name="json-preset"></a>Set di impostazioni JSON
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>Set di impostazioni XML
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a id="audio_only"></a>Impostazioni predefinite solo audio
In questa sezione vengono illustrate due impostazioni predefinite MES solo audio: Audio AAC e Audio AAC di buona qualità.

### <a name="aac-audio"></a>Audio ACC
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="aac-good-quality-audio"></a>Audio AAC di buona qualità
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="concatenate"></a>Concatenare due o più file video

Nell'esempio seguente viene illustrato come generare un set di impostazioni per concatenare due o più file video. Lo scenario più comune è l'aggiunta di un'intestazione o una sequenza finale al video principale. L'uso previsto sono i file video modificati insieme che condividono proprietà: risoluzione video, frequenza dei fotogrammi, conteggio tracce audio e così via. Prestare attenzione a non combinare video con frequenze dei fotogrammi diverse o con un numero diverso di tracce audio.

>[!NOTE]
>La struttura corrente della funzionalità di concatenazione prevede che i clip video siano coerenti in termini di risoluzione, frequenza dei fotogrammi e così via. 

### <a name="requirements-and-considerations"></a>Problemi e considerazioni

* I video di input devono contenere solo una traccia audio.
* Tutti i video di input devono avere la stessa frequenza dei fotogrammi.
* È necessario caricare i video in asset separati e impostare i video come file primario in ogni asset.
* È necessario conoscere la durata dei video.
* Il seguente esempio di set di impostazioni presuppone che tutti i video di input inizino con un timestamp pari a zero. È necessario modificare i valori StartTime se i video hanno un timestamp iniziale diverso, come avviene in genere con gli archivi in tempo reale.
* Il set di impostazioni JSON fa riferimenti espliciti ai valori AssetID degli asset di input.
* Il codice di esempio presuppone che il set di impostazioni JSON sia stato salvato in un file locale, ad esempio "C:\supportFiles\preset.json". Presuppone anche che siano stati creati due asset caricando due file video e che si conoscano i valori AssetID risultanti.
* Il frammento di codice e il set di impostazioni JSON mostrano un esempio di concatenazione di due file video. È possibile estendere la concatenazione a più di due video nel modo seguente:

  1. Chiamando ripetutamente task.InputAssets.Add() per aggiungere più video in ordine.
  2. Effettuando le modifiche corrispondenti all'elemento "Sources" nel file JSON, aggiungendo altre voci nello stesso ordine.

### <a name="net-code"></a>Codice .NET

    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job.
    // This output is specified as AssetCreationOptions.None, which
    // means the output asset is not encrypted.
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

### <a name="json-preset"></a>Set di impostazioni JSON

Aggiornare il set di impostazioni personalizzate con gli ID degli asset che si vuole concatenare e l'intervallo di tempo appropriato per ogni video.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="crop"></a>Ritagliare video con Media Encoder Standard
Vedere l'argomento [Ritagliare video con Media Encoder Standard](media-services-crop-video.md) .

## <a id="no_video"></a>Inserire una traccia video quando l'input non ha video

Per impostazione predefinita, se si invia al codificatore un input che contiene solo audio e nessun video, l'asset di output contiene file di soli dati audio. Alcuni lettori, tra cui Azure Media Player (vedere [qui](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) potrebbero non essere in grado di gestire tali flussi. In tal caso, è possibile usare questa impostazione per forzare l'aggiunta di una traccia video monocromatica all'output da parte del codificatore.

> [!NOTE]
> Forzando il codificatore a inserire una traccia video di output si accresce la dimensione dell'asset di output e perciò il costo sostenuto per l'attività di codifica. È necessario eseguire test per verificare che questo incremento abbia un impatto modesto sugli addebiti mensili.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Inserimento di video alla sola velocità in bit più bassa

Si supponga di usare un'impostazione di codifica a bitrate multipli, ad esempio ["H264 bitrate multipli 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) per codificare l'intero catalogo di input per lo streaming, che contiene una combinazione di file video e file di solo audio. In questo scenario, quando l'input non ha video, è opportuno forzare il codificatore a inserire una traccia video monocromatica solo alla velocità in bit più bassa, anziché inserire il video a tutte le velocità in bit. A tale scopo è necessario usare il flag **InsertBlackIfNoVideoBottomLayerOnly**.

È possibile usare uno dei set di impostazioni di Media Encoder Standard documentati in [questa](media-services-mes-presets-overview.md) sezione e apportare la modifica seguente:

#### <a name="json-preset"></a>Set di impostazioni JSON
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Set di impostazioni XML

Se si usa XML, applicare Condition="InsertBlackIfNoVideoBottomLayerOnly" come attributo per l'elemento **H264Video** e Condition="InsertSilenceIfNoAudio" come attributo per l'elemento **AACAudio**.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Inserimento di video a tutte le velocità in bit di output
Si supponga di usare un'impostazione di codifica a bitrate multipli, ad esempio ["H264 bitrate multipli 720p"](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) per codificare l'intero catalogo di input per lo streaming, che contiene una combinazione di file video e file di solo audio. In questo scenario, quando l'input non ha video, è opportuno forzare il codificatore a inserire una traccia video monocromatica a tutte le velocità in bit di output. In questo modo gli asset di output saranno tutti omogenei rispetto al numero di tracce video e tracce audio. A tale scopo è necessario specificare il flag "InsertBlackIfNoVideo".

È possibile usare uno dei set di impostazioni di Media Encoder Standard documentati in [questa](media-services-mes-presets-overview.md) sezione e apportare la modifica seguente:

#### <a name="json-preset"></a>Set di impostazioni JSON
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Set di impostazioni XML

Se si usa XML, applicare Condition="InsertBlackIfNoVideo" come attributo per l'elemento **H264Video** e Condition="InsertSilenceIfNoAudio" come attributo per l'elemento **AACAudio**.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a id="rotate_video"></a>Ruotare un video
Il [codificatore multimediale standard](media-services-dotnet-encode-with-media-encoder-standard.md) supporta la rotazione in base ad angoli di 0/90/180/270. Il comportamento predefinito è "Auto", che tenta di rilevare i metadati di rotazione nel file video in arrivo per la compensazione. Includere l'elemento **Sources** seguente in uno dei set di impostazioni definiti in [questa](media-services-mes-presets-overview.md) sezione:

### <a name="json-preset"></a>Set di impostazioni JSON
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...
### <a name="xml-preset"></a>Set di impostazioni XML
    <Sources>
           <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Per altre informazioni sul modo in cui il codificatore interpreta le impostazioni di larghezza e altezza nei set di impostazioni quando è attivata la compensazione di rotazione, vedere [questo](media-services-mes-schema.md#PreserveResolutionAfterRotation) argomento.

È possibile usare il valore "0" per indicare al codificatore di ignorare i metadati di rotazione, se presenti, nel video di input.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Vedere anche
[Panoramica sulla codifica dei servizi multimediali](media-services-encode-asset.md)
